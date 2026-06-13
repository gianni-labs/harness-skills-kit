# Convenciones del harness Spec-Design

> **Única fuente de verdad de lo compartido entre skills.** Cada SKILL.md referencia este documento y define solo sus propios deltas (máximo de preguntas, bloques sugeridos, etc.). La skill `iniciar-harness` instala una copia en `documentacion/CONVENCIONES.md` del proyecto destino.

---

## 1. Estructura de carpetas (convención fija)

El harness usa **siempre** esta estructura, creada por `/iniciar-harness`. No es configurable: la uniformidad es lo que permite que cada fase encuentre los artefactos de la anterior sin ambigüedad.

```
<raíz del proyecto>/
├── CLAUDE.md                          ← prácticas de trabajo + puntero a INDICE.md
├── DESIGN.md                          ← sistema de diseño (si el proyecto tiene UI; ver §7)
└── documentacion/
    ├── INDICE.md                      ← estado de las fases (✅ por fase cerrada)
    ├── CONVENCIONES.md                ← copia de este documento
    ├── BACKLOG.md                     ← cola viva del producto (desde la fase 5)
    ├── 01-requerimiento/              ← idea cruda + requerimiento-vN.md + preguntas.md
    ├── 02-diseno/                     ← diseno.md [+ diseno-tecnico.md] + preguntas-diseno.md
    ├── 03-wireframes/                 ← wireframes.md + preguntas-wireframes.md (omitida si no hay UI)
    ├── 04-especificacion-tecnica/     ← especificacion-tecnica.md + preguntas-especificacion.md
    ├── 05-desarrollo/                 ← plan.md + seguimiento.md + bitacora.md
    └── mejoras/                       ← INDICE.md + una carpeta <id>-<slug>/ por mejora post-MVP
```

## 2. Pipeline y fases

```
/iniciar-harness → 1 refinar-requerimiento → 2 documento-diseno → 3 wireframes* → 4 especificacion-tecnica → 5 plan-implementacion → 6 desarrollo → código
                                                                                                              └─► BACKLOG.md → /mejora <id> (ciclo post-MVP)
```

\* La fase 3 (`wireframes`) **se omite** si el perfil del proyecto no tiene UI (ver §8).

## 3. IDs estables

| ID | Qué identifica | Vive en |
|----|----------------|---------|
| `RF-N` | Requerimiento funcional | `diseno.md` §2 |
| `RNF-N` | Requerimiento no funcional | `diseno.md` §3 |
| `F-N` | Flujo | `diseno.md` §6 |
| `V-N` | Vista / pantalla | `diseno.md` §7 |
| `ADR-N` | Decisión técnica | `diseno.md` / `diseno-tecnico.md` |
| `F{n}-T{m}` | Tarea del plan (fase n, tarea m) | `plan.md` |
| `B-{nnn}` | Entrada de bitácora del proyecto | `bitacora.md` |
| `MEJ-{nn}` | Ítem del backlog que no es RF (deuda técnica, mejora) | `BACKLOG.md` |

La numeración es **global y continua** dentro de cada tipo; las mejoras post-MVP **continúan** la numeración del MVP (no reinician en RF-1).

**Tareas en planes de mejora:** el `plan.md` de una mejora usa el **mismo formato `F{n}-T{m}`** que el MVP (no `T-01` ni otras variantes), con su propia numeración dentro del plan de la mejora; en la bitácora se referencian con el ID de la mejora como origen (ej. `Origen: MEJ-04 F1-T2`).

## 4. Estados (vocabulario único)

Para fases (`INDICE.md`), tareas (`seguimiento.md`) e ítems del backlog (`BACKLOG.md`):

| Estado | Significado |
|--------|-------------|
| ⬜ | no iniciado / pendiente |
| 🔵 | planificado (solo backlog: ítem promovido a una mejora/release) |
| 📝 | documentado, espera OK del usuario (solo backlog: gate pre-desarrollo) |
| 🟡 | en curso / en desarrollo |
| ✅ | hecho |
| ⛔ | bloqueado / descartado |

Ciclo de un ítem del backlog: `⬜ → 🔵 → 📝 → 🟡 → ✅` (el paso 📝→🟡 exige OK explícito del usuario).

## 5. Patrón preguntas → merge

Toda fase de diseño que necesite decisiones del usuario produce un archivo `preguntas-*.md` **separado del artefacto** (nunca preguntas incrustadas en el documento). Principio rector: **preguntar, no inventar** — si falta información, se pregunta; no se rellena el hueco con algo plausible.

**Formato del archivo:**

```markdown
# Preguntas para [fase]

Responde debajo de cada una (`R:`) y después hacemos merge a [artefacto].

---

## Sobre [bloque temático del proyecto]

**N. [pregunta clara y específica]**
[Contexto de POR QUÉ se pregunta — 1–2 líneas.]
Opciones (cuando aplique):
- (a) ...
- (b) ...

R:
```

**Reglas:**
- Bloques temáticos **del propio proyecto** (no genéricos tipo "Funcionales / No funcionales").
- **Numeración global continua** (no se resetea por bloque).
- Cada pregunta lleva **contexto** (por qué importa).
- Opciones **(a)(b)(c)** cuando la decisión es discreta; pregunta abierta cuando no lo es.
- Bloque final **"Fuera de scope (confirmar)"** con asunciones negativas ("asumo que no — confirmar") cuando aplique.
- Máximo de preguntas por fase: lo define cada skill (gradiente decreciente a medida que avanza el pipeline).

