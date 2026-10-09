---
name: registrar-resultado
description: Registra el resultado de la ejecución de un caso de prueba de Jira PROJ con el formato de evidencia del equipo QA ("Prueba OK" / "Prueba NO OK" + Evidencia), transiciona el CP y, si falló, ofrece crear el bug. Usar cuando el usuario diga "CP 03 de 12345 NO OK: ...", "registrá que pasó el CP...", "el caso PROJ-XXXX dio OK", "marcá como fallido el caso...", o pase varios resultados juntos.
---

# Registrar resultado de un caso de prueba

Leer primero la skill `jira-base`.

## Entradas

- CP: key (PROJ-XXXX) o "CP NN de la HU XXXXX" (buscar entre las subtareas TEST de la UH por el número del título).
- Resultado: OK o NO OK.
- Si es NO OK: descripción del error (lo observado) y, si hay, el esperado y capturas.
- Ambiente: TEST por defecto (preguntar solo si el usuario menciona otro).
- Se pueden recibir varios CP juntos: procesarlos en lote con un solo borrador.

## Flujo

1. Leer el CP (título, resultado esperado, estado, enlaces) y la UH padre.
2. Para NO OK: redactar la evidencia en una o dos frases claras, en presente, nombrando el elemento de pantalla y el síntoma, y contrastando con lo esperado (ej. `El campo "Prestador" solo muestra el código; debe mostrar "código - descripción" y funcionar como enlace`). Si el usuario adjuntó captura en el chat, avisar que debe pegarla en el comentario del CP (no se puede subir desde aquí) y dejar el lugar marcado.
3. Mostrar el borrador del comentario y la transición propuesta:
   - **OK** → comentario "Prueba OK" → CP a **Cerrado**.
   - **NO OK** → comentario "Prueba NO OK" + Evidencia → CP a **En espera**.
4. Esperar OK del usuario.
5. Publicar el comentario y transicionar por nombre de estado destino (snippet `transicionar` en `reportar-bug/references/vinculacion-cp.md`). Flujo de estados de los CP (TEST) en PROJ:
   - `Abierto → En progreso → Cerrado` (a Cerrado solo se llega desde En progreso).
   - `En progreso → En espera` y `En espera → Abierto`.
   - Para OK desde **En espera**: En espera → Abierto → En progreso → Cerrado. Desde **Abierto**: Abierto → En progreso → Cerrado.
   - Para NO OK desde **Abierto**: Abierto → En espera (o Abierto → En progreso → En espera).
   Encadenar los pasos necesarios. Si alguna transición no está disponible, mostrar las posibles y preguntar.
6. **Si fue NO OK**, ofrecer: "¿Creo el bug?" → usar la skill `reportar-bug` (ya tendrá la evidencia para vincular el CP).
7. **Si fue OK** y todos los CP de la UH quedaron Cerrados, avisar: "Todos los casos de PROJ-XXXX están OK: ¿la paso a <siguiente estado>?" (no transicionar la UH sin OK).

## Formato de comentarios (wiki Jira)

OK:
```
{panel:bgColor=#e3fcef}
Prueba OK

Ambiente TEST
{panel}
```

NO OK:
```
{panel:bgColor=#ffebe6}
Prueba NO OK

Ambiente TEST
{panel}

{panel:bgColor=#eae6ff}
Evidencia:

(i) <descripción del error>

<capturas, si el usuario las agrega luego>
{panel}
```
