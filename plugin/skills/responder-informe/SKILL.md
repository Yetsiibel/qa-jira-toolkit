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
