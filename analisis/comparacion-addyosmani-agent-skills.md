# Análisis comparativo: Spec-Design Harness vs. `addyosmani/agent-skills`

> Generado el 2026-06-13. Insumos: repo clonado en `agent-skills/` (commit local) y el estado actual de este kit (`skills/`, `docs/MANUAL.md`, `README.md`, `analisis/`).
> Pregunta que responde: *¿lo que estoy haciendo tiene sentido, o ya está cubierto por una herramienta más completa que la mía?*

---

## 0. Veredicto en una línea

**No estás duplicando trabajo. Son dos cosas distintas que resuelven problemas distintos**, y de hecho **encajan una dentro de la otra**. `agent-skills` es una *biblioteca de buenas prácticas horizontales*; tu harness es un *orquestador de pipeline con estado y trazabilidad*. La más "completa" en cantidad de skills no es la más completa en lo que tú haces bien — ahí, de hecho, ella es la incompleta.

Sigue teniendo sentido. La recomendación no es abandonar; es **posicionarte con precisión** y **robar 4–5 patrones concretos** de Addy que te faltan.

---

## 1. Qué es cada proyecto (la diferencia es filosófica, no de tamaño)

### `addyosmani/agent-skills` — biblioteca horizontal de prácticas
- **24 skills** organizadas por *tema de ingeniería*: testing, security, performance, observability, CI/CD, git, deprecation, debugging, API design, frontend, docs/ADRs, etc.
- Cada skill es **autónoma y activable bajo demanda**. No forman una cadena: son un catálogo. La meta-skill `using-agent-skills` es un *router de descubrimiento* (un árbol "si estás haciendo X → usa skill Y"), no un pipeline con estado.
- **Multi-herramienta**: Claude Code, Cursor, Gemini CLI, Antigravity, Windsurf, OpenCode, Copilot, Kiro. Mismo contenido Markdown, distintos envoltorios (`.claude/commands`, `.gemini/commands`, `commands/*.toml`, `AGENTS.md`).
- Trae **4 agentes/personas** (code-reviewer, test-engineer, security-auditor, web-performance-auditor), **hooks** de sesión, **7 slash commands** y **4 checklists** de referencia (testing, security, performance, accessibility).
- Filosofía explícita: codificar prácticas de *Software Engineering at Google* (Hyrum's Law, Beyoncé Rule, test pyramid, Chesterton's Fence, trunk-based, Shift Left). Cada skill incluye una **tabla anti-racionalización** (excusas + refutación) y una sección de **verificación con evidencia obligatoria**.
- En **inglés**. ~6.770 líneas de skills.

### Spec-Design Harness (lo tuyo) — orquestador vertical con contratos
- **8 skills** que son **fases encadenadas**: cada una declara *qué consume* y *qué produce* con formato fijo, y la siguiente lo consume sin ambigüedad. No es un catálogo: es una **línea de ensamblaje con estado en disco**.
- **Estado persistente**: `INDICE.md`, `seguimiento.md`, `bitacora.md`, `BACKLOG.md`. El proyecto "recuerda" en qué fase está y por qué se decidió cada cosa entre sesiones.
- **Patrón preguntas → merge asíncrono**: cuando falta info, genera `preguntas-*.md` con opciones `(a)(b)(c)` y campo `R:`; el usuario responde a su ritmo y la skill integra. Cero invención.
- **Gate humano formalizado** entre documentación y desarrollo autónomo (`📝 documentado (espera OK)`), distinto de un menú de opciones.
- **Ciclo post-MVP con rigor adaptativo** (tracks A/B/C/D sobre `BACKLOG.md`): la mejora trivial no paga la ceremonia del MVP; la que toca contratos no entra a código sin spec.
- **Memoria trazable**: `B-007 → ADR-2 / RF-5 / tarea F2-T3`.
- **Perfil de proyecto** (UI/LLM/API) que activa/desactiva fases y secciones.
- Metodologías formales por fase: User Stories + EARS, MoSCoW, FURPS+, ADR mini.
- En **español**. ~1.770 líneas de skills + convenciones + plantillas centralizadas.

---