**Merge:** cuando el usuario responde, la skill clasifica cada respuesta (integración directa / deseable post-MVP / fuera de scope / pendiente), la integra **reformulada en el tono del documento** (no copia literal), limpia la sección de pendientes, y conserva el archivo de preguntas como registro hasta el cierre de la fase. Respuestas que abren hilos nuevos → máximo 3–4 preguntas de seguimiento en el chat.

**Archivado al cerrar (automático, sin preguntar):** al cerrar la fase, los **archivos intermedios** —el `preguntas-*.md` ya respondido y mergeado, y el documento de entrada que quedó reemplazado por una versión `-vN` o por el artefacto final— se **mueven** a un subdirectorio `_archivo/` dentro de la carpeta de la fase (ej. `02-diseno/_archivo/preguntas-diseno.md`). Default conservador: **archivar, no borrar** (quedan como historial, fuera del camino). La skill **no pregunta** al usuario qué hacer con ellos — aplica esta convención. El artefacto vivo de la fase (el documento final) permanece en su lugar. Avanzar a la siguiente fase sí sigue siendo decisión del usuario (se ofrece, no se encadena solo).

## 6. Gate de aprobación humano ≠ AskUserQuestion

Dos mecanismos distintos; confundirlos es un anti-patrón:

- **`AskUserQuestion`** — para **forks reales de decisión** entre opciones concretas (ej. elegir track de una mejora). Nunca para "¿está bien el documento?" o "¿procedo?".
- **Gate de aprobación** — una **parada**: la skill presenta lo producido, marca el estado correspondiente (ej. `📝`) y **espera el OK del usuario en lenguaje natural**. Se usa siempre antes de que documentación fluya hacia desarrollo autónomo. No es un menú de opciones.

## 7. DESIGN.md (sistema de diseño)

En proyectos con UI, `DESIGN.md` en la raíz del repo codifica la identidad visual real (colores, tipografía, espaciado, patrones de componentes), en el formato abierto de Google Stitch. Se genera **leyendo el codebase, nunca inventando valores**. Se crea/verifica al iniciar la primera mejora visual (track B de `/mejora`) y se actualiza al cerrar cualquier mejora que cambie tokens o patrones. Opcionalmente se genera al cerrar el MVP.

**Expectativa visual del MVP:** la UI del MVP es **base funcional y limpia, no identidad de marca** — es una decisión, no un límite. El pulido visual (tipografía distintiva, paleta propia, micro-interacciones) es un **track B post-MVP** que usa `DESIGN.md` para subir el nivel sin rehacer. Comunicarlo así al usuario para que la simpleza inicial se lea como etapa, no como carencia.

## 8. Perfil del proyecto

Lo captura `/iniciar-harness` (o `refinar-requerimiento`, si el proyecto arrancó sin init) y queda registrado al inicio del requerimiento cerrado:

```markdown
**Perfil del proyecto:** UI: sí/no · LLM: sí/no · API/backend: sí/no · [otras capacidades relevantes]
**Git:** respaldo local en gates: sí/no  (si sí: `git commit` local al cerrar cada fase, acotado a `documentacion/`, sin remoto ni push)
```

La **política de git** es opt-in (se elige en `/iniciar-harness`); si está activa, los cierres de fase de `desarrollo` hacen un commit **local** de la documentación. El harness **nunca** crea repos remotos, configura remotos ni hace `push` — eso es del usuario.

Las skills lo consumen:
- `wireframes` — se **omite** si UI: no.
- `especificacion-tecnica` — activa/desactiva secciones condicionales (contrato LLM, etc.).

## 9. Fase 0 y modo directo

Toda skill de fase abre con una **Fase 0** (confirmar fuentes y enfoque en un solo mensaje, no empezar sin confirmación). Excepciones — la Fase 0 se reduce a "anunciar fuentes y proceder", sin parada:

- Cuando la skill es invocada **por el orquestador `mejora`** (que ya tiene su propio gate de aprobación).
- Cuando el usuario lo pide explícitamente (argumento `directo`, ej. `/documento-diseno <archivo> directo`).

Los gates de riesgo (OK pre-desarrollo, gates de cierre de fase en `desarrollo`) **nunca** se saltan.

## 10. Esqueleto estándar de una skill de fase

Toda skill de fase mantiene estas secciones (su orden y presencia es parte del contrato del kit):

1. Diagrama de posición en el pipeline (§2).
2. **Qué consume** (contrato de entrada) / **Qué hace** / **Qué NO hace**.
3. **Fase 0** (aclaraciones iniciales, con modo directo §9).
4. **Fase 1** (generar artefacto + preguntas).
5. **Fase 2** (merge de respuestas, §5).
6. **Fase 3** (cierre + handoff + actualizar `INDICE.md`).
7. **Modo mejora (scoped)** — comportamiento delta dentro del track de `/mejora`.
8. **Manejo de casos especiales**.
9. **Anti-patrones a evitar**.
10. **Output check** (definition of done; si un check falla, la skill no termina).

## 11. Reglas de oro (separación por altitud)

- Una decisión vive en **una sola fase** (la fase dueña); los cambios fluyen top-down. No se reabre una decisión desde una fase posterior: se vuelve a la fase dueña.
- `wireframes` no define stack. `especificacion-tecnica` no reabre ADRs. `plan-implementacion` no escribe código ni estima. `desarrollo` no re-planifica ni re-decide. `mejora` no codifica ni reabre los docs del MVP.
- Herramientas del entorno (`code-review`, `security-review`, `verify`, `run`, etc.) son **opcionales**: si no están disponibles, el fallback es lint/build/tests manuales y revisión propia del diff.
- Todas: **preguntar, no inventar**.
