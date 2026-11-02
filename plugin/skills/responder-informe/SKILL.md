---
name: responder-informe
description: Revisa épicas de Jira PROJ buscando Informes UAT y comentarios nuevos, analiza cada punto reportado (bug, consulta, no es error, FDA, mejora) y redacta y publica la respuesta de QA. Usar con "hay informes nuevos?", "analizá/respondé el informe PROJ-XXXX".
---

# Seguimiento y respuesta de Informes UAT

Esta skill hace tres cosas, que se pueden pedir juntas o por separado:
- **A. Detectar novedades:** "revisá los informes de la épica X", "hay informes nuevos?"
- **B. Analizar un informe:** "analizá el informe PROJ-XXXX"
- **C. Responder un informe:** "respondé el informe PROJ-XXXX" (si no hay análisis previo en la conversación, hacer B primero)

## Acceso a Jira

Si está disponible la skill `jira-base` (plugin qa-jira-toolkit), leerla primero. Si no: el Jira es https://tu-sitio.atlassian.net (proyecto PROJ). Se lee y escribe con la API REST v2/v3 desde una pestaña del navegador con sesión iniciada en ese sitio, usando `fetch`:

```js
const H = {'Content-Type':'application/json','Accept':'application/json','X-Atlassian-Token':'no-check'};
// Usuario QA que usa la skill (para excluir sus propios comentarios):
const yo = await fetch('/rest/api/3/myself',{headers:H}).then(r=>r.json()); // yo.accountId
// Búsqueda: POST /rest/api/3/search/jql  body {jql, fields:[...], maxResults}
// Comentarios: GET /rest/api/3/issue/KEY/comment
// Adjuntos: fields.attachment[].content (descargar con fetch -> blob para ver las capturas)
```

## A. Detectar novedades

Entradas: épicas a revisar (las indica el usuario) y ventana de tiempo (desde la fecha que indique; si no dice, todo lo abierto).

- **Informes nuevos:** Tareas hijas de las épicas con "informe" en el título, creadas dentro de la ventana.
- **Comentarios nuevos:** en Informes abiertos de esas épicas **y en sus subtareas** (ej. "Coordinación y seguimiento"), creados dentro de la ventana.
- Avisar **todo comentario o adjunto nuevo de cualquier persona distinta del usuario QA** (`yo.accountId`): negocio, funcional, Dev, PM u otro QA. Indicar autor, fecha/hora, ticket (link) y resumen en 1-3 líneas.
- Si el comentario reporta comportamiento o errores → analizarlo (sección B). Si es coordinación, una pregunta o una respuesta → solo resumir y sugerir respuesta si se pide.
- **Historias con casos de prueba modificadas:** en las mismas épicas, si una Historia con subtareas TEST tuvo cambios en `description` dentro de la ventana (changelog, por otra persona), resumir qué cambió y qué CP podrían verse afectados. No editar los CP: solo avisar.
- Si no hay novedades, decirlo en una línea.

## B. Analizar cada reporte

1. Leer el texto completo, los tickets citados y **ver las capturas adjuntas**.
2. Identificar la UH involucrada (link citado, pantalla o título del informe) y leer su descripción vigente, CA, tablas y reglas.
3. **Revisar toda la épica antes de concluir:** las demás UH (pueden definir la misma pantalla o regla), Errores abiertos y cerrados, FDA, mejoras e Informes anteriores con respuestas sobre el mismo tema. Si existe documentación técnica del sistema (doc. técnica del formulario, specs), consultarla.
4. No duplicar: si ya hay un Error que lo cubre, es "Ya reportado".
5. Clasificar cada punto:

