# Canal de aviso

Destino: **Microsoft Teams**, en un canal propio de cada usuario (ej. equipo privado "Avisos QA", canal General), además de la notificación al celular/email de la tarea programada. El bot de Power Automate NO puede publicar en el chat personal ("Usted"): da BadRequest. Usar un canal.

Se avisa ante cualquier comentario o adjunto nuevo de otra persona en los Informes seguidos (ver SKILL.md). Agrupar todas las novedades de una ejecución en un solo mensaje. No enviar mensajes cuando no hay novedades.

## Webhook (personal de cada usuario)

Cada usuario crea su propio flujo y su URL **no se guarda en el plugin** (funciona como una contraseña):

1. Teams → crear equipo privado "Avisos QA" (solo el usuario).
2. Canal General → ⋯ → Workflows → plantilla "Send webhook alerts to a channel" ("Enviar alertas de webhook a un canal") → elegir equipo y canal → Crear → copiar la URL.
3. Pasar la URL a Claude en el chat. Para la revisión automática, incluirla en el texto de la tarea programada.

Si el usuario no dio una URL, no enviar a Teams: avisar solo en el chat / notificación de la tarea y ofrecer configurarlo.

El entorno cloud puede no llegar a ese dominio (proxy). Enviar desde el navegador integrado de la computadora del usuario, con `Claude_Browser__javascript_tool` en una pestaña abierta (por ejemplo la de Jira). Respuesta esperada: `202`.

```js
const url = '<TEAMS_WEBHOOK_URL del usuario>';
const card = (titulo, lineas, links=[]) => ({type:'message', attachments:[{contentType:'application/vnd.microsoft.card.adaptive', contentUrl:null, content:{
  $schema:'http://adaptivecards.io/schemas/adaptive-card.json', type:'AdaptiveCard', version:'1.4',
  body:[{type:'TextBlock', text:titulo, weight:'Bolder', size:'Medium', wrap:true},
        ...lineas.map(t=>({type:'TextBlock', text:t, wrap:true}))],
  actions: links.map(l=>({type:'Action.OpenUrl', title:l.title, url:l.url}))}}]});
const r = await fetch(url,{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify(card(
  '🔔 Nuevo comentario en Informe PROJ-XXXX',
  ['Épica: PROJ-YYYY', 'Comentó: <nombre> · <dd/mm hh:mm>', 'Resumen: <1-3 líneas>', 'Clasificación: <n> bugs · <n> consultas · <n> no es error · <n> falta info'],
  [{title:'Abrir Informe', url:'https://tu-sitio.atlassian.net/browse/PROJ-XXXX'}]))});
r.status
```

Si el envío falla (status distinto de 2xx o error), decirlo en el resultado; la notificación de la tarea programada sigue como respaldo.

## Formato del aviso

```
🔔 Nuevo <Informe | comentario | adjunto> en <ÉPICA>
<Informe PROJ-XXXX - título> (botón "Abrir Informe")
Comentó/Reportó: <usuario> · <fecha hora>
Resumen: <1-3 líneas>
Clasificación: <n> bugs · <n> consultas al funcional · <n> no es error · <n> falta info   (solo si hubo análisis)
✏️ <autor> modificó PROJ-H (<hora>): <resumen>. CP a revisar: ...
```
