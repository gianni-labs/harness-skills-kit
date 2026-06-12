# Análisis del harness Spec-Design — diseño de skills y preparación para empaquetado

> Generado el 2026-06-11. Insumos: las 8 skills de `skills/`, `docs/MANUAL.md`, `docs/MEJORAS-HARNESS.md`.
> Objetivo: evaluar la calidad del diseño actual y dejar una hoja de ruta concreta para empaquetarlo y publicarlo (CLI / instalable).

---

## 1. Resumen ejecutivo

El harness está **conceptualmente muy bien diseñado**: la cadena de contratos fijos entre fases, la disciplina "preguntar, no inventar", la separación por altitud y el rigor adaptativo post-MVP son decisiones de diseño maduras que lo ponen al nivel de metodologías publicadas del mismo género (GitHub spec-kit, AWS Kiro specs, BMAD Method). Lo que hoy lo separa de un producto publicable no es el diseño, sino tres cosas:

1. **Acoplamiento al proyecto de origen** (fit-rutinas): rutas hardcodeadas, supuestos de dominio (app con LLM, app con UI, mobile-first, TypeScript/Next.js) incrustados en skills que deberían ser genéricas.
2. **Drift y duplicación internas**: los diagramas de pipeline de las primeras skills quedaron desactualizados cuando se agregó `especificacion-tecnica`; las convenciones compartidas (patrón preguntas, IDs, estados) están copiadas en cada skill en vez de vivir en un solo lugar, y ya divergen entre sí.
3. **Falta de infraestructura de producto**: no hay versionado, ni instalador, ni plantillas separadas del prompt, ni forma de validar que una skill funciona tras un cambio, y una de las 8 skills (`frontend-design`) es de un tercero con licencia referenciada pero ausente.

Recomendación central de empaquetado: **distribuir como plugin de Claude Code** (marketplace de plugins) en lugar de un CLI propio, al menos como primer release. Detalle en §7.

---

## 2. Fortalezas del diseño (lo que hay que conservar tal cual)

Estas son las piezas que hacen al harness valioso y que el empaquetado **no debe diluir**:

- **Cadena de contratos handoff-ready.** Cada fase declara explícitamente "qué consume" y "qué produce" con formato fijo (V-N como contrato con wireframes, §Alcance MVP como contrato con el plan, la spec como contrato del código). Esto es exactamente lo que hace que el desarrollo asistido por IA no degenere: el modelo de la fase N no puede improvisar porque la fase N-1 ya fijó el formato.
- **"Preguntar, no inventar" + patrón `preguntas-*.md → merge`.** Es la mejor pieza del harness. Resuelve el problema número uno de los LLM en diseño (rellenar huecos con inventos plausibles) y además es asíncrono: el usuario responde en el archivo a su ritmo, con opciones (a)(b)(c) y campo `R:`. El propio log (M-004) confirma que funcionó consistentemente en 4 fases.
- **Separación por altitud con "fase dueña".** La regla "no se reabren decisiones desde una fase posterior; se vuelve a la fase dueña" es la que evita que el código erosione la documentación. La tabla de altitud de `especificacion-tecnica` (¿por qué? / ¿cuál es el contrato? / ¿en qué orden?) es didácticamente excelente.
- **Estructura interna homogénea de las skills.** Las 6 de fase comparten esqueleto: contrato de entrada → qué hace / qué NO hace → Fase 0 → generación → merge → cierre → modo scoped → casos especiales → anti-patrones → output check. Las secciones "Qué NO hace" y "Anti-patrones" son prompt engineering de primera: restringir es más efectivo que instruir.
- **Output checks como definition of done.** "Si algún check falla, el skill no termina" convierte cada fase en verificable, no en "terminé porque escribí el archivo".
- **Rigor adaptativo post-MVP (tracks A/B/C/D) + gate humano.** El insight de M-007 (la mejora trivial no paga ceremonia de MVP; la que toca contratos no entra a código sin spec) y el de M-008 (gate de aprobación ≠ AskUserQuestion) son aprendizajes que muchos frameworks publicados no tienen. La distinción fork-de-opciones vs parada-de-aprobación merece quedar documentada como patrón con nombre propio.
- **Memoria en dos altitudes.** `bitacora.md` (proyecto) vs `MEJORAS-HARNESS.md` (meta/harness) con prefijos distintos (`B-` vs `M-`) — el harness es auto-mejorable por diseño, y eso es un diferenciador publicable.

