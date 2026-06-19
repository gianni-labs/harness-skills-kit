# Manual del harness Spec-Design

> Este manual explica el **porqué** del harness (principios, fases, ciclo de vida). El **formato** de lo compartido (rutas, IDs, estados, patrones) vive en `skills/_harness/CONVENCIONES.md`, que se instala en cada proyecto como `documentacion/CONVENCIONES.md`.

---

## 1. Qué es

Un **harness de desarrollo asistido por IA**: una cadena de *skills* donde cada fase produce un artefacto con **contrato fijo** que la siguiente fase consume sin ambigüedad. El objetivo es ir de una idea cruda a código de forma **ordenada, trazable y segura**, separando el pensamiento por niveles de abstracción ("altitud") para no mezclar decisiones de distinto tipo.

Principios:

- **Una fase = un skill = un artefacto handoff-ready.** Cada skill sabe qué consume y qué produce.
- **Separación por altitud.** Requerimiento (qué se quiere) ≠ diseño (cómo, alto nivel) ≠ contratos (tipos/APIs exactos) ≠ plan (orden) ≠ código.
- **Fuente de verdad y flujo top-down.** Una decisión vive en una sola fase; los cambios fluyen hacia abajo. No se reabren decisiones cerradas desde una fase posterior — se vuelve a la fase dueña.
- **Preguntar, no inventar.** Cuando falta info, cada skill genera un archivo `preguntas-*.md` y espera respuestas; no rellena huecos por su cuenta.
- **Memoria.** Una bitácora viva registra decisiones/issues/aprendizajes con referencia cruzada.
- **Genérico por diseño.** El harness no asume dominio ni tipo de app: el **perfil del proyecto** (UI/LLM/API) activa o desactiva fases y secciones.

---

## 2. Las fases

El arranque es `/iniciar-harness` (no es una fase: crea la estructura, instala las plantillas y captura el **perfil del proyecto**). Después:

| # | Fase | Skill | Consume | Produce |
|---|------|-------|---------|---------|
| 1 | Requerimiento | `refinar-requerimiento` | idea/notas en bruto | `requerimiento-vN.md` + `preguntas.md` |
| 2 | Diseño | `documento-diseno` | requerimiento cerrado | `diseno.md` + `diseno-tecnico.md` + `preguntas-diseno.md` |
| 3 | Wireframes* | `wireframes` | §Vistas + §Flujos del diseño | `wireframes.md` (ASCII + mapa nav) + `preguntas-wireframes.md` |
| 4 | Especificación técnica | `especificacion-tecnica` | diseño + wireframes | `especificacion-tecnica.md` (tipos, APIs, módulos; secciones condicionales por perfil) + preguntas |
| 5 | Plan de implementación | `plan-implementacion` | especificación + alcance MVP | `plan.md` (Fases→Tareas) + inicializa control + `BACKLOG.md` |
| 6 | Desarrollo | `desarrollo` | plan + contratos | código + mantiene los archivos de control |

\* La fase 3 **se omite** si el perfil del proyecto no tiene UI (CLI, API, librería) — queda "— omitida" en `INDICE.md`.

```
/iniciar-harness → idea → 1 refinar → 2 diseño → 3 wireframes* → 4 spec-técnica → 5 plan → 6 desarrollo → código
```

### Cómo se invoca
Cada fase tiene un slash command: `/refinar-requerimiento`, `/documento-diseno`, `/wireframes`, `/especificacion-tecnica`, `/plan-implementacion`, `/desarrollo`. Los SKILL.md viven en `.claude/skills/`.

> Hay un skill más, `mejora` (`/mejora <id>`), que **no es una fase lineal**: es el router del **ciclo de vida post-MVP**. Reaplica las fases anteriores, en versión reducida, a un ítem del backlog (ver §6). El pipeline lineal de arriba construye el primer release; `mejora` itera sobre él.

### Metodologías por fase
- **RF** (requerimientos funcionales): User Story + criterios **EARS**, priorización **MoSCoW**.
- **RNF**: **FURPS+**.
- **Decisiones técnicas**: **ADR mini** (Contexto/Decisión/Alternativas/Consecuencias/Estado).
- **Vistas (V-N)** y **Flujos (F-N)**: formato fijo (contrato con wireframes).

---

## 3. Convenciones transversales

El detalle vive en `CONVENCIONES.md`; lo esencial:

