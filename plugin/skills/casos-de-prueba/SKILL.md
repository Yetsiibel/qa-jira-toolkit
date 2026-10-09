---
name: casos-de-prueba
description: Diseña la suite óptima de casos de prueba de una Historia de Usuario de Jira PROJ, detecta inconsistencias de la UH y las deja como nota en cada caso, y (tras validación) crea las subtareas TEST. También revisa qué cambió en la descripción de una UH y ajusta los CP existentes. Usar cuando el usuario diga "creá los casos de prueba de PROJ-XXXX", "armá los CP de esta UH", "qué cambió en la historia y qué CP hay que modificar", "revisá los casos de prueba de la UH".
---

# Casos de prueba desde una UH

Actuar como QA Automation/Manual Engineer Senior, analítico y meticuloso. Leer primero la skill `jira-base` para acceso y campos.

## Modo A - Suite nueva

1. **Leer la UH** (descripción completa, subtareas, links de documentación técnica y Figma). Leer también 1-2 CP existentes de otras UH de la misma épica para reutilizar la ruta de navegación (paso a paso base) y el link de acceso a la aplicación que usa el equipo.
2. **Análisis exhaustivo:** recorrer TODOS los Criterios de Aceptación (CA) y complementarlos con "Detalle de segmentos funcionales" (tablas de campos, validaciones, observaciones, permisos, tooltips, mensajes exactos) y "Reglas funcionales clave". Armar una matriz interna CA/regla → CP para garantizar 100% de cobertura.
3. **Cobertura optimizada:** la cantidad justa de casos. Agrupar validaciones que se verifican en la misma pantalla y estado; separar caminos felices, alternativos y de error/restricción (permisos, compromiso generado, campos vacíos, cancelaciones). Evitar duplicados.
4. **Detectar inconsistencias** de la UH (ver `references/inconsistencias.md`). Cada inconsistencia se deja como **Nota** en el/los CP afectados, y el resultado esperado toma la fuente más específica (tabla de segmentos > regla clave > CA), dejando explícito que se ajustará según la respuesta del funcional.
5. **Presentar el borrador en el chat** con la plantilla de `references/plantilla-cp.md` (Markdown), seguido de:
   - Matriz de cobertura (CA/regla → CP).
   - Lista de inconsistencias con a qué CP se asignó cada nota.
   - Preguntas sugeridas para el analista funcional (autor de la UH).
6. **Esperar validación.** Aplicar los ajustes que pida el usuario.
7. **Crear en Jira** cada CP como subtarea TEST de la UH (snippet en `jira-base/references/api-snippets.md`), convirtiendo el Markdown a formato wiki de Jira según `references/plantilla-cp.md`. Campos: Área solicitante Gerencia IT, solo el componente de Desarrollo de la UH (excluir siempre los componentes "Discovery"), etiqueta `QA`, prioridad Media, Gerencias copiadas de la UH. Sin persona asignada y sin sprint, salvo que el usuario lo pida.
8. Verificar leyendo la UH que todas las subtareas quedaron creadas y reportar la lista con links.

## Modo B - La UH cambió: ajustar CP existentes

1. Leer el changelog de la descripción. Tomar como base la versión vigente al crearse el CP más antiguo (no solo la última edición), para no perder cambios intermedios.
2. Mostrar un diff resumido: qué cambió, cuándo y quién.
3. Leer cada subtarea TEST existente y clasificar: **modificar** (con el ajuste puntual), **sigue válido**, o **falta crear** (CA/regla sin cobertura). Revisar también CP que contradicen la UH aunque no sea por el último cambio, y pasos incompletos (que terminan antes de la acción a validar).
4. Detectar inconsistencias y proponer notas como en el Modo A.
5. Presentar el análisis y los borradores; esperar validación.
6. Aplicar: editar solo subtareas TEST (cambios puntuales con reemplazo de texto, conservando el resto) y crear las nuevas con la numeración siguiente. Guardar una copia del texto previo de cada CP antes de editar, para poder revertir.

## Reglas

- No inventar datos de prueba reales (usuarios, facturas): usar descripciones del tipo "Factura sin compromiso generado" o los ejemplos que trae la UH.
- Mensajes, títulos de modales y tooltips se copian textuales de la UH entre comillas.
- Título: `CP NN - PROJ-XXXX - Título corto` (NN con dos dígitos).
