---
name: estado-epica
description: Arma el reporte de estado de una épica de Jira PROJ (historias por estado, historias bloqueadas y los bugs que las bloquean, bugs abiertos, casos en espera y alertas como historias bloqueadas sin bugs abiertos), listo para pegar en Teams con links, y opcionalmente lo publica en el canal "Avisos QA". Usar cuando el usuario diga "estado de la épica PROJ-XXXX", "armá el informe de la épica", "reporte para Teams", "cómo está la épica".
---

# Reporte de estado de épica

Leer primero la skill `jira-base`.

## Datos a obtener

JQL `parent = <ÉPICA>` con `summary,status,issuetype,issuelinks,subtasks`:
- **Historias** (tipo Historia): agrupar por estado.
- **Bloqueadas**: estado "Bloqueado por BUG" → listar sus enlaces "is blocked by" (Error) con estado del bug y responsable (`assignee` del Error).
- **Bugs abiertos**: tipo Error, estado distinto de Cerrado/Cancelado.
- **Casos en espera**: subtareas TEST en "En espera" por historia (contar).
- **Alertas** (sección aparte, solo si existen):
  - Historia "Bloqueado por BUG" cuyos bugs están todos cerrados → "se puede pasar de estado".
  - Bug cerrado con CP vinculados aún "En espera" → "re-testear".
  - Historia en prueba sin casos de prueba.

Excluir las historias o tickets que el usuario pida excluir. Si el usuario pide renombrar estados en el reporte (ej. "TEST Prueba" → "TEST UAT PEND"), respetarlo.

## Formato (Markdown, listo para pegar en Teams)

```
**📋 Estado épica [PROJ-XXXX](link) – <nombre>**
_Corte: dd/mm/aaaa_

**Resumen de historias (N)**
- 🧪 En <estado>: n
- ⛔ Bloqueadas por BUG: n
- ✅ Cerradas: n

**⛔ Historias bloqueadas y bugs que las bloquean**
1. **[PROJ-H](link)** – <título corto>
   - [PROJ-B](link) – <síntoma> → *<estado>* (<responsable>)
   - CP en espera: CP 03 y CP 06

**🧪 Historias en <estado> (n)**
- [PROJ-H](link) – <título corto>

**🐞 Bugs abiertos:** n (links)

**⚠️ Alertas**
- ...
```

Todo key PROJ va con link `https://tu-sitio.atlassian.net/browse/<KEY>`. Títulos cortos y en lenguaje funcional.

## Publicar en Teams (opcional)

Si el usuario pide "mandalo a Teams" (o lo dispara una tarea programada que lo indique), enviar una Adaptive Card al canal "Avisos QA" con el webhook de `seguimiento-informes/references/aviso.md`: título, resumen con números, lista de bloqueadas con sus bugs y botón "Abrir épica". Para mensajes largos, usar TextBlocks por sección.
