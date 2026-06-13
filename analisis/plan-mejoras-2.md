# Plan de mejoras del harness — Ronda 2 (final, post-dogfooding)

> Generado el 2026-06-12 a partir del log de dogfooding `calculadora-pesos/harness/MEJORAS-HARNESS.md` (M-001…M-008), tras cerrar el MVP del proyecto piloto.
> Alcance: aplicar al kit las mejoras detectadas usándolo de verdad. Es la **última ronda** antes de dar el harness por estable.
> La ronda 1 (`plan-mejoras.md`) ya está aplicada (genérico + skill inicial + convenciones). Esta ronda pule el flujo con lo aprendido en uso real.

## Decisiones propuestas (confirmar al revisar)

Mi recomendación para las pocas decisiones abiertas. Si estás de acuerdo, ejecuto tal cual; si no, ajustamos.

| # | Tema (mejora) | Recomendación |
|---|---------------|---------------|
| DEC-A | **M-001 log de eventos JSONL** | **Diferir** como mejora opcional futura (no entra en esta ronda). Es la más pesada (toca las 8 skills o exige hooks) y su valor es proporcional a proyectos grandes/multi-sesión. El resto de la ronda es higiene/convención de mayor ROI. Queda documentada para retomar si aparece la necesidad. |
| DEC-B | **M-004 herramientas-recomendadas** | **Colapsar:** eliminar el archivo de control separado; las herramientas opcionales se quedan en los gates de `desarrollo` + una línea en CONVENCIONES. Baja de 4 a 3 los archivos de control. |
| DEC-C | **M-006 checkbox del plan** | **Quitar** el `[ ] Hecho` del formato de tarea. `seguimiento.md` = única fuente de estado; `plan.md` = definición. |
| DEC-D | **M-008 carpeta mejoras/** | `mejoras/` con un `INDICE.md` (índice vacío). Sin prefijo numérico (no es una fase). Opción: `_mejoras/` si prefieres el guion bajo como marca de "post-fases". |
| DEC-E | **M-002 archivos intermedios** | Mover automático a un `_archivo/` por carpeta de fase al cerrar (idea original + `preguntas-*.md` respondidos), sin preguntar. Default conservador: archivar, no borrar. |
| DEC-F | **M-003 gestión git** | Opt-in en el onboarding (`iniciar-harness` pregunta la política). Si se activa: commit **local** en cada gate de fase, mensaje estándar, **sin remoto/push**. Default si no responde: no tocar git. |

## Tracking

> Estados: ⬜ pendiente · 🟡 en curso · ✅ hecho · ⛔ bloqueado

| Fase | Objetivo | Mejoras | Tareas | Estado |
|------|----------|---------|--------|--------|
| A — Higiene | arreglos rápidos, alto ROI, baja controversia | M-006, M-007, M-008, +IDs mejora | 4 | ✅ 4/4 |
| B — Convenciones de flujo | fricciones de uso real | M-004, M-002, M-003 | 3 | ✅ 3/3 |
| C — Expectativa + opcional | doc y mejora diferida | M-005, M-001 | 2 | ✅ 2/2 |
| D — Cierre | docs + verificación + sincronizar al kit | — | 2 | ✅ 2/2 |

**Progreso global:** ██████████ 100% (11/11) — completado 2026-06-12

---

## Fase A — Higiene

#### A-1 · M-006: quitar el checkbox redundante del plan ✅
- **Cambio:** en `plan-implementacion`, eliminar `- [ ] Hecho` del formato de tarea. Nota: el estado vive en `seguimiento.md`.
- **Archivos:** `skills/plan-implementacion/SKILL.md` (formato de tarea + output check).
- **Done:** el formato de tarea ya no tiene checkbox; el plan no duplica estado.

#### A-2 · M-007: barra de progreso fija y simple ✅
- **Cambio:** reforzar en la plantilla y en `desarrollo` que la barra ASCII + % es obligatoria y se recalcula en cada actualización. Mantener minimalista.
- **Archivos:** `skills/_harness/templates/seguimiento.md` (ya la tiene; añadir nota de "siempre presente"), `skills/desarrollo/SKILL.md` (instrucción de recálculo).
- **Done:** plantilla e instrucción dejan la barra como elemento fijo.

#### A-3 · M-008: anclar `documentacion/mejoras/` ✅
- **Cambio:** `iniciar-harness` crea `mejoras/INDICE.md` (desde nueva plantilla) al inicializar. Decidir prefijo (DEC-D). Documentar en CONVENCIONES §1.
- **Archivos:** nueva `skills/_harness/templates/INDICE-mejoras.md`, `skills/iniciar-harness/SKILL.md`, `skills/_harness/CONVENCIONES.md` (§1).
- **Done:** un proyecto recién iniciado tiene `mejoras/INDICE.md`; el árbol no queda con la carpeta suelta y vacía.

#### A-4 · Consistencia de IDs de tarea en modo mejora ✅
- **Cambio:** explicitar que el `plan.md` de una mejora usa el formato `F{n}-T{m}` (igual que el MVP), no `T-01`. Detectado al revisar `MEJ-04-dark-mode` del experimento (usó `T-01/T-02`).
- **Archivos:** `skills/_harness/CONVENCIONES.md` (§3), `skills/plan-implementacion/SKILL.md` (modo mejora).
- **Done:** la convención de IDs de tarea es única para MVP y mejoras.

## Fase B — Convenciones de flujo

#### B-1 · M-004: colapsar herramientas-recomendadas ✅
- **Cambio:** eliminar `templates/herramientas-recomendadas.md`; quitar su inicialización de `plan-implementacion`; dejar las herramientas opcionales en los gates de `desarrollo` (ya están) + una línea en CONVENCIONES §11.
- **Archivos:** borrar plantilla; `skills/plan-implementacion/SKILL.md`, `skills/desarrollo/SKILL.md`, `skills/_harness/CONVENCIONES.md`, MANUAL §4.
- **Done:** `05-desarrollo/` inicializa 3 archivos de control (no 4); sin referencias colgando a la plantilla borrada.

#### B-2 · M-002: archivado automático de intermedios ✅
- **Cambio:** nueva convención de archivado: al cerrar una fase, mover la idea original / los `preguntas-*.md` respondidos a un `_archivo/` dentro de la carpeta de la fase, automático y sin preguntar (DEC-E). Reemplaza el "menú de cierre" que hoy ofrece cada skill.
- **Archivos:** `skills/_harness/CONVENCIONES.md` (nueva §archivos intermedios), las 6 skills de fase (sección de cierre: sustituir el "ofrecer borrar/renombrar" por "archivar según convención").
- **Done:** ninguna skill pregunta qué hacer con los intermedios; quedan en `_archivo/` por defecto.

#### B-3 · M-003: gestión de git (commit local, opt-in) ✅
- **Cambio:** `iniciar-harness` pregunta la política de git (no tocar / commit local en gates). Si se activa, `desarrollo` y los cierres de fase hacen commit **local** acotado, mensaje estándar, sin remoto. Registrar la preferencia en el perfil del proyecto.
- **Archivos:** `skills/iniciar-harness/SKILL.md`, `skills/desarrollo/SKILL.md` (gates), `skills/_harness/CONVENCIONES.md` (§8 perfil + §VCS), MANUAL.
- **Done:** la política se elige una vez y se aplica en silencio; el harness nunca pushea ni crea repos.

## Fase C — Expectativa + opcional

#### C-1 · M-005: comunicar "MVP visual simple → escalable" ✅
- **Cambio:** dejar explícito en `wireframes` y en el cierre del MVP de `desarrollo` que la UI del MVP es base funcional, no identidad de marca; documentar el camino de pulido visual vía `DESIGN.md` + track B de `/mejora`.
- **Archivos:** `skills/wireframes/SKILL.md`, `skills/desarrollo/SKILL.md` (gate de release), `skills/_harness/CONVENCIONES.md` (§7 DESIGN.md).
- **Done:** la simpleza del MVP queda como decisión documentada con un camino de escalada claro. (Doc-only, sin código.)

#### C-2 · M-001: log de eventos JSONL ✅ *(diferida — DEC-A)*
- **Decisión:** **no se implementa en esta ronda.** Queda documentada en el log (M-001) con su diseño (JSONL, schema cerrado, best-effort vs hook). Se retoma si aparece la necesidad (proyectos grandes / tooling tipo `/harness-status`).
- **Done:** marcada como diferida; sin cambios en skills.

## Fase D — Cierre

#### D-1 · Actualizar documentación del kit ✅
- **Cambio:** reflejar en MANUAL, README, CLAUDE.md y CONVENCIONES los cambios de las fases A–C (3 archivos de control, archivado, git opt-in, índice de mejoras).
- **Done:** docs coherentes con el kit final.

#### D-2 · Verificación + sincronización ✅
- **Cambio:** smoke check de coherencia (referencias cruzadas, plantillas que existen, sin restos de `herramientas-recomendadas` ni del checkbox). Marcar M-001…M-008 como `aplicada`/`diferida` en el log del experimento.
- **Done:** checks pasan; log del experimento actualizado.

---

## Orden de ejecución

```
A (higiene, sin dependencias) → B (convenciones; B-1 antes de tocar plan/desarrollo) → C (doc) → D (cierre)
```

Regla de trabajo: una fase a la vez, actualizar este tracking al cerrar cada tarea (⬜→✅).
