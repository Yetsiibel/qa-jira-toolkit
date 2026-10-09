# Vinculación del bug con CP, estados y comentarios

## 1. Detectar los CP afectados

Varios CP pueden estar "En espera" por errores distintos: el estado NO alcanza. Decidir por contenido.

1. Leer todas las subtareas TEST de la UH (`summary`, `description`, `status`, `issuelinks`, `comment`).
2. Para cada CP, tomar la **evidencia de QA**: comentarios de la usuaria QA que contienen "Prueba NO OK" y el panel "Evidencia:" (texto después de "(i)"). Formato habitual:
   ```
   {panel:bgColor=#ffebe6} Prueba NO OK  Ambiente TEST {panel}
   {panel:bgColor=#eae6ff} Evidencia: (i) <descripción del error> !captura! {panel}
   ```
   Si hay varios comentarios NO OK, usar el más reciente (y considerar los anteriores).
3. Comparar contra el bug (título, resultado obtenido, resultado esperado) mirando: campo/sección/elemento de pantalla nombrado, el síntoma (ej. "solo muestra el código", "color incorrecto", "no se abre"), los valores concretos (estado 13, permiso 634, textos entre comillas) y el resultado esperado del CP.
4. Clasificar cada CP:
   - **Alta** (vincular): la evidencia describe el mismo síntoma sobre el mismo elemento.
   - **Media** (preguntar): mismo elemento o sección pero síntoma distinto o evidencia vaga; o CP sin comentario NO OK cuyo resultado esperado cubre exactamente lo que falla.
   - **Descartar**: síntoma distinto, o ya tiene enlace "is blocked by" a otro Error que cubre ese síntoma, o el CP está Cerrado/OK sin NO OK.
5. Mostrar en el borrador: `CP NN (KEY) · estado · coincidencia Alta/Media · frase de evidencia que coincide` y la lista de descartados con motivo. Nunca vincular "Media" sin confirmación explícita.

## 2. Snippets (API v2, desde el navegador integrado)

```js
const H = {'Content-Type':'application/json','X-Atlassian-Token':'no-check'};

// CP de la UH con comentarios y links
const uh = await fetch('/rest/api/2/issue/PROJ-UH?fields=subtasks,status').then(r=>r.json());
const cps = [];
for (const s of uh.fields.subtasks.filter(s=>s.fields.issuetype.name==='TEST')) {
  const x = await fetch(`/rest/api/2/issue/${s.key}?fields=summary,description,status,issuelinks,comment`).then(r=>r.json());
  const ev = x.fields.comment.comments.filter(c=>/Prueba NO OK/i.test(c.body)).map(c=>({fecha:c.created, txt:c.body}));
  cps.push({key:s.key, summary:x.fields.summary, status:x.fields.status.name, evidencia:ev,
    bloqueadoPor:x.fields.issuelinks.filter(l=>l.inwardIssue && l.type.name==='Blocks').map(l=>l.inwardIssue.key)});
}

// Transicionar por NOMBRE de estado destino (no hardcodear ids)
const transicionar = async (key, destino) => {
  const st = (await fetch(`/rest/api/2/issue/${key}?fields=status`).then(r=>r.json())).fields.status.name;
  if (st.toLowerCase() === destino.toLowerCase()) return `${key}: ya estaba en ${st}`;
  const t = (await fetch(`/rest/api/2/issue/${key}/transitions`).then(r=>r.json())).transitions
              .find(t=>t.to.name.toLowerCase()===destino.toLowerCase());
  if (!t) return `${key}: sin transición de "${st}" a "${destino}" (no se cambió)`;
  const r = await fetch(`/rest/api/2/issue/${key}/transitions`,{method:'POST',headers:H,body:JSON.stringify({transition:{id:t.id}})});
  return `${key}: ${st} → ${destino} (${r.status})`;
};
// UH:  await transicionar('PROJ-UH', 'Bloqueado por BUG')
// CP:  await transicionar('PROJ-CP', 'En espera')

// Enlace Error bloquea X (verificar sentido después)
const bloquea = (bug, key) => fetch('/rest/api/2/issueLink',{method:'POST',headers:H,
  body:JSON.stringify({type:{name:'Blocks'}, inwardIssue:{key:bug}, outwardIssue:{key}})});

// Comentario
const comentar = (key, body) => fetch(`/rest/api/2/issue/${key}/comment`,{method:'POST',headers:H,body:JSON.stringify({body})});
```

Verificación: leer `issuelinks` del Error; cada CP/UH debe aparecer como `outwardIssue` con `type.outward = "blocks"`. Si quedó invertido, borrar (`DELETE /rest/api/2/issueLink/{id}`) y recrear intercambiando.

## 3. Formato de comentarios

En cada CP:
```
{panel:bgColor=#fffae6}
(!) Bloqueado por el bug [PROJ-XXXX|https://tu-sitio.atlassian.net/browse/PROJ-XXXX] - <título corto del bug>
{panel}
```

En la UH:
```
{panel:bgColor=#fffae6}
(!) Bloqueado por el bug [PROJ-XXXX|https://tu-sitio.atlassian.net/browse/PROJ-XXXX] - <título corto del bug>
CP afectados: [PROJ-AAAA|https://tu-sitio.atlassian.net/browse/PROJ-AAAA], [PROJ-BBBB|https://tu-sitio.atlassian.net/browse/PROJ-BBBB]
{panel}
```
No mencionar personas en estos comentarios salvo que el usuario lo pida.