---

## 3. Hallazgos concretos (bugs y drift detectados en los archivos actuales)

Cosas puntuales y verificables, ordenadas por severidad. Conviene arreglarlas antes de cualquier otra cosa porque son baratas y algunas confunden al modelo en ejecución.

| # | Severidad | Dónde | Hallazgo |
|---|-----------|-------|----------|
| H-1 | Alta | `refine-requirement` (diagrama inicial), `design-document` (diagrama), `wireframes` (diagrama) | Los diagramas de pipeline **omiten la fase `especificacion-tecnica`** (se agregó después, M-001) y llaman a la fase 5 `implementation-plan` (nombre viejo en inglés; la skill real es `plan-implementacion`). El modelo que ejecuta la fase 1 ve un pipeline de 4 fases que ya no existe. |
| H-2 | Alta | `especificacion-tecnica` §4, §6, §7 | La estructura objetivo trae secciones **obligatorias específicas de apps con LLM** ("JSON Schema de los artefactos del LLM", "Contrato del LLM", "prompts versionados", "la rutina/ciclo"). En un proyecto sin LLM esas secciones no aplican y el skill no dice qué hacer. Deben ser condicionales ("si la app integra un LLM…"). |
| H-3 | Alta | `frontend-design` | Es la skill pública de Anthropic copiada tal cual; su frontmatter dice `license: Complete terms in LICENSE.txt` pero **no hay LICENSE.txt en el repo**. Para publicar el kit hay que: verificar términos de redistribución, incluir la licencia, o reemplazarla por una skill propia / declararla dependencia externa que el instalador descarga. |
| H-4 | Media | `plan-implementacion` (plantilla BACKLOG) vs `mejora`/MANUAL §6 | **Dos vocabularios de estado distintos** para el backlog: la plantilla define `✅ 🔵 🟠 (en análisis) ⬜ ⛔` y el flujo de mejora usa `⬜ → 🔵 → 📝 (documentado, espera OK) → 🟡 → ✅`. El estado `🟠` no existe en el ciclo de mejora y `📝`/`🟡` no existen en la plantilla. Unificar en un solo set. |
| H-5 | Media | `INDICE.md` | Lo mantienen `wireframes`, `especificacion-tecnica`, `plan-implementacion`, `desarrollo` y `mejora`… pero **ninguna skill lo crea ni define su formato**. `refine-requirement` y `design-document` ni lo mencionan. Falta el dueño del artefacto (candidato natural: que `refine-requirement` lo inicialice, o una plantilla del instalador). |
| H-6 | Media | `wireframes` Fase 0 | Default "mobile-first (**el uso diario es desde el teléfono**)" — justificación heredada de fit-rutinas. El default genérico debería derivarse del RNF de responsive del diseño, y si no existe, preguntar sin sesgo. |
| H-7 | Media | `especificacion-tecnica` Fase 0 | Defaults "REST con route handlers" y "TypeScript si el ADR eligió Next.js" — razonables como ejemplo, pero formulados como default universal. Deben derivarse del ADR de stack, sin nombrar frameworks concretos en el skill. |
| H-8 | Baja | `docs/MEJORAS-HARNESS.md` | M-008 aparece después de M-009 (orden de archivo); M-009 y M-003 están "aplicada (en este proyecto)" — es decir, **aplicadas en fit-rutinas pero no necesariamente reflejadas en los skills de este repo** (ej.: el flujo DESIGN.md de M-009 no aparece en `skills/mejora/SKILL.md` de este repo). Verificar sincronización snapshot ↔ versión viva antes de congelar. |
| H-9 | Baja | README vs MANUAL | El README cuenta "8 skills (6 + 2 auxiliares)"; el MANUAL habla del "7º skill" y no menciona `frontend-design` en su inventario. Cosmético, pero el MANUAL es el doc canónico del kit. |
| H-10 | Baja | Nombres de skills | Mezcla de idiomas: `refine-requirement`, `design-document`, `wireframes`, `frontend-design` (inglés) vs `especificacion-tecnica`, `plan-implementacion`, `desarrollo`, `mejora` (español). Para publicar, decidir una convención (ver §6.3). |

