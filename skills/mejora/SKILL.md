---
name: mejora
description: 'Front-door del track de mejoras post-MVP: toma un ítem ya priorizado del BACKLOG.md y lo lleva por un mini-ciclo ADAPTATIVO del harness (rigor proporcional al radio de impacto del cambio), en su propia carpeta documentacion/mejoras/<id>-<slug>/. Clasifica el impacto, elige el track (solo plan / +diseño / +contratos / ciclo completo), andamia la carpeta, secuencia los skills de fase necesarios en modo scoped y cierra actualizando el backlog. NO codifica ni reabre los documentos cerrados del MVP. Trigger: el MVP (o un release) está construido y el usuario quiere implementar una mejora, una funcionalidad deseable, deuda técnica o un RF post-MVP del backlog. Slash command sugerido: /mejora <backlog-id>.'
---

# mejora

Track de **iteración post-MVP** del harness Spec-Design (convenciones compartidas: `CONVENCIONES.md` del harness). No es una fase nueva del pipeline lineal: es el **router** que aplica el harness, en versión reducida, a una sola mejora del backlog.

```
… MVP construido … → BACKLOG.md → /mejora <id> (este skill)
                                       │  Fase 0: clasificar impacto → track A/B/C/D
                                       │  Fase 1: andamiar carpeta + secuenciar fases (scoped)
                                       └─► documento-diseno? → especificacion-tecnica? → plan-implementacion → desarrollo
                                                                                                                   └─► BACKLOG.md fila ✅
```

El principio rector es **seguridad proporcional al riesgo**: una mejora trivial no debería pagar la ceremonia de un MVP, y una mejora que toca contratos no debería entrar a código sin spec. Este skill decide cuánto rigor aplica **cada** mejora.

---

## Qué consume (contrato de entrada)

- **`documentacion/BACKLOG.md`** — la cola viva del producto. El ítem a implementar debe existir ahí con un **ID estable** (RF-N para requerimientos; MEJ-{nn} para deuda técnica/otros).
- **Los documentos cerrados del MVP** (`02-diseno/`, `04-especificacion-tecnica/`, etc.) — como **baseline de solo lectura**. Se referencian, **no se reabren**.

> Si el ítem no está en el backlog, **primero se agrega ahí** (no se improvisa una mejora suelta). El backlog es la única puerta de entrada del scope post-MVP.

---

## Qué hace este skill

1. **Clasifica** el impacto de la mejora (Fase 0) y propone un **track**.
2. **Andamia** `documentacion/mejoras/<id>-<slug>/` con solo los documentos que el track requiere.
3. **Secuencia** los skills de fase existentes en **modo mejora (scoped)** según el track.
4. **Cierra** actualizando la fila del backlog y el `INDICE.md`.

## Qué este skill NO hace

- **No escribe código** (eso es `desarrollo`).
- **No reabre** los documentos del MVP ni sus ADRs/contratos — los referencia como baseline.
- **No inventa** un track más liviano de lo que el impacto exige (no saltarse spec si toca contratos).
- **No implementa** una mejora que no esté en el backlog.
- **No duplica** los requerimientos del MVP; los documentos de la mejora son **deltas**.

---

## Fase 0: Clasificar el impacto (elegir el track)

Responder **3 preguntas** sobre el cambio que implica la mejora:

1. **¿Cambia comportamiento visible / UX, o agrega pantallas nuevas?**
2. **¿Cambia tipos, JSON Schema, endpoints o el modelo de datos?** (¿y toca algún ADR/arquitectura?)
3. Si **no** a ambas → es refactor / deuda técnica / toggle sobre contrato existente.

Con eso, proponer el **track** vía `AskUserQuestion` (el usuario confirma o ajusta):