- **IDs estables:** `RF-N`, `RNF-N`, `F-N` (flujo), `V-N` (vista), `ADR-N`, `F{n}-T{m}` (tarea), `B-{nnn}` (bitácora), `MEJ-{nn}` (ítem no-RF del backlog).
- **Estados (vocabulario único):** `⬜ → 🔵 → 📝 → 🟡 → ✅` (+ `⛔`). El paso 📝→🟡 es el gate de OK del usuario.
- **Patrón preguntas → merge:** cada fase de diseño produce un `preguntas-*.md` con bloques temáticos, numeración global, contexto y campo `R:`. El usuario responde; el skill hace *merge* al documento y conserva el archivo de preguntas como registro.
- **Archivado automático:** al cerrar una fase, los intermedios (`preguntas-*.md` respondido, entrada reemplazada) se mueven solos a un `_archivo/` en la carpeta de la fase — sin preguntar.
- **Git opt-in y local:** respaldo versionado de la documentación si el usuario lo activa en el onboarding; commit local en los gates, nunca remoto ni push.
- **AskUserQuestion** solo para forks reales de decisión; un **gate de aprobación** ("dame el OK") es una parada, no un menú — nunca usa AskUserQuestion.
- **Fase 0 y modo directo:** cada skill confirma fuentes antes de empezar, salvo cuando la invoca el orquestador `mejora` o el usuario pasa `directo`.
- **Estados de cierre:** cada fase se marca ✅ en `documentacion/INDICE.md`.
- **Herramientas opcionales:** `code-review`, `verify`, etc. dependen del entorno; sin ellas, el fallback es verificación manual. Los gates nunca se saltan.

---

## 4. Archivos de control (fase 6)

En `documentacion/05-desarrollo/`, inicializados por `plan-implementacion` desde las plantillas del kit (`_harness/templates/`):

- **`plan.md`** — Fases → Tareas (atómicas, trazadas a RF/endpoint, con criterio de done).
- **`seguimiento.md`** — estado del avance (barra + tabla fase/tareas). Minimalista.
- **`bitacora.md`** — memoria **del proyecto**: decisiones/issues/aprendizajes con referencia cruzada (`B-nnn`, Origen, Refs, Por).

> Las herramientas opcionales del entorno (`code-review`, `verify`, etc.) se sugieren en los gates de `desarrollo`, no en un archivo de control aparte.

---

## 5. Cómo usar el harness en un proyecto nuevo

1. Corre `/iniciar-harness` — crea la estructura, instala plantillas y captura el perfil del proyecto.
2. Coloca tu idea en un `.md` dentro de `documentacion/01-requerimiento/` y corre `/refinar-requerimiento <archivo>`.
3. Avanza fase por fase; responde los `preguntas-*.md`; deja que cada skill haga el merge.
4. En la fase 5, `/plan-implementacion` genera el plan y los archivos de control.
5. `/desarrollo` ejecuta el plan tarea por tarea, verificando en cada fase. Al cerrar el release, el alcance diferido ya quedó en `BACKLOG.md`.
6. **Después del MVP**, para cada mejora corre `/mejora <id>` (§6): el harness aplica el rigor proporcional al impacto y mantiene el backlog actualizado.

---

## 6. Ciclo de vida post-MVP: track de mejoras

El pipeline lineal (§2) construye el **primer release** (MVP). Lo que queda fuera vive en `documentacion/BACKLOG.md` —la **cola viva del producto**— y se itera con el **track de mejoras adaptativo** (skill `mejora`), para que el harness no termine en el MVP.

```
PRIMER RELEASE:  idea → … → desarrollo → código
                                   └─► BACKLOG.md (alcance diferido: Should/Could/Won't, deuda, pendientes de prod, insumos)

ITERACIÓN:       BACKLOG.md → /mejora <id> → [clasificar impacto] → track A/B/C/D
                                  └─► (solo las fases necesarias, scoped, en mejoras/<id>-<slug>/)
                                        → desarrollo → código → BACKLOG.md fila ✅
```

**Rigor adaptativo (proporcional al radio de impacto).** El skill `mejora` clasifica cada ítem y elige cuánto del harness aplicar:

| Track | Cuándo | Documentos (en `mejoras/<id>-<slug>/`) |
|-------|--------|----------------------------------------|
| **A — Mínimo** | deuda técnica, toggle, refactor | `plan.md` |
| **B — UI** | feature visible sin tocar contratos | `DESIGN.md` (verificar/generar si no existe) + `diseno.md` [+ `wireframes.md`] + `plan.md` |
| **C — Contratos** | toca tipos/schema/endpoints/datos | `diseno.md` + `especificacion-tecnica.md` [+ `diseno-tecnico.md`] + `plan.md` |
| **D — Grande** | feature arquitectónica | ciclo completo (mini-MVP) |