| Situación | Clasificación |
|---|---|
| La UH define claramente el comportamiento y el sistema hace otra cosa | **Bug** |
| La UH no define el caso, es ambigua o se contradice | **Consulta al funcional** |
| El sistema hace lo que define la UH, aunque al usuario no le guste | **No es error** |
| Lo pedido **existe en el sistema actual** pero no está en ninguna UH de la épica | **FDA** |
| Lo pedido **no existe en el sistema actual** (funcionalidad nueva) | **Mejora** |
| Ya existe un Error que lo cubre | **Ya reportado (PROJ-XXXX)**; si estaba cerrado y vuelve a pasar → Bug (reapertura) |
| No se entiende qué pasó, falta captura, datos o pasos | **Falta información** |
| Dato de prueba mal cargado o usuario sin permisos | **No es error** de desarrollo; sugerir corregir el dato |

- Solo sugerir **Mejora** si no existe en el sistema actual. Si no hay evidencia, marcar "FDA o Mejora - a confirmar si existe en el sistema actual" y redactar la pregunta.
- Citar siempre la fuente ("CA05", "Regla clave 3", "Doc. técnica - sección X"). Si la clasificación depende de una suposición, decirlo.

6. Presentar el resultado:

| # | Punto reportado (resumen) | UH | Qué dice la UH / doc. técnica | Clasificación | Acción sugerida |
|---|---|---|---|---|---|

Después de la tabla:
- **Bug:** borrador del bug (con la skill `reportar-bug` si está disponible). Informe "en TEST": ambiente `UAT`, título `TEST | USER | ...`; "en PROD": ambiente `PROD`, título `PROD | ...`. Prioridad Alta. Se enlaza **solo a la UH (Blocks) y al Informe (Relates)**, sin tocar casos de prueba. **No crearlo sin OK.**
- **FDA:** qué hace hoy el sistema actual (y la fuente) y qué falta en la UH.
- **Mejora:** redactada como pedido nuevo.
- **Consulta al funcional:** pregunta lista para enviar.
- **No es error:** respuesta sugerida citando el CA o la regla.

## C. Responder el Informe

1. Leer el Informe y el/los comentarios a responder (los últimos de personas distintas del usuario QA, salvo que indique otro). Respetar la numeración de puntos de quien reportó.
2. Usar el análisis de B (hacerlo si no existe, revisando toda la épica) y cruzar cada punto con lo hecho: bugs creados o existentes, historias nuevas o FDA, mejoras, consultas pendientes.
3. Redactar agrupando puntos con el mismo destino, en este orden:
   1. Bugs creados: "Según lo reportado en los puntos *5 y 6* se creó el bug [PROJ-XXXX|link]".
   2. Nuevas historias/FdA creadas.
   3. No es error: "El punto *2*: según la UH [PROJ-XXXX|link], el funcionamiento actual es correcto". Agregar "se tomaría como mejora" solo si **no existe en el sistema actual**; si existe en el sistema actual y no está en la UH, es **FDA**.
   4. Consultas al usuario (no reproducible, falta info, confirmar criterio): pregunta concreta.
   5. Ya resuelto / validado OK, con evidencia si hay.
   6. Puntos derivados al funcional o mejoras, citando el texto del usuario resumido.
   Cierre: "Quedamos a su disposición para cualquier duda o consulta. ¡Muchas gracias!"
4. Arrancar con saludo y mención a quien reportó (`[~accountid:<id>]`).
   **Menciones fijas al final (opcional):** si el equipo definió una lista fija de personas a mencionar en todas las respuestas (accountIds que el usuario indica en el chat o en su configuración), agregarla después del cierre, con una línea en blanco antes:
   ```
   [~accountid:<id1>] [~accountid:<id2>] ...
   ```
   Si quien reportó está en esa lista, no repetirla. Mencionar a otras personas solo si el usuario lo pide.
