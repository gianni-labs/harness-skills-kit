# Plan de mejoras del harness — hacia la versión genérica y publicable

> Generado el 2026-06-11 a partir de `analisis-harness.md` + decisiones del usuario.
> Alcance: dejar el harness **OK, genérico y usable desde cero**. La distribución (plugin/CLI) queda explícitamente **fuera** de este plan.

## Decisiones base (cerradas, no se reabren)

| # | Decisión |
|---|----------|
| D-1 | **Nombres de skills 100% en español.** Renombres: `refine-requirement` → `refinar-requerimiento` · `design-document` → `documento-diseno` · `wireframes` → se mantiene (préstamo estándar en UX en español). Las demás ya están en español. |
| D-2 | **`frontend-design` se elimina por completo**: carpeta + toda referencia en skills, docs y README. |
| D-3 | **`docs/MEJORAS-HARNESS.md` se elimina** al cerrar este plan (todas sus entradas quedan aplicadas o registradas aquí). |
| D-4 | **Rutas: convención fija** `documentacion/01-requerimiento … 05-desarrollo` + `mejoras/` + `BACKLOG.md` + `INDICE.md`. La crea la skill inicial; se documenta en un único lugar (CONVENCIONES). Sin archivo de configuración. |
| D-5 | **Nueva skill inicial `iniciar-harness`**: scaffolding + perfil del proyecto + verificaciones. |
| D-6 | **Cero referencias a fit-rutinas** ni a ningún dominio particular: ejemplos neutros o placeholders en todas las skills. |

## Tracking

> Estados: ⬜ pendiente · 🟡 en curso · ✅ hecho · ⛔ bloqueado

| Fase | Objetivo | Tareas | Estado |
|------|----------|--------|--------|
| F1 — Higiene y drift | Corregir bugs internos detectados | 5 | ✅ 5/5 |
| F2 — Genérico | Eliminar toda herencia de fit-rutinas | 6 | ✅ 6/6 |
| F3 — Convenciones y plantillas | Una sola fuente para lo compartido | 4 | ✅ 4/4 |
| F4 — Skill inicial | `iniciar-harness` | 3 | ✅ 3/3 |
| F5 — Docs y cierre | MANUAL/README/CLAUDE.md + limpieza final | 4 | ✅ 4/4 |

**Progreso global:** ██████████ 100% (22/22) — completado 2026-06-11

---

## F1 — Higiene y drift

#### F1-T1: Corregir diagramas de pipeline desactualizados ✅
- **Qué:** En `refine-requirement`, `design-document` y `wireframes`: los diagramas/descripciones del pipeline deben mostrar las 6 fases reales (incluida `especificacion-tecnica`) con sus nombres definitivos en español (post F2-T1). Eliminar el nombre viejo `implementation-plan`.
- **Archivos:** `skills/refine-requirement/SKILL.md` (líneas ~10-17), `skills/design-document/SKILL.md` (~10-17), `skills/wireframes/SKILL.md` (~10-17).
- **Done:** `grep -r "implementation-plan" skills/` sin resultados; los 3 diagramas listan las 6 fases.

#### F1-T2: Unificar estados del backlog ✅
- **Qué:** Un solo vocabulario en todo el kit: `⬜ no iniciado · 🔵 planificado · 📝 documentado (espera OK) · 🟡 en desarrollo · ✅ hecho · ⛔ descartado`. Eliminar `🟠 en análisis` de la plantilla BACKLOG de `plan-implementacion`. Alinear `mejora`, `desarrollo` y MANUAL §6.
- **Done:** `grep -r "🟠" skills/ docs/` sin resultados; el set aparece idéntico en plantilla BACKLOG, skill `mejora` y MANUAL.

#### F1-T3: Portar el flujo DESIGN.md (M-009) a la skill `mejora` ✅
- **Qué:** En `skills/mejora/SKILL.md`, track B: antes de diseñar, verificar que `DESIGN.md` existe en la raíz del repo destino; si no, generarlo **leyendo el codebase** (nunca inventando), formato Google Stitch. Actualizarlo al cerrar una mejora visual que cambie tokens/patrones. Añadir además la generación opcional al cierre del MVP en `desarrollo` (gate de release) como sugerencia.
- **Done:** la sección track B de `mejora` incluye el check de `DESIGN.md`; coherente con MANUAL §6 (que ya lo describe).

#### F1-T4: Definir el dueño y formato de `INDICE.md` ✅
- **Qué:** Hoy 5 skills lo actualizan y ninguna lo crea. Resolución: lo crea `iniciar-harness` (F4) desde plantilla (F3-T2). Las skills de fase solo lo actualizan; añadir a `refinar-requerimiento` y `documento-diseno` la instrucción de actualizarlo al cerrar (hoy no lo mencionan).
- **Done:** plantilla `INDICE.md` existe; las 6 skills de fase mencionan actualizarlo al cierre; `iniciar-harness` lo crea.