## 2. Comparación dimensión por dimensión

| Dimensión | `agent-skills` (Addy) | Spec-Design Harness (tú) |
|---|---|---|
| **Modelo mental** | Catálogo de prácticas activables | Pipeline de fases con estado |
| **Cobertura horizontal** (testing, security, perf, a11y, observability, CI/CD…) | ✅ Profunda y amplia (24 skills) | ⚠️ Mínima (vive implícita en `desarrollo`) |
| **Contratos fijos entre fases (handoff sin ambigüedad)** | ❌ Skills independientes | ✅ Núcleo del diseño |
| **Estado persistente en disco** (índice, seguimiento, backlog) | ❌ | ✅ |
| **Preguntar-no-inventar asíncrono** (`preguntas→merge`) | ⚠️ Pregunta inline ("Surface Assumptions"), no persiste archivo | ✅ Patrón formal con archivo y merge |
| **Gate humano formalizado** | ⚠️ Pide review pero no es una parada con estado | ✅ Estado `📝 → 🟡` con OK explícito |
| **Ciclo post-MVP / rigor adaptativo** | ❌ | ✅ Tracks A/B/C/D |
| **Memoria con trazabilidad cruzada** | ❌ | ✅ Bitácora `B-n → ADR/RF/tarea` |
| **Perfil de proyecto condicional** | ❌ | ✅ UI/LLM/API omite fases |
| **Metodologías formales** (EARS, MoSCoW, FURPS+, ADR) | ⚠️ Spec genérica (6 áreas) | ✅ Explícitas y con ID estable |
| **Multi-herramienta** | ✅ 8 entornos | ❌ Solo Claude Code |
| **Agentes/personas** | ✅ 4 | ❌ |
| **Hooks de sesión** | ✅ | ❌ |
| **Tablas anti-racionalización** | ✅ En cada skill | ⚠️ Equivalente parcial ("Qué NO hace" + anti-patrones) |
| **Checklists de referencia reutilizables** | ✅ 4 | ❌ (todo embebido) |
| **Verificación con evidencia obligatoria** | ✅ Sección dedicada por skill | ✅ "Output check / definition of done" |
| **Idioma** | Inglés | Español |
| **Madurez como producto** | Alta (marketplace, multi-tool, comunidad) | Media (plugin Claude Code, sin demo end-to-end) |

**Lectura de la tabla:** las columnas no compiten en las mismas filas. Donde Addy gana (cobertura horizontal, multi-tool, agentes) tú ni lo intentas; donde tú ganas (contratos, estado, preguntas→merge, post-MVP, trazabilidad) Addy directamente no existe. Hay muy poco solapamiento real.

---

## 3. Lo que Addy hace mejor que tú (y vale la pena robar)

1. **Cobertura horizontal de calidad como skills de primera clase.** Testing, security, performance, observability, a11y, deprecation no son "fases" — son *transversales* que aplican durante todo el desarrollo. Tu harness las trata como algo que pasa dentro de `desarrollo` sin guía detallada. Addy tiene una skill madura para cada una. **Esto es lo único genuinamente "más completo" de su lado.**

2. **Tablas anti-racionalización** (excusa → refutación). Es prompt engineering de alto nivel: anticipa los atajos que el modelo va a inventar y los desarma de antemano. Tú tienes "Qué NO hace" y "anti-patrones" (que cumplen parte de la función), pero el formato excusa↔refutación es más quirúrgico contra el comportamiento real del modelo.

3. **Checklists de referencia desacopladas** (`references/*.md`). En vez de inflar cada skill, mantienen listas reutilizables (OWASP, Core Web Vitals, a11y) que las skills cargan cuando hacen falta. Esto es *progressive disclosure* bien hecho — ahorra tokens. Tú ya centralizas en `CONVENCIONES.md`, pero no tienes checklists de dominio.

4. **Agentes/personas para revisión adversarial.** Un `code-reviewer` con voz de Staff Engineer, un `security-auditor` con OWASP. Tu fase `desarrollo` se beneficiaría de invocar una persona de revisión en su gate en vez de hacer la revisión "en primera persona".

