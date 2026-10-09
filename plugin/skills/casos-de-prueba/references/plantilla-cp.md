# Plantilla de caso de prueba

## Borrador en el chat (Markdown, estricto)

### CP [Número de caso] - [Código HU] - [Título corto y descriptivo]
* **Qué se prueba:** [Breve explicación del objetivo de la prueba]
* **Pre-condiciones:** [Estado previo del sistema necesario para ejecutar la prueba]
* **Datos de prueba:** [Datos específicos, variables o inputs requeridos]
* **Paso a paso:**
  1. [Paso 1]
  2. [Paso 2]
  3. [Paso 3...]
* **Resultado esperado:** [Comportamiento correcto del sistema al finalizar los pasos]
* **Nota:** [Solo si aplica: inconsistencia de la UH que afecta a este caso]

## Descripción en Jira (formato wiki, API v2)

```
*Qué se prueba:*

<objetivo>

*Pre-condiciones:*

* <condición 1>
* <condición 2>

*Datos de prueba:*

Acceso a la aplicación: [+<url de acceso del equipo>+|<url de acceso del equipo>]

* <dato 1>
* <dato 2>

*Paso a paso:*

# Ingresar a la web a través del enlace
# Seleccionar +<Módulo>+
# <... navegación base usada por el equipo para esa pantalla ...>
# <acción a validar>

*Resultado esperado:* <resultado>

*Nota:* Ambigüedad pendiente de confirmar con Funcional (<autor de la UH>) en la HU <KEY>: <qué dice cada parte>. <Qué fuente se tomó como referencia>. El resultado esperado se ajustará según la respuesta.
```

- La sección *Nota:* va solo en los CP afectados.
- Los elementos de pantalla (menús, botones, secciones) van con `+subrayado+` en los pasos, como hacen los CP existentes.
