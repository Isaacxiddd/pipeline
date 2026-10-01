# El pipeline en detalle

## Principio rector

**Especificación y implementación están separadas por diseño.**

No es una preferencia de estilo — es lo que hace auditable el resultado. Si un agente escribe código sin un spec previo, no hay forma de verificar si lo que construyó cumple con lo pedido. Y sin verificación, el output es desechable.

---

## Flujo

```
 ┌──────────────┐
 │  direccion   │  concepto visual, paleta, tipografía, referencias
 └──────┬───────┘
        │ brief de dirección
        ▼
 ┌──────────────┐
 │   diseno     │  layout, grilla, tokens, estados, responsive
 └──────┬───────┘
        │ design spec  ← fuente de verdad
        ▼
 ┌──────────────┐
 │  implementa  │  código real, diff mínimo, verificado
 └──────┬───────┘
        │ código
        ▼
 ┌──────────────┐
 │   revisa     │  fidelidad + arquitectura
 └──────┬───────┘
        │
        ├── APROBADO ──────────────► fin
        │
        └── CON CONDICIONES / RECHAZADO
                     │
                     └────► vuelve a implementa
```

---

## Handoff: qué genera cada etapa

Cada agente produce un artefacto explícito. Sin esto, el siguiente etapa tiene que adivinar.

| Etapa | Entregable | Para qué lo usa la siguiente |
|-------|-----------|------------------------------|
| `direccion` | Brief: concepto, moodboard, paleta, tipografías, tono por sección | Da el marco visual a `diseno` |
| `diseno` | **Design spec**: layout por sección, grilla, spacing, tokens, estados, responsive | **Fuente de verdad de la implementación** |
| `arquitecto` | Veredicto + requisitos bloqueantes / condicionados | Define qué debe cumplirse a nivel arquitectura |
| `anima` | Spec de animación: timing, easing, delays, orden de capas | Traducir a código en la implementación |
| `implementa` | Código aplicado + resumen breve de cambios | Material que audita `revisa` |
| `revisa` | Veredicto + hallazgos clasificados por severidad, con `archivo:línea` | Alimenta el loop de corrección |

---

## El design spec

Es el contrato que hace funcionar todo. Sin spec explícito, "fiel al diseño" no significa nada.

Un design spec mínimo contiene:

- **Layout por sección** — estructura y composición de cada bloque
- **Grilla** — columnas, gutters, breakpoints
- **Tokens** — color, tipografía, radius, spacing, z-index
- **Estados** — hover, active, focus, disabled, error, loading, vacío
- **Responsive** — comportamiento en mobile / tablet / desktop

`implementa` debe ceñirse a este spec, no reinterpretarlo. Si el spec es ambiguo, se corrige el spec — no se improvisa en el código.

---

## El loop de corrección

El bucle es lo que convierte el pipeline en algo confiable.

**Al recibir feedback de `revisa`:**

1. Corregir **solo** los puntos señalados
2. Priorizar bloqueantes sobre mejoras
3. Mantener diff mínimo
4. Volver a auditar

**Lo que no se hace:** rehacer dirección o diseño porque el código quedó feo. Si el spec es correcto y la implementación es fiel pero el resultado no gusta, eso es un problema del spec — y se vuelve al principio, no se parchea en la capa de código.

---

## Doble gate

| Gate | Agente | Momento | Valida |
|------|--------|---------|--------|
| 1 | `arquitecto` | Antes de escribir código | Arquitectura, lógica de negocio, seguridad, mantenibilidad |
| 2 | `revisa` | Después de implementar | Fidelidad al spec, consistencia de tokens, defectos de código |

El gate 1 previene construir sobre una base mala. El gate 2 previene que la base mala se convierta en código feo y pulido.

---

## Cuándo usar cada flujo

**Flujo clásico** — landing simple, sección aislada, iteración rápida:

```
direccion → diseno → implementa → revisa
```

**Flujo extendido** — feature compleja, sistema existente, cambios con riesgo:

```
arquitecto → diseno → worker → revisa
```

La diferencia real es dónde entra la validación de arquitectura. En el flujo clásico se asume que el spec de diseño no tiene implicaciones estructurales. En el extendido, se chequea explícitamente antes de construir.

---

## Anti-patrones

- **Implementar sin spec.** Es el error que rompe el pipeline entero.
- **Pedir `APROBADO` sin `revisa`.** Un veredicto que no se pidió no es un gate.
- **Corregir todo en el loop.** Si `revisa` señala dos cosas, se corrigen dos.
- **Tratar `anima` como implementación.** Es read-only por diseño.
- **Saltear `arquitecto` en cambios con riesgo.** El gate 1 existe para eso.