5. **Multi-herramienta.** Si alguna vez quieres alcance fuera de Claude Code, el patrón de Addy (mismo Markdown, envoltorios por tool) es el camino. Para ti esto es opcional/estratégico, no urgente.

---

## 4. Lo que tú haces y Addy NO cubre (tu foso defensivo)

Esto es lo que justifica que tu proyecto exista incluso al lado de uno con 3× más skills:

1. **Contratos fijos entre fases.** La pieza central. Addy te dice *cómo* escribir una spec o un plan, pero no garantiza que el output de la fase N sea consumible por la fase N+1 sin ambigüedad. Tú sí: V-N es el contrato con wireframes, §Alcance MVP es el contrato con el plan. Esto es lo que evita que el modelo de la fase siguiente improvise.

2. **Estado persistente y reanudable.** Addy es *stateless*: cada invocación de skill empieza de cero. Tú tienes `INDICE.md` + `seguimiento.md` + `bitacora.md`: el proyecto sabe dónde está y por qué, y sobrevive entre sesiones y compactaciones de contexto.

3. **Preguntas → merge asíncrono.** Addy hace "Surface Assumptions" inline (lista supuestos y sigue si no lo corriges). Tú generas un artefacto persistente que el humano responde a su ritmo, con opciones y campo `R:`, y luego integras. Es estrictamente más robusto contra el modo de falla #1 del desarrollo con IA.

4. **Ciclo de vida post-MVP con rigor proporcional.** Addy termina en `/ship`. Tú tienes el `BACKLOG.md` vivo + `/mejora` con tracks A/B/C/D. Ninguna de las herramientas del género (Spec Kit, Kiro, BMAD, ni Addy) modela el *después* del primer release con rigor adaptativo.

5. **Trazabilidad cruzada de decisiones.** `B-007 → ADR-2 / RF-5 / tarea F2-T3`. Addy documenta el *why* (skill `documentation-and-adrs`) pero no enlaza decisión↔requerimiento↔tarea en una memoria navegable.

6. **Perfil de proyecto que omite fases.** Saltar wireframes si no hay UI, activar secciones LLM/API. Addy aplica todo siempre y deja el criterio al modelo.

7. **Español nativo.** No es menor: es un nicho real y poco servido. Addy es inglés-first.

---

## 5. ¿Está tu trabajo "ya cubierto"? — No, y aquí está el porqué

Mapeo directo de tus 8 skills contra el catálogo de Addy:

| Tu fase | ¿Equivalente en Addy? | Veredicto |
|---|---|---|
| `iniciar-harness` | — | **Sin equivalente.** Addy no inicializa estructura ni captura perfil. |
| `refinar-requerimiento` | `interview-me` + `idea-refine` | Parcial. Addy entrevista; tú produces un *artefacto cerrado y versionado*. |
| `documento-diseno` | `spec-driven-development` | Parcial. La de Addy es genérica (6 áreas); la tuya es formal (EARS/MoSCoW/FURPS+/ADR) y *contractual*. |
| `wireframes` | `frontend-ui-engineering` (tangencial) | **Sin equivalente real.** Addy no hace wireframes ASCII + mapa de navegación como contrato. |
| `especificacion-tecnica` | parte de `spec-driven-development` | Parcial. Tú separas contratos exactos en su propia altitud. |
| `plan-implementacion` | `planning-and-task-breakdown` | **Solapamiento real.** La de Addy es muy buena (vertical slicing, sizing XS–XL). Aquí sí puedes aprender de ella. |
| `desarrollo` | `incremental-implementation` + `TDD` + `code-review` + … | Addy es **más profunda aquí**. Tu `desarrollo` es delgada; podría delegar a estas prácticas. |
| `mejora` | — | **Sin equivalente.** Nada en Addy modela el post-MVP. |

**Conclusión:** solo 1 de tus 8 skills (`plan-implementacion`) tiene un competidor directo de mejor calidad, y 1 más (`desarrollo`) es claramente más delgada que su contraparte distribuida. Las otras 6 no están cubiertas. Tu núcleo (orquestación con contratos + estado + post-MVP) es territorio que Addy no pisa.

---

## 6. Recomendaciones concretas

