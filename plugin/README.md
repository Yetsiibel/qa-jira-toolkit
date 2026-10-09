# qa-jira-toolkit

Plugin de Cowork para QA de un proyecto (PROJ) en Jira.

## Skills

| Skill | Para qué |
|---|---|
| `casos-de-prueba` | Diseña la suite de CP de una UH (o ajusta los existentes si la UH cambió), deja notas de inconsistencias y, tras validación, crea las subtareas TEST. |
| `reportar-bug` | Redacta el bug con la nomenclatura del equipo, define componente/etiquetas/ambiente/prioridad y, tras validación, crea el Error en la épica vinculado como bloqueante de la UH. |
| `seguimiento-informes` | Detecta Informes UAT y comentarios nuevos en las épicas indicadas, analiza lo reportado contra la UH y la doc. técnica y sugiere qué es bug, consulta o no es error. |
| `registrar-resultado` | Registra Prueba OK / NO OK con el formato de evidencia del equipo, mueve el CP y ofrece crear el bug. |
| `estado-epica` | Reporte de estado de una épica listo para Teams (bloqueadas, bugs, alertas). |
| `responder-informe` | Redacta y publica la respuesta punto por punto a un Informe UAT. |
| `consultas-funcional` | Junta las notas de ambigüedad pendientes, arma el mensaje al funcional y aplica las respuestas. |
| `jira-base` | Base común: acceso a Jira (conector Atlassian o navegador integrado por la restricción de IP), IDs de campos y snippets de API. |

## Requisitos

- Acceso a Jira `tu-sitio.atlassian.net` con la sesión del usuario. Como el cliente restringe por IP, mientras el conector de Atlassian no esté habilitado para Claude se usa el navegador integrado de la app de Claude en la computadora del usuario.
- Para habilitar el conector de Atlassian sin depender de la computadora: un administrador de Atlassian del cliente debe agregar el rango de salida de Anthropic `160.79.104.0/21` a la IP allowlist de Jira y permitir el dominio de Claude en Rovo → Rovo MCP server.

## Configuración por usuario

- **Avisos a Teams:** cada usuario crea su propio webhook (ver `skills/seguimiento-informes/references/aviso.md`) y se lo pasa a Claude. No se guarda en el plugin.
- **Seguimiento automático:** se configura como tarea programada personal indicando las épicas a seguir y el webhook.
