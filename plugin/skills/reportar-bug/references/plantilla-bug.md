# Plantilla de bug

## Nomenclatura obligatoria del título

`KEY-HISTORIAS | Componente o Acción - Descripción general del Error`

Ejemplo: `PROJ-5900-5901 | Agregar y Editar Denuncias - El campo "Motivo Denuncia" muestra solo el código al seleccionar, no la descripción`

- Si afecta a varias UH: `PROJ-5900-5901` (key de la primera + números de las siguientes).
- "Componente o Acción" = sección/pantalla/acción funcional (ej. "Datos de factura", "Conceptos Editar").

## Borrador en el chat (Markdown)

### Título
KEY-HISTORIAS | Componente o Acción - Descripción general del Error

#### 📝 Descripción
<Qué falla, dónde ocurre y bajo qué contexto funcional.>

#### Datos de prueba
- Usuario: <usuario>
- Ambiente: <TEST/UAT/PROD>
- Dato 1: <valor>
- Dato 2: <valor>

#### Pasos para reproducir
1. <paso 1>
2. <paso 2>
3. <paso n>

#### Resultado esperado
<Comportamiento correcto según HU, CA y Figma, citando el CA o la regla.>

#### Resultado obtenido
<Comportamiento real observado.>

#### Evidencia
- Captura: <link o adjunto>
- Video: <link o adjunto>
- Logs/Console: <link o texto>
- Figma: <link> (si aplica)

**Campos propuestos:** Componente · Etiquetas · Ambiente detectado · Prioridad · Épica · UH bloqueada · CP fallidos

## Descripción en Jira (formato wiki, API v2) - mismo orden de secciones

```
<descripción>

*Datos de prueba:*

* Usuario: <usuario>
* Ambiente: <ambiente>
* <dato>: <valor>

*Pasos para reproducir:*

# Ingresar a la web mediante el enlace [+<url>+|<url>]
# Seleccionar +<Módulo>+
# <...>

*Resultado esperado:*

* <...>

*Resultado obtenido:*

* <...>

*Evidencia:*

* Captura: <...>
* Figma: <...>
```