> Nota sobre H-8/M-009: el flujo "track B verifica/genera `DESIGN.md`" está documentado en el MANUAL §6 pero **no** en la skill `mejora` de este repo. Si la versión viva en fit-rutinas ya lo tiene, falta traerlo; si no, falta implementarlo. Es justo el tipo de drift que el empaquetado debe cerrar.

---

## 4. Análisis por skill (calidad individual)

**`refine-requirement` — muy sólida.** La autoconciencia de que "no agregar es contraintuitivo para un LLM" y la lista de anti-patrones (no KPIs, no lenguaje corporativo, no reescribir la voz del usuario) es de lo mejor del kit. Único punto débil: el diagrama desactualizado (H-1) y que el formato de `preguntas.md` está definido aquí en su versión más completa — las demás skills lo referencian por imitación ("mismo patrón que el skill anterior"), lo que funciona pero es frágil (ver §5.2).

**`design-document` — la más completa y la más larga (376 líneas).** La combinación EARS + MoSCoW + FURPS+ + ADR mini es pragmática y bien dosificada. Los checks cruzados ("pantalla sin RF = over-design; RF sin pantalla = hueco") son excelentes heurísticas bidireccionales. Riesgo: por tamaño, es candidata a extraer los formatos (RF/RNF/ADR/V/F) a archivos de referencia adjuntos a la skill (progressive disclosure) y dejar el SKILL.md en la lógica de proceso.

**`wireframes` — la mejor regla de oro del kit.** "No define stack" con instrucción explícita de resistir el "hazlo en React para verlo" ataca el modo de fallo más común del prototipado con IA. La notación ASCII declarada como convención y la trazabilidad por caja (RF/F + "navega hacia") están muy bien. Debilidad: asume que todo proyecto tiene UI — la cadena no dice qué pasa con un CLI, una API o una librería (la fase debería ser **omisible por tipo de proyecto**, igual que en el track B de mejoras ya es condicional).

**`especificacion-tecnica` — el eslabón que faltaba y se nota que nació del dogfooding (M-001).** La tabla de altitud es la mejor explicación del porqué de la fase. Es también la skill **más contaminada por el dominio de origen** (H-2, H-7): la mitad de su estructura objetivo asume app web con LLM. Generalizar: secciones núcleo (módulos, tipos, APIs/interfaces, modelo de error, trazabilidad) + secciones condicionales por capacidad del proyecto (LLM, jobs, integraciones externas…).

**`plan-implementacion` — bien resuelta la doble responsabilidad** (plan + inicializar control + BACKLOG). El formato de tarea con criterio de done verificable y la matriz de cobertura RF→tareas son los mecanismos correctos. Observación: inicializa 4 plantillas embebidas en el prompt (seguimiento, bitácora, herramientas, backlog) — al empaquetar, esas plantillas deberían ser **archivos de plantilla del kit** que la skill copia, no texto incrustado que el modelo re-tipea (menos tokens, cero drift de formato).

**`desarrollo` — el ejecutor con la disciplina correcta.** "Modo mínimo" de sub-agentes (secuencial, sub-agentes solo para review/verify/research) es la decisión conservadora correcta para v1; la disciplina de contratos (detenerse si la desviación cambia un contrato) cierra el loop con la fuente de verdad. El bloque "Dogfooding del harness" activado por convención de archivo es elegante. Mejora posible: el gate de fase invoca `code-review`/`security-review`/`verify`/`run` por nombre — son skills del ecosistema Claude Code que pueden no existir en el entorno del usuario final; el kit debe declararlas **dependencias opcionales** con fallback ("si no está disponible, ejecutar lint/build/tests manualmente").

