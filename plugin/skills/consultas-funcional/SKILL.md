---
name: consultas-funcional
description: Junta todas las notas de ambigüedad pendientes ("*Nota:* Ambigüedad pendiente de confirmar con Funcional") de los casos de prueba de una épica o historia de Jira PROJ, arma un único mensaje de consultas para el analista funcional y, cuando llegan las respuestas, ajusta los resultados esperados y quita las notas. Usar cuando el usuario diga "qué notas siguen sin respuesta", "armá las consultas para el analista", "consultas pendientes al funcional de la épica X", "ya respondió el funcional, actualizá los casos".
---

# Consultas al funcional

Leer primero la skill `jira-base`.

## Modo 1 - Recolectar y armar el mensaje

1. Buscar subtareas TEST de la épica (o UH) que contengan la nota: JQL `parent in (<historias de la épica>) AND issuetype = TEST AND description ~ "Ambigüedad pendiente"` (si la búsqueda por texto falla, leer las descripciones y filtrar por `*Nota:* Ambigüedad`).
2. Excluir CP Cerrados o Cancelados.
3. Agrupar por UH y por analista funcional (autor/informador de la UH). Unificar notas que preguntan lo mismo en varios CP.
4. Revisar si la UH ya fue modificada después de la nota (changelog de la descripción): si la ambigüedad parece resuelta en la UH, marcarla como "posiblemente resuelta" en lugar de preguntar.
5. Redactar un mensaje por analista, listo para Teams o para comentar en la UH:
   ```
   Hola <nombre>, ¿cómo estás? Te dejo consultas pendientes de QA:

   *[PROJ-H](link) – <título UH>*
   1. <pregunta concreta, con lo que dice cada sección> (CP 02, CP 05)
   2. ...

   ¡Gracias!
   ```
6. Mostrar el borrador. Canal según el usuario: copiar/pegar (por defecto), comentario en cada UH (mencionando al analista), o enviar al canal Avisos QA. Publicar solo tras OK.

## Modo 2 - Aplicar las respuestas

1. El usuario pega o señala las respuestas (o el comentario de la UH donde respondió el analista).
2. Para cada nota respondida: proponer el nuevo "Resultado esperado" del CP y la eliminación de la nota (o su reemplazo por "*Confirmado por Funcional (<nombre>, dd/mm):* <respuesta resumida>").
3. Si la respuesta genera escenarios nuevos, proponer CP nuevos (skill `casos-de-prueba`).
4. Mostrar el diff por CP y esperar OK. Editar solo las subtareas TEST, guardando el texto previo.