#### F1-T5: Eliminar `frontend-design` y todas sus referencias (D-2) ✅
- **Qué:** Borrar `skills/frontend-design/`. Quitar referencias en: plantilla `herramientas-recomendadas.md` dentro de `plan-implementacion` (fila frontend-design), README (tabla de auxiliares y estructura), CLAUDE.md del repo, y cualquier otra mención (`grep -ri "frontend-design"`).
- **Done:** `grep -ri "frontend-design" .` sin resultados (fuera de `analisis/`).

---

## F2 — Genérico: eliminar herencia de fit-rutinas (D-6)

#### F2-T1: Renombrar skills al español (D-1) ✅
- **Qué:** `skills/refine-requirement/` → `skills/refinar-requerimiento/`; `skills/design-document/` → `skills/documento-diseno/`. Actualizar frontmatter `name:`, slash commands sugeridos, y **toda referencia cruzada** en las 7 skills restantes, MANUAL y README.
- **Done:** `grep -ri "refine-requirement\|design-document" skills/ docs/ README.md` sin resultados.

#### F2-T2: Generalizar `especificacion-tecnica` (secciones condicionales) ✅
- **Qué:** Reestructurar la "estructura objetivo": secciones **núcleo** (módulos, modelo de datos tipado, contratos de API/interfaces, modelo de error, trazabilidad, pendientes) + secciones **condicionales por perfil** ("si el proyecto integra un LLM: contrato del LLM, JSON Schema de su output, prompts versionados"; "si hay integraciones externas: …"). Eliminar menciones a "rutina/ciclo", OpenRouter y cualquier resto del dominio. Defaults de Fase 0 (REST, TypeScript) → "derivar del ADR de stack; si no está decidido, preguntar".
- **Done:** la skill funciona conceptualmente para una app sin LLM; `grep -i "rutina\|openrouter" skills/especificacion-tecnica/` sin resultados de dominio.

#### F2-T3: Generalizar `wireframes` ✅
- **Qué:** (a) Quitar la justificación "el uso diario es desde el teléfono": la orientación se deriva del RNF de responsive del diseño y, si no existe, se pregunta sin default sesgado. (b) Declarar la fase **omisible**: si el perfil del proyecto no tiene UI (CLI, API, librería), la fase se salta y el pipeline lo dice explícitamente (también en MANUAL).
- **Done:** sin referencias a uso móvil como justificación; la skill y el MANUAL documentan el salto de fase para proyectos sin UI.

#### F2-T4: Limpiar ejemplos de dominio en el resto de skills ✅
- **Qué:** Barrido de las 7 skills buscando restos de fit-rutinas: "Crossfit/Funcional", "rutina", "ejercicio", "periodización", "ánimo", "RPE", "calendario", ejemplos V-2/V-5 de la app, "modelo de periodización", B-001 de ejemplo con SQLite/Vercel (reescribirlo con un ejemplo neutro), bloques sugeridos "Onboarding, Rutinas, IA". Reemplazar por ejemplos de dominio neutro (ej. una app genérica de notas/tareas) o placeholders `[ejemplo del dominio]`.
- **Done:** `grep -ri "rutina\|crossfit\|periodización\|fit-" skills/` sin resultados.

#### F2-T5: Herramientas del entorno como dependencias opcionales ✅
- **Qué:** En `desarrollo` (gates) y la plantilla `herramientas-recomendadas.md`: `code-review`, `security-review`, `verify`, `run`, `claude-api` se mencionan como **herramientas opcionales del entorno** con fallback explícito ("si no están disponibles: lint/build/tests manuales y revisión propia del diff").
- **Done:** los gates de `desarrollo` incluyen el fallback; la plantilla marca las herramientas como opcionales-según-entorno.

#### F2-T6: Suavizar la Fase 0 encadenada ✅
- **Qué:** Mantener la confirmación de Fase 0 como default, pero añadir en las 6 skills de fase una nota: cuando la skill es invocada **por el orquestador `mejora`** o el usuario pasa el argumento `directo`, la Fase 0 se convierte en "anunciar fuentes y proceder" (sin parada). Gates de riesgo (OK pre-desarrollo) intactos.
- **Done:** las 6 skills documentan el modo directo; `mejora` lo usa al secuenciar fases.

---

## F3 — Convenciones compartidas y plantillas