| Track | Cuándo | Documentos en `mejoras/<id>-<slug>/` | Fases que se disparan |
|-------|--------|--------------------------------------|------------------------|
| **A — Mínimo** | deuda técnica, toggle, refactor (no cambia comportamiento ni contratos) | `plan.md` | `plan-implementacion` → `desarrollo` |
| **B — UI** | feature visible, **sin** tocar contratos | `DESIGN.md` (verificar/generar, ver abajo) + `diseno.md` (ligero) [+ `wireframes.md` si hay pantallas nuevas] + `plan.md` | `documento-diseno` → (`wireframes`) → `plan-implementacion` → `desarrollo` |
| **C — Contratos** | toca tipos/schema/endpoints/datos | `diseno.md` + `especificacion-tecnica.md` [+ `diseno-tecnico.md` si toca ADR] + `plan.md` | `documento-diseno` → `especificacion-tecnica` → `plan-implementacion` → `desarrollo` |
| **D — Grande** | feature arquitectónica (ej. multiusuario + auth + DB) | ciclo completo (casi un mini-MVP en su sub-árbol) | el pipeline completo, scoped |

**DESIGN.md (obligatorio en track B):** antes de arrancar el diseño de cualquier mejora visual, verificar que existe `DESIGN.md` en la raíz del repo (sistema de diseño, formato Google Stitch — ver `CONVENCIONES.md` §7). Si no existe, **generarlo leyendo el codebase** (colores, tipografía, espaciado, patrones de componentes) — nunca inventando valores. Al cerrar una mejora visual que cambie tokens o patrones establecidos, actualizarlo.

> Ante la duda entre dos tracks, elegir el **más riguroso**. El costo de un doc de más es bajo; el de un contrato sin definir, alto.

No avanzar sin que el usuario confirme el track.

---

## Fase 1: Andamiar + secuenciar

1. **Crear la carpeta** `documentacion/mejoras/<id>-<slug>/` (slug corto en kebab-case; `<id>` = el ID del backlog: `RF-15`, `MEJ-01`, …).
2. **Marcar la fila del backlog** como 🔵 planificado y enlazarla a la carpeta.
3. **Disparar los skills del track, en orden**, cada uno en **modo mejora (scoped)** (ver la sección "Modo mejora" de cada skill) y en **modo directo** (`CONVENCIONES.md` §9: este orquestador ya tiene su propio gate, así que las Fases 0 de las skills de fase no vuelven a pedir confirmación):
   - El insumo de cada fase es el documento de la fase anterior **de esta mejora** + el baseline del MVP (solo lectura).
   - Cada doc de la mejora es un **delta**: solo los RF/ADR/contratos/tareas que la mejora agrega o cambia.
4. Si un doc de la mejora necesita decisiones del usuario, se usa el patrón `preguntas-*.md` dentro de la carpeta de la mejora (igual que en el pipeline principal).

---

## Gate de revisión — OK del usuario antes de desarrollar

Cuando los documentos del track están listos, **DETENERSE**. No encadenar automáticamente a `desarrollo`: la documentación es el contrato, y el usuario tiene derecho a leerla y aprobarla antes de que empiece el desarrollo autónomo.

1. **Marcar la fila del backlog** como `📝 documentado (espera OK)`.
2. **Presentar un resumen** en el chat con los documentos producidos y sus rutas, para que el usuario los revise:
```
📝 Documentación de la mejora <id> lista para revisión (track <X>).
- Carpeta: documentacion/mejoras/<id>-<slug>/
- Documentos: {diseno.md, especificacion-tecnica.md, plan.md, … según el track}
- Resumen de lo planificado: {2–3 líneas}

Revisa los documentos. Cuando estés conforme, dame el OK para iniciar el desarrollo (o pide ajustes).
```
3. **Esperar el OK explícito.** Esto **no** es `AskUserQuestion` (no es un fork de opciones): es un checkpoint de aprobación; se espera la confirmación del usuario en lenguaje natural. Si pide ajustes, volver a la fase correspondiente del track antes de desarrollar.

> Solo `AskUserQuestion` se usó antes, en la Fase 0, para elegir el track (eso sí es un fork). El gate de aprobación es una parada, no un menú.

