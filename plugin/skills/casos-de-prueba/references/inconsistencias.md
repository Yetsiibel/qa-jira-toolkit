# Checklist de inconsistencias en una UH

Revisar cruzando Detalle funcional, Detalle de segmentos funcionales (tablas), Reglas funcionales clave, Criterios de aceptación, Observaciones/Permisos y documentación técnica.

1. **Textos distintos** para el mismo mensaje, tooltip, título de modal o botón entre secciones (ej. tooltip "(634 o 635)" en la tabla vs "(634)" en el CA).
2. **Permisos** distintos o "A confirmar" en una sección y valor concreto en otra.
3. **Comportamiento contradictorio**: control deshabilitado vs. clickeable con mensaje; oculto vs. deshabilitado; nueva pestaña vs. misma pestaña.
4. **Nombres distintos** para la misma opción/acción ("Borrar siniestro" vs "Borrar Registros", "Borrar Registros de Factura").
5. **CA sin detalle funcional** que lo respalde, o regla/segmento sin CA.
6. **Reglas que se contradicen** (ej. borrar solo automáticos vs. manuales y automáticos).
7. **Datos sin definir**: mensaje de error genérico sin texto, formato de fecha/moneda, orden de grilla, cantidad de decimales, comportamiento ante error técnico.
8. **Restos de versiones anteriores**: referencias a campos, opciones o pantallas que ya no existen en el resto de la UH.
9. **Diferencias con Figma o la documentación técnica**, si se pudieron consultar.
10. **Errores de redacción que cambian el sentido** (ej. CA cortado, "se posesiona en e").

Para cada hallazgo registrar: secciones involucradas y texto de cada una, CP afectados, fuente tomada como referencia, y pregunta sugerida al funcional.