#### F3-T1: Crear `skills/_harness/CONVENCIONES.md` (única fuente de lo compartido) ✅
- **Qué:** Documento canónico con: (1) patrón `preguntas-*.md → merge` completo (formato, bloques temáticos, numeración global, contexto, opciones (a)(b)(c), campo `R:`, máximos por fase como tabla); (2) tabla de IDs (`RF/RNF/F/V/ADR/F{n}-T{m}/B/MEJ`); (3) estados unificados (F1-T2); (4) convención de rutas (D-4); (5) patrón **gate de aprobación humano vs AskUserQuestion** como patrón nombrado; (6) esqueleto estándar de skill de fase. Resuelve M-004.
- **Done:** el archivo existe y cubre los 6 puntos; `iniciar-harness` lo instala en `documentacion/CONVENCIONES.md` del proyecto destino.

#### F3-T2: Extraer plantillas embebidas a `skills/_harness/templates/` ✅
- **Qué:** Mover a archivos de plantilla: `seguimiento.md`, `bitacora.md` (con ejemplo B-001 neutro), `herramientas-recomendadas.md`, `BACKLOG.md`, `INDICE.md` (nueva), `CLAUDE-proyecto.md` (nueva — plantilla del CLAUDE.md del proyecto destino con bloque "Prácticas de trabajo", puntero a `documentacion/INDICE.md` y reglas de oro; resuelve M-003). `plan-implementacion` pasa de re-tipear plantillas a **copiarlas y rellenarlas**.
- **Done:** 6 plantillas en `templates/`; `plan-implementacion` referencia las plantillas en vez de incrustarlas.

#### F3-T3: Adelgazar skills a sus deltas ✅
- **Qué:** Reemplazar en cada skill las re-explicaciones del patrón de preguntas/IDs/estados por una referencia de una línea a CONVENCIONES + su delta propio (máximo de preguntas, bloques sugeridos). No tocar las secciones "Qué NO hace", "Anti-patrones" ni "Output check" (son el corazón de cada skill).
- **Done:** ninguna skill re-define el formato de preguntas ni los estados; cada una conserva solo sus parámetros propios.

#### F3-T4: Perfil del proyecto como concepto formal ✅
- **Qué:** Definir en CONVENCIONES el **perfil**: `UI: sí/no · LLM: sí/no · API/backend: sí/no` (+ libre). Lo captura `iniciar-harness` (o `refinar-requerimiento` si el proyecto arrancó sin init) y queda registrado al inicio del requerimiento cerrado. Las skills lo consumen: `wireframes` (omisible), `especificacion-tecnica` (secciones condicionales).
- **Done:** CONVENCIONES define el perfil; las 3 skills afectadas lo referencian.

---

## F4 — Skill inicial `iniciar-harness` (D-5)

#### F4-T1: Crear `skills/iniciar-harness/SKILL.md` ✅
- **Qué:** Nueva skill, mismo esqueleto estándar. Flujo: (1) verificar estado del proyecto destino (¿ya hay `documentacion/`? ¿hay CLAUDE.md?) — idempotente, no pisa nada existente sin avisar; (2) preguntar lo mínimo: nombre del proyecto + perfil (F3-T4) + ¿hay ya un documento de idea/requerimiento crudo?; (3) crear la estructura de convención (D-4): `documentacion/{01..05}`, `mejoras/`, copiar `INDICE.md`, `CONVENCIONES.md`, `BACKLOG.md` (vacío-semilla) y `CLAUDE-proyecto.md` → `CLAUDE.md` (o proponer merge si ya existe); (4) reportar verificación final (checklist de lo creado) y handoff: "coloca tu idea en un .md y corre `/refinar-requerimiento <archivo>`".
- **Done:** la skill existe con contrato de entrada/salida, qué NO hace (no escribe requerimientos, no decide stack, no crea código), casos especiales (proyecto ya iniciado, CLAUDE.md existente), anti-patrones y output check.

#### F4-T2: Integrar `iniciar-harness` al pipeline documentado ✅
- **Qué:** MANUAL y README pasan a mostrar el pipeline como `iniciar-harness → refinar-requerimiento → … → desarrollo`, con `mejora` como ciclo posterior. La sección "Cómo usar el harness en un proyecto nuevo" del MANUAL arranca con `/iniciar-harness`.
- **Done:** pipeline de 7 pasos (init + 6 fases) consistente en MANUAL, README y diagramas de las skills.

#### F4-T3: Verificación de coherencia del kit (smoke check manual) ✅
- **Qué:** Pasada final de consistencia: (a) toda skill referenciada por otra existe con ese nombre exacto; (b) todo artefacto que una skill consume es producido por otra (o por `iniciar-harness`); (c) rutas idénticas en todas las skills; (d) `grep` de residuos (nombres viejos, dominio, frontend-design, 🟠).
- **Done:** los 4 checks pasan; resultado anotado al final de este plan.

---

## F5 — Documentación y cierre

