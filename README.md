# harness-skills-kit

Kit reutilizable de un **harness de desarrollo asistido por IA** basado en la metodología **Spec-Design**: una cadena de *skills* donde cada fase produce un artefacto con contrato fijo que la siguiente consume, llevando un proyecto de la idea cruda al código de forma ordenada, trazable y segura.

> **Estado:** pre-empaquetado. El harness es **genérico** (no asume dominio, tipo de app ni stack) y usable desde cero vía `/iniciar-harness`. La distribución (instalador/plugin) está pendiente.

## El pipeline

```
/iniciar-harness → idea → 1 refinar-requerimiento → 2 documento-diseno → 3 wireframes* → 4 especificacion-tecnica → 5 plan-implementacion → 6 desarrollo → código
```
\* `wireframes` se omite si el perfil del proyecto no tiene UI.

| Skill | Rol | Produce |
|-------|-----|---------|
| `iniciar-harness` | arranque (no es fase) | estructura `documentacion/`, plantillas, perfil del proyecto, CLAUDE.md |
| `refinar-requerimiento` | fase 1 | requerimiento limpio + preguntas |
| `documento-diseno` | fase 2 | diseño funcional + técnico (RF/RNF/ADR) |
| `wireframes` | fase 3 (solo con UI) | wireframes ASCII + mapa de navegación |
| `especificacion-tecnica` | fase 4 | tipos, contratos de API, módulos (secciones condicionales por perfil) |
| `plan-implementacion` | fase 5 | plan Fases→Tareas + archivos de control + BACKLOG |
| `desarrollo` | fase 6 | código + seguimiento/bitácora |
| `mejora` | ciclo post-MVP | router adaptativo: toma un ítem del backlog y aplica solo las fases necesarias (tracks A/B/C/D) con gate de aprobación humano |

## Estructura del repo

```
skills/<nombre>/SKILL.md       ← las 8 skills instalables
skills/_harness/CONVENCIONES.md ← única fuente de lo compartido (rutas, IDs, estados, patrones)
skills/_harness/templates/      ← plantillas (INDICE, BACKLOG, bitácora, seguimiento, CLAUDE del proyecto…)
docs/MANUAL.md                  ← manual del harness (el porqué)
analisis/                       ← análisis de diseño y plan de mejoras del kit (working docs)
```

## Cómo se usa (hoy)

1. Copia `skills/*` al `.claude/skills/` del proyecto destino (o a `~/.claude/skills/` para uso global).
2. Corre `/iniciar-harness` en el proyecto: crea la estructura, instala las plantillas y captura el perfil.
3. Sigue el pipeline fase por fase (ver `docs/MANUAL.md` §5).

## Diferenciadores frente a otros frameworks spec-driven

- **Patrón preguntas→merge asíncrono:** las skills preguntan en archivos `preguntas-*.md` (con opciones y campo `R:`) en vez de inventar — y hacen merge de tus respuestas.
- **Ciclo de vida post-MVP:** el harness no termina en el primer release; `BACKLOG.md` + `/mejora` iteran con rigor proporcional al impacto.
- **Gate humano formalizado:** la documentación nunca fluye a desarrollo autónomo sin OK explícito.
- **Memoria trazable:** bitácora con referencia cruzada (`B-nnn` → ADR/RF/tarea) y auto-mejora opcional del propio harness.