**DESIGN.md (track B):** antes de arrancar cualquier mejora visual, verificar que `DESIGN.md` existe en la raíz del repo. Si no existe, generarlo leyendo el codebase (colores, tipografía, espaciado, patrones de componentes) — nunca inventando valores. Formato: [Google Stitch DESIGN.md spec](https://stitch.withgoogle.com/docs/design-md/overview). Lo leen automáticamente los agentes de IA antes de generar UI, produciendo componentes coherentes con la identidad visual real del producto.

**Principios del track:**
- Cada mejora vive en **su propia carpeta**; los docs son **deltas** que **referencian** el baseline del MVP (no lo reabren).
- Los skills de fase tienen un **modo mejora (scoped)** que produce artefactos reducidos en esa carpeta.
- **Gate de revisión humano:** producida la documentación, el ítem pasa a `📝 documentado (espera OK)` y `mejora` **se detiene** — el desarrollo autónomo no arranca sin el **OK explícito del usuario**.
- **Memoria unificada:** las decisiones de una mejora van al `bitacora.md` global (Origen = ID de la mejora). No se crea bitácora por mejora.
- El backlog es la **única puerta de entrada** del scope post-MVP: nada se codifica sin estar ahí.
- **Estado del ítem:** ⬜ → 🔵 → 📝 → 🟡 → ✅ (el paso 📝→🟡 es el gate de OK).

---

## 7. Reglas de oro (heredadas de cada skill)

- `wireframes` **no define stack** (solo layout/navegación).
- `especificacion-tecnica` **no reabre ADRs** (los respeta).
- `plan-implementacion` **no escribe código** ni estima tiempos.
- `desarrollo` **no re-planifica ni re-decide**; si un contrato cambia, vuelve a la fase dueña.
- `mejora` **no codifica ni reabre los docs del MVP**; clasifica el impacto y aplica el rigor proporcional.
- `iniciar-harness` **no escribe contenido de fases** (solo estructura y plantillas) y **no pisa nada existente** sin avisar.
- **Git es opt-in y local:** si el usuario lo activa, el harness hace `git commit` local de la documentación en los gates; **nunca** crea repos remotos ni hace `push` (el remoto y el deploy son del usuario).
- Todas: **preguntar, no inventar**.

---

## 7.bis Recursos de calidad transversales

El harness organiza el *flujo* (qué fase, en qué orden, con qué contratos); la *cobertura de calidad horizontal* (seguridad, accesibilidad, performance, testing) vive como **recursos opcionales** que las fases consultan, no como fases nuevas:

- **Referencias** (`skills/_harness/referencias/`): checklists desacopladas que las fases cargan bajo demanda — `documento-diseno` para no olvidar dimensiones de RNF, `especificacion-tecnica` para el contrato de validación, `desarrollo` al construir y verificar, `wireframes` para no diseñar barreras de accesibilidad, y `pre-produccion` para cobrar la deuda diferida antes de soltar a prod. Condicionadas por perfil (la de accesibilidad solo con UI; la de pre-producción solo si va a prod). Detalle en `CONVENCIONES.md §12`.

  **Por qué un gate y no una skill.** La deuda operativa de prod (config por env var, datos de prueba, mocks) no es una *feature diferida*: es un atajo que hay que deshacer. Registrarla es una captura de una línea, no un ciclo con preguntas/merge/gate, así que **no amerita una skill**. Y mantenerla en el `BACKLOG.md` —no en un archivo paralelo— preserva la regla de "única fuente del scope diferido" que hace que `mejora` sepa siempre dónde mirar. La subsección "Pendientes para producción" le da identidad propia (columna `Bloquea prod = sí/no`) sin fragmentar la fuente; `pre-produccion.md` es el momento en que esos pendientes se cobran.
- **Personas de revisión** (`skills/_harness/agentes/`): `revisor-codigo` (5 ejes) y `auditor-seguridad` (OWASP) que `desarrollo` usa en los gates para una revisión adversarial con contexto fresco, en vez de auto-revisarse. Son prompts de referencia: si el entorno las expone como sub-agentes invocables se usan así, si no la grilla se aplica manualmente. Opcionales con fallback manual. Detalle en `CONVENCIONES.md §13`.
- **Anti-racionalización:** además de "Qué NO hace" y "anti-patrones", cada skill de fase trae una tabla `Excusa → Realidad` que desarma los atajos típicos del modelo en *esa* fase (esqueleto en `CONVENCIONES.md §10`).

Ninguno de estos recursos reabre la separación por altitud: se subordinan a una fase existente o se consultan como referencia. El núcleo (contratos, preguntas→merge, gates, post-MVP, trazabilidad) no cambia.

---

## 8. Auto-mejora del harness (opcional)

Si un proyecto quiere registrar fricciones/mejoras **del harness mismo** (no del producto), crea un `MEJORAS-HARNESS.md` en una carpeta meta (ej. `harness/`). El skill `desarrollo` lo detecta por convención y registra ahí las observaciones sobre el flujo/skills, separadas de la bitácora del proyecto (formato `M-{nnn}`, ver la sección "Dogfooding del harness" de `desarrollo`). Si el archivo no existe, el mecanismo queda inactivo.