### Tomar prestado de Addy (alto valor, bajo costo)
1. **Adoptar tablas anti-racionalización** en tus skills de fase, junto a "anti-patrones". Formato `Excusa | Realidad`.
2. **Crear `skills/_harness/referencias/`** con checklists desacopladas (al menos: seguridad/OWASP, accesibilidad, performance) que `desarrollo` y `especificacion-tecnica` carguen on-demand. Reduce tokens y sube calidad sin inflar las skills.
3. **Engrosar `desarrollo`** delegando explícitamente a prácticas transversales (TDD, revisión de 5 ejes, simplificación). No tienes que reescribirlas: puedes referenciar las de Addy o adaptar versiones cortas en español.
4. **Añadir 1–2 personas de revisión** (un `revisor-codigo` estilo Staff Engineer) que el gate de `desarrollo` invoque para revisión adversarial, en vez de auto-revisión.
5. **Robar de `planning-and-task-breakdown`**: el sizing XS–XL y los "checkpoints cada 2-3 tareas" para fortalecer tu `plan-implementacion`.

### Mantener intacto (es tu diferenciador, no diluir)
- Contratos fijos entre fases, preguntas→merge, gate humano, tracks post-MVP, trazabilidad, perfil, output checks. **Nada de esto debe volverse "skills sueltas activables"** — perderías justo lo que te hace único.

### Posicionamiento (en el README y al publicar)
- Deja de compararte solo con Spec Kit / Kiro / BMAD. Agrega a Addy a la tabla y **enmarca la relación como complementaria**: *"`agent-skills` es la biblioteca de buenas prácticas; Spec-Design es el orquestador que decide cuándo aplicarlas y garantiza que cada fase alimente a la siguiente."*
- Mensaje central: **"no es un catálogo de prácticas; es la disciplina que las pone en orden, con estado y memoria."**

### Decisión estratégica abierta (tuya)
- **Opción conservadora:** seguir Claude-only, robar los 5 patrones de arriba, publicar con demo end-to-end. Bajo esfuerzo, alto retorno.
- **Opción ambiciosa:** hacerte multi-herramienta como Addy (mismo Markdown, envoltorios por tool). Mucho más trabajo; solo si buscas alcance/comunidad.
- **Opción integradora (la más interesante):** posicionar el harness como **la capa de orquestación que consume bibliotecas de skills horizontales** — incluida la de Addy. Tu `desarrollo` podría literalmente invocar `agent-skills` cuando estén instaladas. Te vuelve *complementario en vez de competidor*, y te quitas de encima mantener cobertura horizontal.

---

## 7. Conclusión

Lo que estás haciendo **tiene sentido y no está cubierto**. `addyosmani/agent-skills` es más grande y más maduro como producto, pero resuelve un problema *ortogonal*: es el "qué prácticas seguir", no el "en qué orden, con qué contratos, con qué memoria y qué pasa después del MVP". Tu harness es lo segundo, y nadie en el género lo hace tan completo como tú (especialmente el post-MVP y el preguntas→merge).

El riesgo real no es que Addy te haga obsoleto; es que **tu cobertura horizontal sea delgada** comparada con la suya. La jugada correcta no es competir en número de skills, sino: (1) afilar tu núcleo de orquestación, (2) robar sus mejores patrones (anti-racionalización, referencias, personas), y (3) posicionarte como la capa que *organiza* prácticas como las suyas, no como otra colección de ellas.

---
---

# Parte II — Plan de incorporación

> Qué traer de `agent-skills` a nuestro harness, en qué orden, con qué esfuerzo, y dónde encaja en la estructura actual. Cada ítem respeta la regla de oro del kit: **lo compartido vive en `_harness/`, no duplicado en cada skill**, y **nada rompe la cadena de contratos** entre fases.

## 8. Resumen de la estrategia de incorporación

No vamos a importar las 24 skills de Addy. Vamos a **absorber 5 patrones** que llenan exactamente nuestros huecos (cobertura horizontal delgada + falta de anti-racionalización + falta de checklists reutilizables), **sin tocar el núcleo** (contratos, preguntas→merge, gates, post-MVP, trazabilidad).

