---
name: plan-implementacion
description: 'Toma una especificación técnica cerrada (contratos: tipos, schemas, APIs, módulos) y produce un plan de implementación ejecutable en estructura Fases → Tareas, e inicializa los archivos de control del desarrollo (seguimiento, bitácora, herramientas recomendadas). NO escribe código ni reabre contratos/ADRs. Trigger: el usuario tiene la especificación técnica cerrada y pide plan de implementación, plan de desarrollo, secuenciar tareas o "preparar para construir". Slash command sugerido: /plan-implementacion <archivo-spec>.'
---

# plan-implementacion

Quinta fase de la metodología Spec-Design, primera mitad de la **construcción** (convenciones compartidas: `CONVENCIONES.md` del harness):

```
… → 4 especificacion-tecnica → 5 plan-implementacion (este skill) → 6 desarrollo → código
                                        ↓
                          [plan.md + archivos de control]
```

Convierte los contratos de la especificación técnica en un **plan ejecutable, trazable y secuenciado por dependencias**, e inicializa la infraestructura de control que el skill `desarrollo` usará durante el build. No construye nada: define **qué construir y en qué orden**.

---

## Qué consume (contrato de entrada)

- **`especificacion-tecnica.md`** — contratos exactos (tipos, JSON Schema, endpoints, estructura de módulos, contrato LLM, modelo de error). Es la fuente de verdad de *qué existe*.
- **`diseno.md`** §Alcance MVP — RFs Must + **dependencias funcionales** (orden sugerido).
- **`diseno-tecnico.md`** — ADRs `Aceptada` (stack, persistencia, etc.).
- **`BACKLOG.md`** (solo en releases posteriores al MVP) — la cola viva del producto. En un release v2+, el **alcance del release sale de aquí** (ítems marcados para ese release), no del §Alcance MVP.

> La spec y el diseño son fuente de verdad. Este skill **no reabre** contratos ni ADRs. Si algo necesario no está definido, **pregunta**, no inventa.

---

## Qué hace este skill

1. Lee la spec + alcance MVP + ADRs (en un release v2+, lee también `BACKLOG.md`).
2. Produce en `documentacion/05-desarrollo/`:
   - `plan.md` — estructura **Fases → Tareas**.
   - **inicializa** (con semilla/plantilla) `seguimiento.md`, `bitacora.md`, `herramientas-recomendadas.md`.
