# Agentes del pipeline

Referencia de cada subagent, su rol, permisos y qué se espera de él.

---

## `direccion` — Dirección de arte

**Permisos:** read-only

Define el marco visual antes de que exista un solo pixel.

**Entrega:** concepto visual, paleta, tipografías, referencias por sección, tono.

**No hace:** layout, código, decisiones de grilla.

---

## `diseno` — Diseño UI

**Permisos:** read-only

Convierte la dirección de arte en una especificación concreta y aplicable.

**Entrega:** design spec — layout por sección, grilla, tokens, estados, responsive.

**No hace:** código. Su output es el contrato de implementación.

---

## `implementa` — Constructor frontend

**Permisos:** **edición — único agente del pipeline que escribe código**

Traduce el design spec a código real. Es su única función y su única responsabilidad.

**Entrega:** código aplicado con diff mínimo, verificado.

**Reglas:**
- Ceñirse al spec, no reinterpretarlo
- Cambio aditivo cuando se pueda
- Si el spec es ambiguo, frenar y corregir el spec

---

## `revisa` — Auditor de pipeline

**Permisos:** read-only

Revisa lo implementado contra el spec de diseño (fidelidad visual, anti-patrones) y contra la arquitectura y seguridad del código.

**Entrega:** veredicto estructurado.

| Veredicto | Significado |
|-----------|-------------|
| `APROBADO` | Cumple el spec. Sin bloqueantes. |
| `CON CONDICIONES` | Funciona con desviaciones o deuda |
| `RECHAZADO` | Desviación grave de arquitectura, seguridad o spec |

**Reglas:**
- Separar hallazgos **bloqueantes** de **no bloqueantes**
- Referenciar `archivo:línea`
- No editar código — solo reportar

---

## `anima` — Motion designer

**Permisos:** read-only

Genera el sistema de movimiento: microinteracciones, scroll, timing, easing.

**Entrega:** spec de animación para que otro lo implemente.

**Opcional:** entra cuando el proyecto tiene movimiento. No todos los frontends lo necesitan.

---

## `explore` — Explorador de codebase

**Permisos:** read-only

Mapeo rápido del código: estructura, patrones, dónde vive cada cosa.

**Cuándo:** antes de entrar al pipeline, para entender con qué se está trabajando.

---

## `general` — Agente polivalente

**Permisos:** según la tarea

Investigación o múltiples unidades de trabajo en paralelo. Cuando ninguna etapa específica aplica.

---

## Tabla resumen

| Agente | Permisos | Entrega |
|--------|----------|---------|
| `direccion` | read-only | Brief de dirección de arte |
| `diseno` | read-only | Design spec |
| `implementa` | **edita** | Código |
| `revisa` | read-only | Veredicto + hallazgos |
| `anima` | read-only | Spec de animación |
| `explore` | read-only | Mapa del codebase |
| `general` | variable | Investigación |