Principio rector: **importar patrones, no archivos.** Todo lo que entre se traduce al español, se adapta a nuestras convenciones (IDs, estados, rutas `documentacion/…`) y se subordina al pipeline — nunca como "skills sueltas activables", porque eso nos haría perder lo que nos diferencia.

| # | Qué incorporar | Dónde encaja | Esfuerzo | Prioridad | Rompe contratos? |
|---|---|---|---|---|---|
| I-1 | **Tablas anti-racionalización** | Cada SKILL.md de fase (junto a "anti-patrones") | Bajo | 🔴 Alta | No |
| I-2 | **Carpeta `_harness/referencias/`** (checklists desacopladas) | Nueva carpeta; la cargan `desarrollo` y `especificacion-tecnica` | Medio | 🔴 Alta | No |
| I-3 | **Engrosar `desarrollo`** con prácticas transversales (TDD, revisión 5 ejes, simplificación) | `skills/desarrollo/SKILL.md` + referencias | Medio | 🟠 Media-alta | No |
| I-4 | **Personas de revisión** (revisor-código, auditor-seguridad) | Nueva carpeta `agentes/`; invocadas desde gates de `desarrollo` | Medio | 🟠 Media | No |
| I-5 | **Sizing XS–XL + checkpoints** en el plan | `skills/plan-implementacion/SKILL.md` | Bajo | 🟡 Media-baja | No (refuerza) |

Las cinco son **aditivas**: ninguna modifica lo que una fase produce/consume, así que no hay riesgo de romper la cadena. Por eso se pueden incorporar incrementalmente y validar de a poco.

---

## 9. Detalle por ítem

### I-1 · Tablas anti-racionalización 🔴
**Qué es.** Una tabla `Excusa | Realidad` que anticipa los atajos que el modelo inventa para saltarse pasos y los desarma de antemano. Ejemplo real de Addy (TDD): *"Lo probé manualmente" → "El testeo manual no persiste; el cambio de mañana lo rompe sin que nadie se entere."*

**Por qué nos sirve.** Hoy tenemos "Qué NO hace" y "anti-patrones" (que dicen *qué* no hacer). La tabla anti-racionalización ataca el *por qué el modelo se lo salta igual* — es más quirúrgica contra el comportamiento real del LLM en ejecución.

**Dónde y cómo.** Una tabla corta (4–6 filas) en cada SKILL.md de fase, después de "Anti-patrones". Cada fase tiene sus propias excusas típicas:
- `documento-diseno`: *"Esto es obvio, lo infiero" → el requerimiento cerró el scope; lo nuevo va a preguntas.*
- `desarrollo`: *"Marco la tarea ✅ porque escribí el código" → ✅ exige pasar el criterio de done, no escribir el archivo.*
- `mejora`: *"Es un cambio chico, lo codeo directo" → si toca contratos es track C, no track A; el tamaño percibido engaña.*

**Esfuerzo.** Bajo. Es escribir 6–8 tablas. **Empezar por aquí.**

---

### I-2 · Carpeta `_harness/referencias/` 🔴
**Qué es.** Checklists de dominio reutilizables y desacopladas de las skills (Addy: `references/security-checklist.md`, `accessibility-checklist.md`, `performance-checklist.md`, `testing-patterns.md`). Las skills las **cargan cuando hacen falta** en vez de inflar el prompt — *progressive disclosure*.

**Por qué nos sirve.** Es la respuesta directa a nuestro hueco #1 (cobertura horizontal delgada). En vez de escribir skills horizontales nuevas, ponemos el conocimiento como **referencia consultable**, coherente con que ya centralizamos lo compartido en `CONVENCIONES.md`.

**Dónde y cómo.** Nueva carpeta `skills/_harness/referencias/`. Candidatas iniciales (traducidas y podadas a lo esencial):
- `seguridad.md` — OWASP + LLM, validación en límites, secretos. La consulta `especificacion-tecnica` (al definir límites) y `desarrollo` (en el gate de revisión).
- `accesibilidad.md` — WCAG básico. La consulta `wireframes` y `desarrollo` (solo perfil con UI).
- `performance.md` — Core Web Vitals / criterios medibles. Alimenta los RNF de `documento-diseno` (FURPS+ → Performance) y el gate de `desarrollo`.
- `testing.md` — pirámide, naming, qué nivel para qué. La consulta `desarrollo`.

