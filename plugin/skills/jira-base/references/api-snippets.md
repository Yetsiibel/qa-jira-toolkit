# Snippets de API (ejecutar con Claude_Browser__javascript_tool en una pestaña de tu-sitio.atlassian.net)

## Helpers comunes

```js
const H = {'Content-Type':'application/json','X-Atlassian-Token':'no-check'};
const get  = k => fetch(`/rest/api/2/issue/${k}?expand=names`).then(r=>r.json());
const put  = (k, fields) => fetch(`/rest/api/2/issue/${k}`, {method:'PUT', headers:H, body:JSON.stringify({fields})}).then(r=>r.status);
const post = fields => fetch('/rest/api/2/issue', {method:'POST', headers:H, body:JSON.stringify({fields})}).then(r=>r.json());
const jql  = (q, fields='summary,status,issuetype,parent,created,updated') =>
  fetch(`/rest/api/3/search/jql?jql=${encodeURIComponent(q)}&maxResults=100&fields=${fields}`).then(r=>r.json());
```

## Leer una UH completa

```js
const r = await fetch('/rest/api/2/issue/PROJ-XXXX?fields=summary,status,description,subtasks,issuelinks,parent,components,customfield_<GERENCIA_1>,customfield_<GERENCIA_2>').then(r=>r.json());
```
Las subtareas TEST aparecen en `fields.subtasks` con `fields.issuetype.name === 'TEST'`. Leer cada una con `?fields=summary,description`.

## Historial de cambios de la descripción

```js
let start=0, all=[];
while(true){ const c = await fetch(`/rest/api/2/issue/PROJ-XXXX/changelog?startAt=${start}&maxResults=100`).then(r=>r.json());
  all = all.concat(c.values||[]); if(c.isLast!==false || !(c.values||[]).length) break; start += c.values.length; }
const desc = all.filter(h=>h.items.some(i=>i.field==='description'))
  .map(h=>({created:h.created, author:h.author?.displayName, from:h.items.find(i=>i.field==='description').fromString, to:h.items.find(i=>i.field==='description').toString}));
```
Para comparar, hacer un diff por líneas (LCS) entre `from` de la primera edición del período y `to` de la última. Si los CP se crearon antes, comparar contra la versión vigente a la fecha de creación del CP más antiguo.

## Crear subtarea TEST

```js
await post({ project:{key:'PROJ'}, parent:{key:'PROJ-UH'}, issuetype:{id:'<ID_TIPO_TEST>'},
  summary:'CP 01 - PROJ-UH - Título', description: wiki,
  components: uh.fields.components.filter(c=>/^Desarrollo/i.test(c.name)).map(c=>({id:c.id})),  // solo Desarrollo, nunca Discovery
  labels:['QA'], priority:{id:'3'},
  customfield_<AREA_SOLICITANTE>:[{id:'<ID_AREA>'}], customfield_<GERENCIA_1>: uh.fields.customfield_<GERENCIA_1> && {id:uh.fields.customfield_<GERENCIA_1>.id},
  customfield_<GERENCIA_2>: uh.fields.customfield_<GERENCIA_2> && {id:uh.fields.customfield_<GERENCIA_2>.id} });
```

## Crear Error y vincularlo como bloqueante de la UH

```js
const bug = await post({ project:{key:'PROJ'}, parent:{key:'PROJ-EPICA'}, issuetype:{id:'<ID_TIPO_ERROR>'},
  summary, description: wiki, components:[{id:'<ID_FRONTEND>'}],   // <ID_FRONTEND> FRONTEND / <ID_BACKEND> BACKEND
  labels:['criterio-de-aceptacion'], priority:{id:'3'}, customfield_<AMBIENTE_DETECTADO>:{value:'TEST'},
  customfield_<AREA_SOLICITANTE>:[{id:'<ID_AREA>'}] });
await fetch('/rest/api/2/issueLink', {method:'POST', headers:H, body:JSON.stringify({
  type:{name:'Blocks'}, inwardIssue:{key:bug.key}, outwardIssue:{key:'PROJ-UH'} })});
// Verificar SIEMPRE el sentido: leer el Error y confirmar que en issuelinks figura
// type.outward "blocks" con outwardIssue = la UH (en la UH debe verse "is blocked by" el Error).
const chk = await fetch(`/rest/api/2/issue/${bug.key}?fields=issuelinks`).then(r=>r.json());
// Si quedó invertido: DELETE /rest/api/2/issueLink/{id} y recrear intercambiando inwardIssue/outwardIssue.
```
Si además hay CP fallidos, vincularlos igual (`inwardIssue` = CP).

## Informes UAT y comentarios nuevos en épicas

```js
const epicas = ['PROJ-1000','PROJ-2000'];
const desde = '-65m';  // ventana de la revisión
const nuevos = await jql(`parent in (${epicas.join(',')}) AND summary ~ "informe" AND created >= ${desde}`);
const conComentarios = await jql(`parent in (${epicas.join(',')}) AND summary ~ "informe" AND updated >= ${desde}`, 'summary,comment,attachment,parent,status');
// filtrar comentarios con created dentro de la ventana
```

## Adjuntos (capturas)

`fields.attachment[]` trae `id`, `filename` y `content`. Navegar directo a la URL del adjunto suele fallar; usar este método probado:

```js
const b = await fetch('/rest/api/2/attachment/content/<ID>').then(r=>r.blob());
document.body.innerHTML = `<img src="${URL.createObjectURL(b)}" style="max-width:100%;background:#fff">`;
```
Luego `Claude_Browser__computer {action:'screenshot', scale:0.6}` (si da timeout, reintentar una vez). Al terminar, volver a navegar a `/browse/<KEY>`.

Las imágenes de comentarios aparecen como `!image-xxxx.png|...!` y corresponden al adjunto con ese nombre. Los videos (.mp4) no se pueden ver: analizar con el texto y decirlo.