**`mejora` — la skill más madura conceptualmente.** Router + rigor proporcional + gate humano + backlog como única puerta de entrada + "ante la duda, el track más riguroso". El anti-patrón explícito "no usar AskUserQuestion para el gate" demuestra aprendizaje fino sobre los mecanismos del agente. Pendientes: incorporar el flujo DESIGN.md del track B (H-8) y la decisión abierta de M-007 (¿"modo scoped" como sección convencional o como parámetro? — ver §6.2).

**`frontend-design` — no es del harness.** Es la skill genérica de Anthropic; cumple su rol como herramienta invocada, pero para el kit es una **dependencia de terceros**, no contenido propio (H-3).

---

## 5. Temas transversales de diseño

### 5.1 Acoplamiento al proyecto de origen (el problema nº 1 para empaquetar)

Confirma y amplía M-005. Hay tres niveles de acoplamiento, de más fácil a más difícil de resolver:

1. **Rutas** (`documentacion/02-diseno/`, `05-desarrollo/`, `mejoras/<id>-<slug>/`): resoluble por convención fija documentada (recomendado para v1 — "convention over configuration", como hace spec-kit con `specs/`) o por un `harness.config.(json|yaml)` en la raíz que las skills leen al arrancar. Recomiendo **convención fija en v1** y config en v2 solo si hay demanda: un archivo de config es una superficie de fallo más para el modelo.
2. **Defaults de dominio** (mobile-first "porque el uso diario es desde el teléfono", REST, TypeScript): reemplazar por "derivar del documento de la fase anterior; si no está, preguntar".
3. **Estructura asumida del producto** (app con UI + LLM): introducir el concepto de **perfil de proyecto** detectado en fase 1-2 (¿tiene UI? ¿integra LLM? ¿tiene API?) que activa/desactiva fases (wireframes) y secciones (contrato LLM de la spec). Esto puede vivir como una línea en el requerimiento cerrado o en el diseño, no necesita config externa.

### 5.2 Duplicación de convenciones compartidas

El patrón preguntas→merge, los IDs, los estados y el esqueleto de fases están **copiados con variaciones** en cada skill (ej.: máximo de preguntas: ~15, ~15, ~12, ~10, ~8 — esto es razonable como gradiente, pero el formato base se re-explica 5 veces y ya divergió en el set de estados, H-4). Dos soluciones compatibles:

- Extraer un **`CONVENCIONES.md` del kit** (patrón preguntas, IDs, estados, esqueleto de fase, regla de gates) que cada SKILL.md referencia en una línea. Las skills soportan archivos adjuntos en su carpeta — puede ser un archivo compartido instalado junto a las skills.
- Mantener en cada SKILL.md **solo el delta** (su máximo de preguntas, sus bloques sugeridos).

Beneficio doble: menos tokens por invocación y un solo lugar que editar (el drift H-4 no habría ocurrido).

### 5.3 Idioma

Todo el kit está en español (contenido, artefactos, IDs como `diseno.md`). Decisión de producto honesta a tomar **antes** de publicar, porque renombrar después rompe todo:

- **Publicar en español como identidad** (nicho desatendido, diferenciador real — casi todo el ecosistema spec-driven es inglés), o
- **Internacionalizar** (skills en inglés, artefactos configurables), que duplica el costo de mantenimiento.

Mi recomendación: **v1 en español, asumido y declarado** ("harness en español para desarrollo asistido por IA"), con los *nombres de skill* unificados en español (`refinar-requerimiento`… o todos en inglés, pero no mezcla, H-10). La traducción puede ser un release posterior si hay tracción.

### 5.4 Fricción de la Fase 0

Las 6 skills de fase bloquean al inicio esperando confirmación ("No empezar sin confirmación"). Es la decisión correcta como default (el usuario revisa fuentes y enfoque), pero al encadenar fases — sobre todo dentro de `/mejora`, que ya tiene su propio gate — se acumulan paradas de bajo valor. Sugerencia: mantener la Fase 0 pero permitir que el orquestador (o el usuario vía argumento, ej. `/design-document <archivo> --directo`) la convierta en "anunciar y proceder" cuando las fuentes son inequívocas. Está alineado con el espíritu del M-008: gates donde hay riesgo, no ceremonia donde no lo hay.