Importante: **condicionadas por perfil** (no cargar accesibilidad si no hay UI). Y referenciadas desde `CONVENCIONES.md` como recurso opcional, igual que las herramientas del entorno.

**Esfuerzo.** Medio (traducir + podar 4 archivos). Alto retorno: sube calidad sin inflar skills ni romper nada.

---

### I-3 · Engrosar `desarrollo` con transversales 🟠
**Qué es.** Hoy `desarrollo` es delgada en *cómo* construir bien (su loop por tarea es sólido, pero "implementar" es casi una caja negra). Addy es profunda aquí: `incremental-implementation` (slices verticales), `test-driven-development`, `code-review-and-quality` (5 ejes), `code-simplification`, `debugging-and-error-recovery`.

**Por qué nos sirve.** Es nuestro hueco #2 (de mapeo §5: `desarrollo` es claramente más delgada que su contraparte distribuida). No necesitamos reescribir esas skills: las **resumimos como sub-protocolos** dentro del loop por tarea y delegamos el detalle a `_harness/referencias/`.

**Dónde y cómo.** En `skills/desarrollo/SKILL.md`, enriquecer el "Loop por tarea":
- Paso *Implementar* → guía corta de slice vertical + (si el perfil lo pide) ciclo rojo-verde-refactor, citando `referencias/testing.md`.
- Paso *Verificar* → además de lint/build, criterio de evidencia ("seems right" no basta — ya alineado con nuestros output checks).
- Gate de fase → checklist de revisión de 5 ejes (correctitud, legibilidad, arquitectura, seguridad, performance), apoyado en las personas de I-4.
- Sección nueva *"Cuando algo se rompe"* → triage de 5 pasos (reproducir → localizar → reducir → arreglar → blindar) de `debugging-and-error-recovery`.

**Cuidado de altitud.** `desarrollo` sigue **sin re-planificar ni re-decidir**: esto agrega *cómo ejecutar mejor*, no *qué construir*. Si un contrato falla, sigue devolviendo a la fase dueña.

**Esfuerzo.** Medio. Depende de I-2 (las referencias) para no inflar el SKILL.

---

### I-4 · Personas de revisión 🟠
**Qué es.** Agentes/personas con voz especializada para revisión adversarial: un `revisor-codigo` con estándar de Staff Engineer (5 ejes), un `auditor-seguridad` con OWASP. Addy los trae en `agents/`.

**Por qué nos sirve.** Hoy el gate de `desarrollo` hace auto-revisión "en primera persona", que es justo donde el modelo es más complaciente consigo mismo. Una persona con contexto fresco y mandato adversarial encuentra más. Encaja perfecto con nuestra filosofía de gates.

**Dónde y cómo.** Nueva carpeta `skills/_harness/agentes/` (o `agentes/` a nivel repo si se empaqueta como plugin). `desarrollo` los invoca como sub-agente en el gate de fase — y ya tenemos permitido "sub-agentes solo para review/verify/research", así que **encaja sin cambiar reglas**. Fallback manual si el entorno no soporta sub-agentes (como con `code-review`/`verify`).

**Esfuerzo.** Medio. Empezar con 1 (`revisor-codigo`); el de seguridad después.

---

### I-5 · Sizing XS–XL + checkpoints en el plan 🟡
**Qué es.** De `planning-and-task-breakdown`: tabla de tamaño de tarea (XS=1 archivo … XL=demasiado grande, partir) y "checkpoints cada 2–3 tareas". Nuestra única skill con competidor directo de mejor calidad (§5).

**Por qué nos sirve.** Refuerza `plan-implementacion` con dos heurísticas concretas que hoy no explicita: cuándo una tarea es demasiado grande, y dónde poner paradas de verificación.