5. Mostrar el borrador completo y esperar OK. El usuario puede editar puntos.
6. Publicar como **respuesta en hilo del comentario del usuario** (no como comentario suelto). Los comentarios hijos tienen `parentId` = id del comentario original:
   ```js
   // id del comentario a responder: el del usuario (GET /rest/api/3/issue/KEY/comment)
   const r = await fetch('/rest/api/2/issue/PROJ-INFORME/comment', {method:'POST', headers:H,
     body: JSON.stringify({ body: wiki, parentId: ID_COMENTARIO_USUARIO })}).then(r=>r.json());
   // Verificar: GET /rest/api/3/issue/PROJ-INFORME/comment/<r.id> debe devolver parentId = ID_COMENTARIO_USUARIO
   ```
   - Si el comentario a responder ya es una respuesta (tiene `parentId`), responder en ese mismo hilo usando el `parentId` del hilo.
   - **Respaldo:** si Jira rechaza el `parentId` (no se crea nada), publicar un comentario nuevo con la mención a quien comentó (`[~accountid:<id>]`) y avisar que no se pudo responder en hilo. Si se creó pero quedó suelta (sin `parentId`), no publicar otra: agregarle la mención si falta y avisar. Nunca borrar comentarios.
   - Confirmar con el link: `https://tu-sitio.atlassian.net/browse/KEY?focusedCommentId=<id>`.

## Reglas

- Siempre revisar la épica completa antes de clasificar o responder.
- Nunca crear bugs, editar tickets ni publicar sin OK del usuario.
- Nunca afirmar que algo se corrigió o se creó si no se verificó en Jira.
- Tono cordial y profesional, frases cortas, sin tecnicismos innecesarios para negocio.
- Citar siempre la UH o el bug con link cuando se afirma algo.
- Si el equipo usa menciones fijas, no olvidarlas al final, también en el comentario de respaldo.
---
name: responder-informe
description: Redacta la respuesta de QA a un Informe UAT de Jira PROJ punto por punto (bugs creados, no es error con la UH que lo respalda, consultas, mejoras, pedidos de más información), con el estilo del equipo, y la publica como comentario tras validación. Usar cuando el usuario diga "respondé el informe PROJ-XXXX", "armá la respuesta a los puntos del informe", "contestale al negocio", o después de analizar un informe con seguimiento-informes.
---

# Responder Informe UAT

Leer primero la skill `jira-base`.

## Flujo

1. Leer el Informe y el/los comentarios a responder (los últimos de personas distintas de la usuaria QA, salvo que indique otro). Identificar la numeración de puntos que usó quien reportó y respetarla.
2. Si no existe un análisis previo en la conversación, hacerlo con la skill `seguimiento-informes` (clasificación por punto).
3. Cruzar cada punto con lo hecho: bugs creados o existentes (enlaces del Informe y Errores de la épica), historias nuevas (FdA), consultas pendientes.
4. Redactar la respuesta agrupando puntos con el mismo destino, en este orden:
   1. Bugs creados: "Según lo reportado en los puntos *5 y 6* se creó el bug [PROJ-XXXX|link]".
   2. Nuevas historias/FdA creadas.
   3. No es error: "El punto *2*: según la UH [PROJ-XXXX|link], el funcionamiento actual es correcto; se tomaría como mejora".
   4. Consultas al usuario (no reproducible, falta info, confirmar criterio): pregunta concreta.
   5. Ya resuelto / validado OK, con evidencia si hay.
   6. Puntos derivados al funcional o mejoras, citando el texto del usuario de forma resumida.
   Cierre: "Quedamos a su disposición para cualquier duda o consulta. ¡Muchas gracias!"
5. Arrancar con saludo y mención a quien reportó (`[~accountid:<id>]`), igual que en los informes del equipo. Mencionar a otras personas solo si el usuario lo pide.
6. Mostrar el borrador completo y esperar OK. El usuario puede editar puntos.
7. Publicar como comentario en el Informe (API v2, formato wiki). Confirmar con el link.

## Reglas

- Nunca afirmar que algo se corrigió o se creó si no se verificó en Jira.
- Tono cordial y profesional, frases cortas, sin tecnicismos innecesarios para negocio.
- Citar siempre la UH o el bug con link cuando se afirma algo.