3. **Inicializa/actualiza `documentacion/BACKLOG.md`** (cola viva del producto): registra todo el alcance que el release **deja fuera** (RFs Should/Could/Won't, deuda técnica de los ADRs, insumos pendientes). Esto evita que el alcance no planificado se "pierda". No duplica los requerimientos: **referencia** los RFs en `diseno.md`.
4. (Si hay ambigüedades de secuenciación) `preguntas-plan.md`.
5. Actualiza `INDICE.md`.
6. Handoff al skill `desarrollo`.

## Qué este skill NO hace

- **No escribe código** ni implementaciones.
- **No reabre** ni "mejora" contratos, tipos, schemas o ADRs.
- **No estima** esfuerzo ni fechas.
- **No agrega RFs/endpoints** que no estén en la spec.
- **No ejecuta** las tareas (eso es `desarrollo`).

---

## Fase 0: Aclaraciones iniciales

Confirmar **en un solo mensaje breve**:

1. **Fuentes:** rutas de `especificacion-tecnica.md`, `diseno.md`, `diseno-tecnico.md`.
2. **Granularidad de tarea:** por defecto, **tarea atómica** = una unidad que se completa y verifica de una sentada (un módulo, un endpoint, una vista). Confirmar o ajustar.
3. **Resumen** en 3–4 viñetas de qué se va a producir.

No empezar sin confirmación.

> **Modo directo** (`CONVENCIONES.md` §9): si la skill fue invocada por el orquestador `mejora` o el usuario pasó `directo`, anunciar fuentes y defaults en un mensaje y proceder sin esperar confirmación.

---

## Fase 1: Generar el plan + inicializar control

### Estructura objetivo de `plan.md`

```
1. Encabezado + fuentes + nota "no reabre contratos"
2. Resumen de fases (tabla)
3. Fases → Tareas (el cuerpo)
4. Matriz de cobertura RF → tareas
```

### Fases

- Derivar las **fases** del orden de dependencias del §Alcance MVP. Cada fase es un hito coherente que deja algo verificable.
- Para cada fase: **objetivo**, **RFs que cubre**, **gate de cierre**, y si es **paralelizable** (informativo, aunque el desarrollo sea secuencial).

### Formato de tarea

```markdown
#### F{n}-T{m}: [Título]
- **Objetivo:** qué deja hecho (1 línea).
- **Archivos / contratos:** referencias a la spec (módulo, tipo, endpoint, schema).
- **Depende de:** F{n}-T{k} (o "—").
- **Cubre:** RF-N / endpoint / vista.
- **Criterio de done:** condición verificable y concreta.
- [ ] Hecho
```

### Gate de cierre de fase (texto fijo por fase)

> Al cerrar la fase: ejecutar verificación (lint/build + `verify`/`run`), actualizar `seguimiento.md`, anotar en `bitacora.md` lo notable, y consultar `herramientas-recomendadas.md` para la fase siguiente.

### Reglas

- **Cobertura total:** cada RF Must del MVP aparece en al menos una tarea (validar contra la matriz de §4).
- **Trazabilidad:** cada tarea referencia su contrato en la spec; nada de tareas "huérfanas".
- **Orden por dependencias:** una tarea no puede preceder a aquello de lo que depende.
- **F0 primero:** scaffolding, estructura de carpetas, tipos base, puertos/interfaces (persistencia, integraciones) y config van antes que cualquier feature.
- **Atómicas:** si una tarea no se puede verificar de una sentada, dividirla.

### Inicialización de los archivos de control

Los archivos de control **se copian desde las plantillas del kit** (`_harness/templates/` del harness) y se rellenan los placeholders (`{proyecto}`, `{fecha}`, fases del plan). No re-escribir las plantillas desde cero — son parte del contrato del kit:

- **`seguimiento.md`** — plantilla `templates/seguimiento.md`. Rellenar la tabla con las fases reales del plan.
- **`bitacora.md`** — plantilla `templates/bitacora.md` (append-only, registro vivo con referencia cruzada `B-{nnn}` + Origen/Refs/Por; ver `CONVENCIONES.md` §3). El skill `desarrollo` sigue ese formato al añadir entradas.
- **`herramientas-recomendadas.md`** — plantilla `templates/herramientas-recomendadas.md`. Ajustar la columna de fases sugeridas al plan real; todas las herramientas son **opcionales según el entorno**.
- **`BACKLOG.md`** (en la raíz de `documentacion/`) — plantilla `templates/BACKLOG.md`, si `/iniciar-harness` no lo creó ya. Se crea en la primera corrida (MVP) capturando el alcance diferido y se actualiza en cada release. **No redefine** requerimientos: referencia los RFs de `diseno.md` y les sigue el estado (vocabulario único de estados: `CONVENCIONES.md` §4).

### Reglas para `preguntas-plan.md` (solo si hace falta)

Formato canónico en **`CONVENCIONES.md` §5**. Deltas de esta fase:

- Únicamente ambigüedades de **secuenciación o granularidad** que bloqueen el plan.
- Máximo ~8 preguntas.

---

## Fase 2: Merge de respuestas

1. Integrar respuestas en las fases/tareas afectadas.
2. Recalcular dependencias y cobertura si algo cambió.
3. Limpiar pendientes.

---

## Fase 3: Cierre + handoff

Resumen en el chat:

```
✅ Plan de implementación cerrado.

- Fases: F0..FN
- Tareas: M (todas trazadas a RF/endpoint)
- Cobertura: RF-1..RF-K (sin huecos)
- Archivos de control inicializados: seguimiento.md, bitacora.md, herramientas-recomendadas.md
- BACKLOG.md: alcance diferido registrado (RF Should/Could/Won't + deuda técnica + insumos)

Listo para: skill `desarrollo` (ejecuta el plan tarea por tarea).
```

---

## Modo mejora (scoped)

Cuando este skill se invoca dentro del **track de mejoras post-MVP** (orquestado por el skill `mejora`), produce un **plan reducido** para una sola mejora:

- **Escribe en** `documentacion/mejoras/<id>-<slug>/plan.md`, **no** en `05-desarrollo/`.
- **Insumo = los documentos de la mejora** (su `diseno.md`/`especificacion-tecnica.md` si el track los generó) + el baseline del MVP como referencia. En **Track A** (deuda técnica/toggle) el insumo puede ser directamente la fila del backlog + el ADR/contrato existente que referencia.
- El plan suele ser de **una sola fase** con pocas tareas atómicas; mantienen el formato (objetivo · archivos/contratos · depende de · cubre · criterio de done · checkbox) y la trazabilidad al ítem del backlog.
- **No se replican** los 3 archivos de control del MVP: el seguimiento de una mejora se lleva con los checkboxes de su propio `plan.md`; la memoria va al `bitacora.md` global (Origen = ID de la mejora).

---

## Manejo de casos especiales

**Un contrato necesario falta en la spec.**
No inventarlo. Marcar ⚠️ en `preguntas-plan.md`: "Falta en la especificación técnica — volver a esa fase antes de planificar esta tarea."

**El alcance MVP y la spec discrepan.**
Preguntar cuál manda; no asumir.

**Tentación de detallar implementación.**
Resistir: aquí se define qué/orden, no el cómo del código.

---

## Anti-patrones a evitar

- ❌ Escribir código o pseudocódigo de implementación dentro del plan.
- ❌ Reabrir o redefinir tipos/schemas/endpoints/ADRs.
- ❌ Tareas sin RF/contrato asociado (huérfanas) o RF Must sin tarea (hueco).
- ❌ Tareas no verificables ("avanzar el onboarding") en vez de atómicas y con criterio de done.
- ❌ Estimar tiempos/fechas.
- ❌ Ordenar tareas violando sus dependencias.

---

## Output check (definition of done)

- [ ] `plan.md` con fases derivadas del orden de dependencias del MVP.
- [ ] Toda tarea es atómica, trazable a la spec y tiene criterio de done.
- [ ] Matriz de cobertura: todo RF Must aparece en ≥1 tarea.
- [ ] F0 (scaffolding/contratos base) precede a las features.
- [ ] `seguimiento.md`, `bitacora.md` y `herramientas-recomendadas.md` inicializados con su formato.
- [ ] `BACKLOG.md` creado/actualizado con el alcance diferido (nada de scope "perdido").
- [ ] No hay código, ni contratos reabiertos, ni estimaciones.

Si algún check falla, el skill no termina — vuelve a la fase de preguntas.