**Dónde y cómo.** En `skills/plan-implementacion/SKILL.md`: agregar la tabla de sizing como criterio de atomicidad de tareas, y los checkpoints como marcadores entre grupos de tareas en `plan.md` (se reflejan en `seguimiento.md`). Nuestros IDs `F{n}-T{m}` y la traza a RF se mantienen.

**Esfuerzo.** Bajo. Refuerza un contrato existente, no lo cambia.

---

## 10. Orden sugerido de implementación

Pensado para que cada paso sea validable por separado y el riesgo crezca despacio:

1. **I-1 (anti-racionalización)** — bajo esfuerzo, cero riesgo de contrato, mejora inmediata del comportamiento. Calienta motores.
2. **I-2 (referencias)** — habilita I-3; es la inversión estructural de mayor retorno.
3. **I-5 (sizing/checkpoints)** — barato, cierra el único punto donde Addy nos superaba de frente.
4. **I-3 (engrosar `desarrollo`)** — el cambio de más valor, pero apóyalo en I-2 ya hecho.
5. **I-4 (personas)** — el más "producto"; déjalo al final porque depende de cómo empaquetes.

---

## 11. Validación con un proyecto nuevo

Tu intuición es correcta: **estos cambios hay que validarlos dogfooding**, no en abstracto. Pero conviene calibrar el costo.

- **I-1, I-5** no necesitan un proyecto completo: se validan **leyendo** las skills modificadas y corriendo una fase aislada (p. ej. re-correr `documento-diseno` sobre un requerimiento de prueba y ver si la tabla anti-racionalización cambia el comportamiento).
- **I-2, I-3, I-4** sí piden un **proyecto pequeño end-to-end** para ver el efecto en construcción real. La buena noticia: ya tienes el pendiente *"proyecto de ejemplo + artefactos dorados (demo end-to-end)"* en el roadmap del README. **Este es el momento de ejecutarlo** — mata dos pájaros: valida las incorporaciones *y* produce la demo que te falta para publicar.

**Recomendación de validación.** Elige un proyecto-juguete chico pero con las tres dimensiones del perfil tocadas (algo con UI + un endpoint + idealmente un toque de LLM), córrelo de punta a punta con el harness ya enriquecido, y registra las fricciones en `MEJORAS-HARNESS.md` (el mecanismo de auto-mejora que ya tienes, §8 del MANUAL). Ese log es, además, la evidencia de que las incorporaciones funcionan.

> Nota de altitud para toda la Parte II: ninguna incorporación reabre el principio de *separación por altitud* ni convierte el harness en un catálogo. Todo lo que entra se subordina a una fase existente o vive como **referencia/persona opcional** que las fases consultan. El núcleo (contratos, preguntas→merge, gates, post-MVP, trazabilidad) queda intacto.

---

## 12. Estado de aplicación

Las cinco incorporaciones se implementaron en la rama `mejoras/incorporaciones-agent-skills` (2026-06-14):

| Ítem | Estado | Qué se tocó |
|------|--------|-------------|
| I-1 anti-racionalización | ✅ aplicado | tabla `Excusa → Realidad` en las 8 skills + esqueleto en `CONVENCIONES §10` |
| I-2 referencias | ✅ aplicado | `skills/_harness/referencias/` (seguridad, accesibilidad, performance, testing) + `CONVENCIONES §12` + citas desde `documento-diseno`, `wireframes`, `especificacion-tecnica` |
| I-5 sizing/checkpoints | ✅ aplicado | tabla XS–XL + checkpoints en `plan-implementacion` (+ output check) |
| I-3 engrosar desarrollo | ✅ aplicado | guía de implementación/verificación, revisión 5 ejes en el gate, sección de triage de bugs |
| I-4 personas | ✅ aplicado | `skills/_harness/agentes/` (`revisor-codigo`, `auditor-seguridad`) + `CONVENCIONES §13` + invocación desde el gate de `desarrollo` |

Documentación interna actualizada en consecuencia: `CLAUDE.md` (arquitectura + esqueleto) y `docs/MANUAL.md §7.bis`.

**Pendiente:** validación dogfooding con un proyecto de ejemplo end-to-end (ver §11). Es el siguiente paso natural y a la vez produce la demo que falta en el roadmap del README.
</content>
