# Skills

Skills y subagents son mecanismos distintos. Confundirlos es el error más común al trabajar con el pipeline.

---

## La diferencia

| | Subagents | Skills |
|---|-----------|--------|
| **Qué es** | Agente autónomo | Módulo de instrucciones |
| **Cómo se invocan** | `task` con `subagent_type` | `skill` con nombre |
| **Qué hacen** | Ejecutan trabajo multistep por su cuenta | Inyectan criterios y reglas en contexto |
| **Autonomía** | Sí — trabajan solos | No — informan a quien los invoca |

Un skill no ejecuta una tarea. Cambia **cómo** se ejecuta.

---

## Skills que refuerzan el pipeline

### `arquitecto`

Revisor de solo lectura. Analiza cambios y features contra la arquitectura, la lógica de negocio, la seguridad y la deuda técnica.

Emite un veredicto estructurado (`APROBADO` / `CON CONDICIONES` / `RECHAZADO`) y **no escribe código** — es el contrapeso del constructor.

**Usar:** antes de construir (revisión de diseño) o sobre un diff existente (revisión de cambio).

Es el **Gate 1**. Corre antes de que exista código.

---

### `worker`

Constructor ejecutor, par de `arquitecto`. Toma un veredicto o plan y lo implementa con disciplina: handoff tibio desde `.arquitecto/`, diff mínimo, enfoque aditivo.

**Usar:** cuando ya está claro *qué* construir y falta hacerlo bien.

Diferencia con `implementa`: `worker` ejecuta desde un plan estructurado (idealmente `.arquitecto/`), mientras `implementa` traduce un design spec.

---

### `explore`

Fast agent de exploración. Encuentra archivos por patrón, busca contenido, entiende la arquitectura existente.

**Usar:** al inicio, para mapear antes de entrar al pipeline.

---

### `general`

Agente de propósito general para investigación compleja o múltiples unidades en paralelo.

**Usar:** cuando ninguna etapa específica aplica.

---

## Skills de apoyo (fuera del pipeline)

Estos no participan del flujo principal pero son útiles en su contexto:

| Skill | Qué aporta |
|-------|-----------|
| `impeccable` | Auditoría y Totora de interfaces: jerarquía, accesibilidad, performance, anti-patrones |
| `scroll-cinematic` | Animación vinculada a scroll: ScrollTrigger, Lenis, scrub, pinning |
| `high-end-visual-design` | Tipografía, spacing, sombras, estructura de cards — lo que separa barato de caro |
| `minimalist-ui` | Interfaz editorial, paleta monocroma cálida, grillas bento planas |
| `design-taste-frontend` | Anti-slop: evita templates genéricos en landings y portfolios |
| `redesign-existing-projects` | Mejora un sitio existente sin romper funcionalidad |

---

## Cómo encajan

Los skills **no reemplazan** etapas del pipeline. Endurecen el contrato:

- `arquitecto` → agrega validación de arquitectura antes de implementar
- `worker` → agrega disciplina de ejecución sobre un plan ya validado
- `explore` → agrega contexto antes de diseñar

La secuencia de agentes del pipeline no cambia. Lo que cambia es qué criterios se están aplicando cuando cada uno hace su parte.