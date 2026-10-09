# QA Jira Toolkit · Claude (Cowork)

Kit de QA para equipos que trabajan con **Jira** y **Claude (Cowork)**: diseña y mantiene casos de prueba a partir de historias de usuario, registra resultados, reporta bugs vinculados a los casos afectados, sigue los informes UAT de los usuarios y avisa por **Microsoft Teams**.

## Qué incluye

| Carpeta | Contenido |
|---|---|
| `plugin/` | Plugin de Cowork `qa-jira-toolkit` con 8 skills (ver abajo). |

### Skills del plugin

| Skill | Qué hace |
|---|---|
| `casos-de-prueba` | Analiza criterios de aceptación y segmentos funcionales, arma la suite óptima de casos con notas de inconsistencias de la historia y, tras validación, crea las subtareas de prueba. También detecta qué cambió en una historia y ajusta los casos existentes. |
| `registrar-resultado` | Registra "Prueba OK / NO OK" con un formato de evidencia estándar, mueve el caso de estado y ofrece crear el bug. |
| `reportar-bug` | Redacta el bug con nomenclatura y secciones del equipo, clasifica componente/etiquetas/ambiente/prioridad, detecta los casos afectados comparando con la evidencia, bloquea la historia y los casos y deja comentarios. |
| `seguimiento-informes` | Detecta informes UAT, comentarios de otros usuarios y cambios en historias; analiza cada punto contra la historia y sugiere si es bug, consulta o no es error. |
| `estado-epica` | Reporte de estado de una épica listo para Teams (bloqueadas, bugs, alertas). |
| `responder-informe` | Redacta la respuesta punto por punto a un informe UAT. |
| `consultas-funcional` | Junta las ambigüedades pendientes y arma el mensaje al analista funcional; luego aplica las respuestas. |
| `jira-base` | Base común: acceso a Jira, IDs de campos y snippets de API. |

## Cómo adaptarlo a tu Jira

Los valores específicos están como marcadores `<...>`:
- `tu-sitio.atlassian.net` → tu sitio de Jira.
- `PROJ` → la clave de tu proyecto.
- `customfield_<...>`, `<ID_...>` → los IDs de tus campos, tipos de incidencia y componentes (se obtienen con `/rest/api/2/issue/createmeta/<PROJ>/issuetypes` y `/rest/api/2/project/<PROJ>/components`).
- Estados ("TEST Prueba", "Bloqueado por BUG", "En espera", "Cerrado"): ajustar a tu flujo en `jira-base` y `registrar-resultado`.

## Instalar

1. Comprimir el **contenido** de `plugin/` en un `.zip`.
2. Uso personal: app de Claude → Personalizar → Plugins → subir el zip.
   Organización: claude.ai → Configuración de la organización → Plugins & skills → Add → Upload a plugin.

## Avisos a Teams

Cada usuario crea su propio webhook (Teams → canal → ⋯ → Workflows → "Send webhook alerts to a channel") y se lo pasa a Claude en el chat o en su tarea programada. **Nunca** subir el webhook al repositorio: funciona como una contraseña.

## Licencia

MIT