#### F5-T1: Actualizar `docs/MANUAL.md` ✅
- **Qué:** Reescribir lo afectado: pipeline con `iniciar-harness`, nombres en español, estados unificados, fase wireframes omisible, perfil de proyecto, referencia a CONVENCIONES (el MANUAL explica el *porqué*; CONVENCIONES el *formato*). Quitar la nota de portabilidad/dogfooding de fit-rutinas del encabezado.
- **Done:** MANUAL consistente con el kit final; sin referencias a fit-rutinas.

#### F5-T2: Actualizar `README.md` ✅
- **Qué:** Tabla de skills final (7: init + 6 fases + mejora — sin frontend-design), estructura del repo actualizada (`_harness/`, `analisis/`), quitar la "Nota de sincronización" del dogfooding y la sección "Pendientes de empaquetado" (reemplazada por este plan). Mantener "Estado: pre-empaquetado".
- **Done:** README refleja el repo real post-plan.

#### F5-T3: Actualizar `CLAUDE.md` del repo ✅
- **Qué:** Reflejar: nombres nuevos, skill inicial, `_harness/` como fuente compartida, eliminación del flujo de dogfooding con fit-rutinas, y que `analisis/plan-mejoras.md` es el tracking activo.
- **Done:** CLAUDE.md sin referencias obsoletas.

#### F5-T4: Cierre — eliminar `docs/MEJORAS-HARNESS.md` (D-3) ✅
- **Qué:** Verificar que las 9 entradas M-001..M-009 quedaron aplicadas (M-001✓ ya estaba, M-002→F3-T2, M-003→F3-T2, M-004→F3-T1, M-005→D-4/F3-T1, M-006✓ ya estaba, M-007✓ + decisión scoped-como-sección ratificada, M-008✓ + patrón nombrado en CONVENCIONES, M-009→F1-T3). Borrar el archivo. Quitar también la sección "Dogfooding del harness" de `desarrollo` **solo la referencia a este repo**; el mecanismo genérico condicional ("si existe un MEJORAS-HARNESS.md") se conserva como feature de auto-mejora para usuarios del kit.
- **Done:** archivo eliminado; tabla de verificación M-001..M-009 completada aquí abajo.

### Verificación final de M-001..M-009 (completar en F5-T4)

| M | Cubierta por | Verificada |
|---|--------------|-----------|
| M-001 | ya aplicada + F1-T1 | ✅ |
| M-002 | F3-T2 | ✅ |
| M-003 | F3-T2 (CLAUDE-proyecto.md) | ✅ |
| M-004 | F3-T1 | ✅ |
| M-005 | D-4 + F3-T1 | ✅ |
| M-006 | ya aplicada + F1-T2 | ✅ |
| M-007 | ya aplicada (scoped = sección, ratificado) | ✅ |
| M-008 | ya aplicada + patrón nombrado en F3-T1 | ✅ |
| M-009 | F1-T3 | ✅ |

---

## Orden de ejecución y dependencias

```
F1 (T5 primero: borra frontend-design antes de tocar referencias)
 → F2 (T1 renombrado primero: el resto de tareas escriben los nombres nuevos)
  → F3 (T1 y T2 antes que T3: las skills se adelgazan contra algo que ya existe)
   → F4 (necesita plantillas y convenciones)
    → F5 (documenta el resultado final y cierra)
```

Regla de trabajo: una fase a la vez, actualizar el tracking de este archivo al cerrar cada tarea (⬜→🟡→✅), y al cerrar cada fase correr el grep de residuos correspondiente.

---

## Resultado del smoke check final (F4-T3) — 2026-06-11

- (a) **Referencias cruzadas:** toda skill referenciada existe con su nombre exacto (`refinar-requerimiento`, `documento-diseno`, `wireframes`, `especificacion-tecnica`, `plan-implementacion`, `desarrollo`, `mejora`, `iniciar-harness`). ✅
- (b) **Cadena de artefactos:** todo artefacto consumido es producido por otra fase o por `iniciar-harness` (INDICE/CONVENCIONES/BACKLOG → init; archivos de control → plan-implementacion desde `_harness/templates/`). ✅
- (c) **Rutas:** convención `documentacion/01..05 + mejoras/` idéntica en todas las skills y en CONVENCIONES §1. ✅
- (d) **Greps de residuos:** `rutina|crossfit|periodización|RPE|openrouter|fit-rutinas|frontend-design|implementation-plan|refine-requirement|design-document|🟠` → **0 resultados** en `skills/`, `docs/`, `README.md`, `CLAUDE.md`. ✅

Notas de cierre:
- `docs/MEJORAS-HARNESS.md` eliminado (D-3); las 9 entradas M-001..M-009 verificadas en la tabla de arriba.
- El mecanismo genérico de auto-mejora ("si existe un MEJORAS-HARNESS.md…") se conservó en `desarrollo` y MANUAL §8 como feature del kit.
- Kit final: 8 skills (init + 6 fases + mejora) + `_harness/` (CONVENCIONES + 6 plantillas).