---

## Fase 2: Desarrollo (tras el OK)

Una vez que el usuario aprueba, handoff a **`desarrollo`**, que ejecuta el plan **desde la carpeta de la mejora** (no desde `05-desarrollo/plan.md` del MVP). Marcar la fila del backlog como `🟡 en desarrollo`. Las decisiones/issues se anotan en el **`bitacora.md` global** del proyecto, con `Origen = <id de la mejora>` (memoria unificada; no se crea bitácora por mejora).

---

## Fase 3: Cierre

Al terminar el build de la mejora:

1. **`BACKLOG.md`:** subir la fila del ítem a ✅ y, si corresponde, el release asociado.
2. **`INDICE.md`:** reflejar la mejora cerrada (entrada bajo `mejoras/`).
3. **Resumen en el chat:**
```
✅ Mejora <id> implementada (track <X>).
- Carpeta: documentacion/mejoras/<id>-<slug>/
- Documentos producidos: {los del track}
- Backlog: fila <id> → ✅
```

---

## Manejo de casos especiales

**La mejora resulta más grande de lo que parecía (al diseñarla cambia un ADR / arquitectura).**
Re-clasificar hacia arriba (a Track C o D). No seguir con un track liviano si el impacto creció. Anotar el cambio de track en `bitacora.md`.

**La mejora exige cambiar un contrato del MVP.**
No editar el contrato del MVP en silencio. La mejora **extiende** (nuevo tipo/endpoint/campo) y lo documenta como delta; si **rompe** un contrato existente, detenerse y avisar: es una decisión de diseño que debe quedar registrada (posible nuevo ADR en el `diseno-tecnico.md` de la mejora).

**El ítem no está en el backlog.**
Agregarlo primero a `BACKLOG.md` (con su ID y prioridad), y recién entonces promoverlo. El backlog es la puerta de entrada.

**Varias mejoras a la vez.**
Una mejora a la vez (igual que el desarrollo secuencial del MVP). Si son interdependientes, considerarlas un solo release (Track D) o secuenciarlas explícitamente.

---

## Anti-patrones a evitar

- ❌ Codificar la mejora directo desde la línea del backlog, sin pasar por el track.
- ❌ Reabrir/editar los documentos cerrados del MVP (se referencian; las mejoras son deltas).
- ❌ Elegir un track más liviano de lo que el impacto exige (saltarse spec cuando toca contratos).
- ❌ Aplicar el ciclo completo a una mejora trivial (ceremonia innecesaria).
- ❌ Duplicar requerimientos del MVP en los docs de la mejora.
- ❌ Crear una bitácora por mejora (la memoria del proyecto es única: `bitacora.md`).
- ❌ Implementar una mejora que no exista en el backlog.
- ❌ Encadenar a `desarrollo` sin el OK del usuario (saltarse el gate de revisión de documentación).
- ❌ Usar `AskUserQuestion` para el gate de aprobación ("¿está bien la doc?"); es una parada, no un fork.

---

## Output check (definition of done)

- [ ] El ítem existe en `BACKLOG.md` con ID estable.
- [ ] Track clasificado por impacto y **confirmado por el usuario**.
- [ ] Carpeta `mejoras/<id>-<slug>/` creada con **solo** los documentos del track.
- [ ] *(Track B)* `DESIGN.md` verificado/generado antes del diseño, y actualizado si la mejora cambió tokens/patrones.
- [ ] Cada documento de la mejora es un **delta** que referencia (no reabre) el baseline del MVP.
- [ ] **Gate de revisión pasado:** documentación presentada y **OK explícito del usuario** antes de desarrollar.
- [ ] Fila del backlog enlazada a la carpeta y con estado actualizado (⬜→🔵→📝→🟡→✅).
- [ ] Al cerrar: backlog → ✅, `INDICE.md` actualizado, bitácora del proyecto alimentada si hubo algo notable.

Si algún check falla, la mejora no se da por cerrada.