### 5.5 Validación del propio kit (no existe hoy)

No hay manera de saber si un cambio a un SKILL.md mejora o rompe el comportamiento, salvo dogfooding completo. Para un kit publicado esto es crítico (los usuarios reportarán "la skill hizo X raro"). Mínimo viable:

- Un **proyecto de fixture** en el repo (`fixtures/proyecto-ejemplo/` con un requerimiento crudo de ~1 página de un dominio neutro) + los **artefactos dorados** esperados de cada fase.
- Un checklist de humo por skill: correr la fase sobre el fixture y verificar el output check a mano (automatizarlo con un LLM-judge puede venir después).

Esto además produce gratis la **demo del README** (antes/después de cada fase), que es lo que vende el kit.

---

## 6. Los apuntes de `docs/` integrados al análisis

Estado de cada M-nnn respecto al empaquetado, con mi lectura:

| M | Tema | Estado real para el kit | Mi evaluación |
|---|------|-------------------------|---------------|
| M-001 | Fase `especificacion-tecnica` | Hecha y en el pipeline | Correcta — pero dejó el drift H-1 (diagramas viejos en 3 skills). Cerrar. |
| M-002 | Bitácora con referencia cruzada | Aplicada en plantillas | Correcta. Al extraer plantillas a archivos (§4, plan-implementacion) queda blindada. |
| M-003 | CLAUDE.md plantilla con "Prácticas de trabajo" | **Pendiente** — no hay plantilla en este repo | Imprescindible para el instalador: es el pegamento que hace que un proyecto nuevo "hable" el harness desde el día 1. Debe incluir: puntero a `documentacion/INDICE.md`, las reglas de oro, y la convención de rutas. |
| M-004 | Formalizar patrón preguntas→merge | **Pendiente** | Resolver con el `CONVENCIONES.md` compartido (§5.2). Es la pieza con mejor relación esfuerzo/beneficio del backlog. |
| M-005 | Rutas hardcodeadas | **Pendiente** — el mayor bloqueante | Resolver por convención fija documentada en v1 (§5.1). |
| M-006 | BACKLOG.md / ciclo de vida | Hecha | Correcta. Unificar estados (H-4). |
| M-007 | Skill `mejora` + tracks | Hecha; decisión abierta: ¿scoped como sección o parámetro? | Mantener como **sección convencional** (como hoy): un parámetro formal exigiría que el runtime de skills lo soporte; la sección es legible y ya funciona. Revisar en v2. |
| M-008 | Gate humano ≠ AskUserQuestion | Hecha | Excelente. Promoverla de nota a **patrón nombrado** en el MANUAL/CONVENCIONES (es exportable a cualquier skill de terceros). |
| M-009 | DESIGN.md en track B | Aplicada en fit-rutinas, **no reflejada en `skills/mejora` de este repo** | Traer al kit (H-8). Además, considerar generar DESIGN.md también al cerrar el MVP (no solo al primer track B): el costo es bajo y deja el sistema de diseño documentado desde el inicio. |

Síntesis: de las 9 entradas, 4 están realmente cerradas en este repo, 3 pendientes (M-003, M-004, M-005 — justamente las tres que el README ya lista como bloqueantes del empaquetado) y 2 con drift de sincronización (M-007 decisión abierta, M-009 sin portar). El log funcionó: el harness se auto-diagnosticó bien.

---

## 7. Contexto de ecosistema y estrategia de distribución

### Comparables (para posicionar, no para copiar)

- **GitHub spec-kit** (`specify` CLI): fases spec → plan → tasks → implement, con plantillas y una "constitution". Es el comparable más directo. Lo que tu harness tiene y spec-kit no: el patrón preguntas→merge asíncrono, el ciclo post-MVP con rigor adaptativo, la bitácora con trazabilidad y el gate humano formalizado.
- **AWS Kiro**: requirements (EARS) → design → tasks como artefactos versionados. Valida tu elección de EARS.
- **BMAD Method**: agentes por rol (analyst, PM, architect…). Más pesado; tu enfoque "una fase = un skill" es más simple de mantener.

