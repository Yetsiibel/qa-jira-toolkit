# Cómo clasificar un punto reportado en un Informe UAT

Comparar lo observado (texto + captura) con lo que define la UH vigente (CA, tabla de segmentos, reglas clave, mensajes textuales), Figma y la documentación técnica.

| Situación | Clasificación |
|---|---|
| La UH define claramente el comportamiento y el sistema hace otra cosa | **Bug** |
| La UH no define el caso, es ambigua o contradictoria entre secciones | **Consulta al funcional** |
| El sistema hace exactamente lo que define la UH, aunque al usuario no le guste | **No es error** (si pide otra cosa → **Fuera de alcance / nuevo requerimiento**) |
| Lo reportado pide algo que no está en ninguna UH de la épica | **Fuera de alcance / nuevo requerimiento** |
| Ya existe un Error abierto o cerrado que lo cubre | **Ya reportado (PROJ-XXXX)**; si estaba cerrado y vuelve a ocurrir → Bug (reapertura) |
| No se entiende qué pasó, falta captura, datos o pasos | **Falta información** (redactar qué pedirle al usuario) |
| Error de datos de prueba del ambiente (dato mal cargado, usuario sin permisos) | **No es error** de desarrollo; sugerir corregir el dato o el permiso |

Siempre citar la fuente: "CA05", "Tabla Acciones - fila Borrar factura", "Regla clave 3", "Doc. técnica - sección X". Si la clasificación depende de una suposición, decirlo.
