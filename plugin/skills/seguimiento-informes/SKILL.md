---
name: seguimiento-informes
description: Revisa las épicas indicadas por el usuario buscando cambios en la descripción de historias con casos de prueba, Informes UAT nuevos (Tareas "Informe UAT en TEST/PROD ...") y comentarios nuevos de usuarios reportando errores, avisa al usuario y analiza cada reporte (texto y capturas) contra la UH y la documentación técnica, sugiriendo qué es bug, qué es consulta al funcional y qué no es error. Usar cuando el usuario diga "revisá los informes de la épica X", "hay informes nuevos?", "analizá el informe PROJ-XXXX", o cuando lo dispare la tarea programada de seguimiento.
---

# Seguimiento de Informes UAT

Leer primero la skill `jira-base` para acceso y campos.

## Entradas

- Lista de épicas a seguir (la indica el usuario o viene en el pedido de la tarea programada).
- Ventana de revisión: por defecto los últimos 65 minutos cuando lo dispara la tarea horaria; si lo pide el usuario a mano, desde la fecha que indique (o todo lo abierto).

## 1. Detectar novedades

- **Informes nuevos:** Tareas hijas de las épicas con "informe" en el título creadas dentro de la ventana.
- **Comentarios nuevos:** en Informes abiertos de esas épicas **y en sus subtareas** (ej. "Coordinación y seguimiento"), comentarios creados dentro de la ventana.
- **Avisar SIEMPRE que comente cualquier persona distinta de la usuaria QA** (el usuario actual, obtenido con `GET /rest/api/2/myself`), sin importar si es negocio, funcional, Dev, PM u otro QA. Solo se excluyen los comentarios hechos por su propio usuario. Los adjuntos nuevos subidos por otras personas también cuentan como novedad.
- Cada comentario de otra persona genera aviso, aunque no reporte errores: indicar autor, fecha/hora, ticket y un resumen. El **análisis** (sección 2) se hace solo cuando el comentario reporta comportamiento/errores; si es coordinación, una pregunta o una respuesta, se avisa con el resumen y se sugiere respuesta si la pide.
- Si no hay novedades: terminar sin avisar (en ejecuciones programadas no enviar mensajes vacíos).

## 1b. Cambios en historias con casos de prueba

En las mismas épicas, para cada Historia que tenga subtareas TEST:
- Leer el changelog y detectar ediciones del campo `description` dentro de la ventana hechas por personas distintas de la usuaria QA.
- Hacer un diff por líneas entre la versión anterior y la nueva (ver `jira-base/references/api-snippets.md`) y resumir qué cambió en 1-3 líneas.
- Identificar los CP potencialmente afectados (los que mencionan el campo, mensaje, permiso o regla modificada) y si alguno contradice el texto nuevo.
- Incluirlo en el mismo aviso a Teams: "✏️ <autor> modificó PROJ-H (<hora>): <resumen>. CP a revisar: CP 02, CP 05." con botón a la UH.
- No editar los CP: solo avisar y sugerir "pedime revisar los casos de PROJ-H" (skill `casos-de-prueba`, Modo B).

## 2. Analizar cada reporte

Para cada Informe o comentario nuevo:

1. Leer texto completo, menciones a tickets (links PROJ-XXXX) y **ver las capturas** adjuntas (ver `jira-base/references/api-snippets.md`, sección Adjuntos).
2. Identificar la UH involucrada (por el link citado, la pantalla o el título del informe) y leer su descripción vigente, CA, tabla de segmentos y la documentación técnica enlazada (por ejemplo, la herramienta de documentación técnica del equipo) si está accesible.
3. Revisar Errores ya existentes de la épica para no duplicar.
4. Clasificar cada punto reportado con `references/clasificacion-reporte.md`.

## 3. Resultado

Presentar por cada Informe:

| # | Punto reportado (resumen + captura) | UH | Qué dice la UH / doc. técnica | Clasificación | Acción sugerida |
|---|---|---|---|---|---|

Clasificaciones: **Bug** · **Consulta al funcional** · **No es error (comportamiento esperado)** · **Fuera de alcance / nuevo requerimiento** · **Ya reportado (PROJ-XXXX)** · **Falta información**.

Después de la tabla:
- Para cada **Bug**: borrador listo con la skill `reportar-bug` (Ambiente detectado `UAT` si el informe es "en TEST", `PROD` si es "en PROD"). No crearlo sin OK.
- Para cada **Consulta al funcional**: pregunta redactada lista para enviar al analista.
- Para **No es error**: respuesta sugerida al usuario citando el CA o regla.

## 4. Aviso

- En ejecución manual: responder en el chat.
- En ejecución programada: enviar el aviso por el canal configurado (ver `references/aviso.md`) con: épica, Informe (link), quién comentó/reportó, resumen del comentario, cantidad de puntos por clasificación (si hubo análisis) y el análisis resumido. El análisis completo queda en el resultado de la sesión.
- Agrupar en un solo aviso todas las novedades de la misma ejecución.