Conclusión: el harness no llega tarde — llega con diferenciadores reales (post-MVP, preguntas→merge, auto-mejora). El README debería decirlo explícitamente frente a spec-kit.

### Distribución: plugin de Claude Code primero, CLI después (si hace falta)

La idea de un CLI propio es válida, pero hay un camino con mucho menos costo y mejor encaje:

1. **v1 — Plugin de Claude Code.** Los plugins empaquetan skills + comandos y se instalan con `/plugin install` desde un marketplace (un repo git con `.claude-plugin/marketplace.json`). Te da: instalación en un comando, versionado, updates, y distribución natural al público que ya usa Claude Code (que es exactamente tu usuario objetivo). El trabajo es reorganizar este repo al formato plugin + manifest.
2. **v1.5 — Compatibilidad `npx skills add`** (skills.sh / formato agéntico abierto) para usuarios de otros agentes (Cursor, etc.), si quieres alcance extra: el formato SKILL.md ya es el estándar.
3. **v2 — CLI propio solo si aparece la necesidad** que el plugin no cubre: scaffolding de `documentacion/` + `INDICE.md` + `CLAUDE.md` plantilla en el proyecto destino. Incluso eso puede resolverlo una skill más (`/harness-init`) dentro del plugin — **recomiendo esa vía**: una skill de inicialización que crea la estructura de carpetas, el INDICE.md (resuelve H-5), el CLAUDE.md plantilla (resuelve M-003) y pregunta el perfil del proyecto (§5.1.3). Cero binarios que mantener.

---

## 8. Plan de acción recomendado (ordenado)

**Etapa 1 — Higiene (horas, no días):**
1. Corregir diagramas de pipeline en `refine-requirement`, `design-document`, `wireframes` (H-1).
2. Unificar estados del backlog en plantilla + mejora + MANUAL (H-4).
3. Reordenar/renumerar M-008/M-009 y alinear README↔MANUAL (H-8, H-9).
4. Portar el flujo DESIGN.md de M-009 a `skills/mejora/SKILL.md`.

**Etapa 2 — Desacoplar (el corazón del empaquetado):**
5. Decidir idioma y unificar nombres de skills (H-10, §5.3).
6. Fijar la convención de rutas y documentarla en un solo lugar (M-005, §5.1).
7. Generalizar `especificacion-tecnica` (secciones condicionales por perfil) y limpiar defaults de dominio en `wireframes`/Fase 0 (H-2, H-6, H-7).
8. Hacer `wireframes` omisible para proyectos sin UI (perfil de proyecto).

**Etapa 3 — Infraestructura de kit:**
9. Crear `CONVENCIONES.md` compartido (patrón preguntas→merge, IDs, estados, gate humano como patrón nombrado) y adelgazar las skills a sus deltas (M-004, §5.2).
10. Extraer las plantillas embebidas (`seguimiento`, `bitacora`, `herramientas`, `BACKLOG`, `INDICE`, `CLAUDE.md` plantilla) a `templates/` (M-002, M-003, H-5).
11. Crear la skill `harness-init` (scaffolding del proyecto destino + perfil).
12. Resolver la licencia de `frontend-design`: incluir LICENSE o tratarla como dependencia externa (H-3).

**Etapa 4 — Publicar:**
13. Reorganizar al formato plugin de Claude Code + marketplace.json; versionar (semver) y CHANGELOG.
14. Fixture de proyecto ejemplo + artefactos dorados (§5.5) → sirve de test de humo y de demo del README.
15. README de producto: posicionamiento vs spec-kit, GIF/transcript del flujo, instalación en 1 comando.

---

## 9. Veredicto

El diseño metodológico está listo; lo que falta es trabajo de **producto**, no de concepto. Las tres pendientes que tú mismo registraste (M-003, M-004, M-005) son efectivamente los bloqueantes correctos — este análisis agrega sobre eso el drift interno (H-1, H-4, H-5, H-8), el desacoplamiento de dominio (H-2, H-6, H-7), la cuestión de licencia (H-3) y una estrategia de distribución más barata que un CLI (plugin + skill de init). Con las etapas 1–3 hechas, el kit es honestamente publicable.
