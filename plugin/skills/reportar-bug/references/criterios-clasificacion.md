# Criterios de clasificación de bugs

## Componente (FRONTEND <ID_FRONTEND> / BACKEND <ID_BACKEND>)

- **FRONTEND:** el dato llega bien pero se muestra mal; estilos, colores, íconos, alineación, textos/labels/tooltips/mensajes fijos, modales, estados de botones (habilitado/deshabilitado/oculto) según reglas que se resuelven en pantalla, navegación/redirecciones, formatos de fecha/moneda en pantalla, validaciones de formulario en el cliente.
- **BACKEND:** datos faltantes, incorrectos o desactualizados (lo que devuelve el servicio), cálculos y totales, persistencia (no guarda/no borra/borra de más), cambios de estado en base, permisos validados en servidor, errores 4xx/5xx, respuestas lentas, mensajes de error que vienen del servicio.
- Si la evidencia no permite distinguir (ej. campo vacío: ¿no viene o no se pinta?), revisar la pestaña Network/console si es posible; si no, proponer el más probable, explicarlo y marcarlo como "a confirmar por Dev". Puede llevar ambos componentes si claramente involucra los dos. Si además se agrega el componente funcional de la UH, usar solo el de "Desarrollo", nunca el de "Discovery".

## Etiquetas (pueden ir varias)

- `UI-UX`: el error es visual y no funcional (color, ícono, alineación, texto que no coincide con Figma pero no cambia el comportamiento).
- `criterio-de-aceptacion`: el error es funcional o incumple un CA.
- `bloqueante`: se agrega además cuando impide continuar la prueba del flujo o de otros CP.

## Ambiente detectado (campo, obligatorio)

- QA lo detecta antes de llegar al usuario → `TEST`.
- Lo reporta un usuario/cliente durante UAT en entorno TEST → `UAT`.
- Lo reporta un usuario/cliente durante UAT en PROD → `PROD`.

## Prioridad (campo, obligatorio)

| Escala QA | Jira |
|---|---|
| P1 urgente | 1 Crítica |
| P2 alta | 2 Alta |
| P3 media | 3 Media |
| P4 baja | 4 Baja |

Guía: P1 = bloquea el flujo principal o hay pérdida/corrupción de datos; P2 = incumple un CA funcional sin alternativa; P3 = incumple un CA con alternativa o afecta un caso secundario; P4 = cosmético. Siempre proponer y dejar que el usuario confirme.
