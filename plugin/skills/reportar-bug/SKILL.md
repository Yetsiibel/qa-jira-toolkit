---
name: reportar-bug
description: Redacta un bug con la nomenclatura y estructura del equipo QA de PROJ, define componente (FRONTEND/BACKEND), etiquetas, ambiente y prioridad, y tras validación lo crea en Jira como Error de la épica vinculado como bloqueante de la UH y de los casos de prueba afectados (detectados comparando con la evidencia 'Prueba NO OK'), pasa la UH a 'Bloqueado por BUG' y los CP a 'En espera', y deja comentarios con el link del bug. Usar cuando el usuario diga "reportá este bug", "armá el bug", "creá el error de la UH PROJ-XXXX", "esto falla en...", o cuando el análisis de un informe sugiera crear un bug.
---

# Reportar bug en PROJ

Leer primero la skill `jira-base` para acceso y campos.

## Flujo

1. **Reunir contexto:** UH afectada (y su épica), lo observado por el usuario, capturas/videos/logs. Leer la UH para citar el comportamiento esperado textual (CA, tabla de segmentos, Figma).
2. **Buscar duplicados:** revisar Errores de la épica y los vinculados a la UH (`issuelinks`). Si ya existe uno igual o muy similar, proponer comentar/reabrir ese en lugar de crear otro.
3. **Detectar los CP afectados** con `references/vinculacion-cp.md`: comparar el bug contra el comentario de evidencia "Prueba NO OK" de cada subtarea TEST de la UH (y su título y resultado esperado), descartar los CP ya bloqueados por otro Error que cubre ese síntoma, y proponer los candidatos con nivel de coincidencia y motivo. Si el usuario indicó el CP, usar ese y verificar igual que su evidencia coincida.
4. **Clasificar** según `references/criterios-clasificacion.md`: componente, etiquetas, ambiente y prioridad. Explicar en una línea por qué.
5. **Redactar el borrador** con `references/plantilla-bug.md` y mostrarlo en el chat junto con los campos propuestos y el **plan de acciones** en Jira:
   - CP a vincular (con % de coincidencia y la frase de evidencia que coincide) y CP descartados con el motivo.
   - Estado actual → nuevo de la UH (a "Bloqueado por BUG") y de cada CP (a "En espera").
   - Comentarios que se dejarán en la UH y en cada CP.
6. **Esperar validación** del usuario: confirma o corrige los CP y el resto del borrador. Nada se crea ni se transiciona sin este OK.
7. **Crear y vincular en Jira** (snippets en `references/vinculacion-cp.md`):
   1. Crear el Error: tipo Error (<ID_TIPO_ERROR>), principal = épica de la UH, campos del paso 4, Área solicitante Gerencia IT.
   2. Enlace **Blocks** Error → UH, y Error → cada CP confirmado. **Verificar el sentido** leyendo el Error.
   3. **UH → "Bloqueado por BUG"**: si la UH está en "TEST Prueba" (u otro estado con esa transición disponible), transicionar. Si ya está en "Bloqueado por BUG", dejarla. Si la transición no está disponible, no forzar: informar el estado actual.
   4. **Cada CP → "En espera"** si todavía no lo está.
   5. **Comentarios**: en cada CP y en la UH, un comentario corto "Bloqueado por el bug PROJ-XXXX - <título corto>" (formato en la referencia). En la UH, si se vincularon CP, listarlos.
8. Si el usuario adjuntó evidencia en el chat, indicarle que la adjunte al ticket y dejar referenciada la captura en la sección Evidencia.
9. **Reportar**: link del Error, CP vinculados, transiciones hechas (de → a) y comentarios agregados. Si algo falló (transición no disponible, permiso), decirlo explícitamente.

## Reglas

- El título describe el **síntoma observable**, nunca una hipótesis técnica.
- Un bug = un problema. Si hay varios síntomas independientes, proponer bugs separados.
- No incluir datos sensibles reales más allá de los que el equipo ya usa en los tickets (usuario de prueba, nro. de compromiso/factura de TEST).
