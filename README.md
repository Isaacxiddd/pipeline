# Pipeline

Un pipeline de agentes especializados para construir frontends de alta calidad: **dirección de arte → diseño → implementación → auditoría**, con un gate de aprobación obligatorio.

La idea central es simple: **separar la especificación (read-only) de la implementación (único agente que escribe código)** y obligar a que el trabajo pase una revisión antes de darse por terminado.

---

## Por qué

Los agentes de IA之于 el diseño tienen un problema recurrente: abren el editor, escriben código plausible, y el resultado es *slop* — genérico, sin dirección, imposible de auditar.

Este pipeline lo resuelve con tres reglas:

1. **Nadie implementa sin spec.** Primero existe un diseño documentado y explícito.
2. **Solo un agente escribe código.** Los demás son read-only. Esto elimina el conflicto de ediciones superpuestas.
3. **Nada está terminado hasta que se aprueba.** Un auditor independiente compara la implementación contra el spec y emite un veredicto.

---

## El pipeline

```
   direccion            diseno             implementa            revisa
  (read-only)         (read-only)          (ESCRIBE)          (read-only)
       │                   │                    │                   │
       ▼                   ▼                    ▼                   ▼
   concepto        spec de diseño          código real       APROBADO
   visual          grilla, tokens,         diff mínimo,          │
   paleta          jerarquía,             verificado          CON CONDICIONES
   tipografía      responsive                    │            RECHAZADO
                                                     │                 │
                    ▲                                 │                 │
                    └─────────────────────────────────┴─────────────────┘
                              loop de corrección hasta APROBADO
```

### Etapas

| # | Agente | Rol | Permisos |
|---|--------|-----|----------|
| 1 | `direccion` | Concepto visual, paleta, tipografías, referencias por sección | Read-only |
| 2 | `diseno` | Spec de diseño concreto: layout, grilla, tokens, estados, responsive | Read-only |
| 3 | `implementa` | Traduce el spec a código real con diff mínimo | **Edita** |
| 4 | `revisa` | Audita fidelidad al spec + arquitectura. Emite veredicto | Read-only |
| — | `anima` | *(opcional)* Spec de animación: microinteracciones, timing, easing | Read-only |

---

## El gate de aprobación

`revisa` emite siempre uno de tres veredictos:

| Veredicto | Significado | Qué sigue |
|-----------|-------------|-----------|
| `APROBADO` | Cumple el spec. Cerrado. | Fin |
| `CON CONDICIONES` | Funciona, pero hay desviaciones o deuda | Volver a `implementa` con los puntos señalados |
| `RECHAZO` (o `RECHAZADO`) | Desviación grave de arquitectura, seguridad o spec | Volver a `implementa`, revisar alcance |

**Reglas del loop:**

- Solo se corrige lo señalado. No se rehace dirección o diseño salvo que el propio spec tenga un error.
- Los hallazgos se separan en **bloqueantes** (bloquean aprobación) y **no bloqueantes** (mejoras).
- Cada vuelta parte del estado corregido, con diff mínimo.

---

## Doble gate

Dos preocupaciones distintas conviene validarlas por separado:

| Gate | Agente | Valida |
|------|--------|--------|
| **Gate 1 — Arquitectura** | `arquitecto` | Coherencia arquitectónica, lógica de negocio, seguridad, acoplamiento, deuda técnica |
| **Gate 2 — Fidelidad** | `revisa` | Cumplimiento estricto del spec de diseño, consistencia de tokens, defectos de implementación |

El orden importa: se valida la arquitectura **antes** de escribir código, para no construir sobre una base débil.

---

## `implementa` vs `worker`

Ambos escriben código, con enfoques distintos:

| Aspecto | `implementa` | `worker` |
|---------|-------------|---------|
| Origen del plan | Spec de `diseno` | Plan/veredicto estructurado (idealmente `.arquitecto/`) |
| Filosofía | Traducción fiel del spec | Ejecución disciplinada, aditiva |
| Cuándo | Flujo diseño → revisión puro | Cuando el alcance ya está cerrado y validado |

---

## Skills vs Subagents

Son mecanismos distintos:

- **Subagents** (`task`) — agentes autónomos que ejecutan trabajo multistep por su cuenta. Es el pipeline de arriba.
- **Skills** (`skill`) — módulos de instrucciones que se cargan en contexto. Inyectan reglas y criterios; no ejecutan por sí solos.

Los skills refuerzan el pipeline sin saltárselo. Los más relevantes:

| Skill | Rol |
|-------|-----|
| `arquitecto` | Revisor de arquitectura y seguridad. Contrapeso del constructor. |
| `worker` | Constructor ejecutor. disciplined, aditivo, diff mínimo. |
| `explore` | Mapeo rápido del codebase antes de entrar al pipeline. |

---

## Uso

Flujo clásico:

```
direccion → diseno → implementa → revisa
```

Flujo endurecido (features complejas):

```
arquitecto → diseno → worker → revisa
```

En ambos casos el loop de `revisa` corre hasta `APROBADO`.

---

## Documentación

- [`docs/pipeline.md`](docs/pipeline.md) — el flujo detallado, handoffs y contratos
- [`docs/agentes.md`](docs/agentes.md) — referencia de cada agente del pipeline
- [`docs/skills.md`](docs/skills.md) — skills que refuerzan el pipeline

---

## Licencia

MIT