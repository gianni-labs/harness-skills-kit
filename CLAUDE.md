# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Kit empaquetable de un **harness de desarrollo asistido por IA** (metodología Spec-Design). No hay código ejecutable, build ni tests: todo el contenido son archivos Markdown — los `SKILL.md` son los artefactos instalables y `docs/MANUAL.md` la documentación conceptual. "Editar código" aquí significa editar prompts/contratos de skills.

El harness es **genérico por diseño**: ninguna skill puede referenciar un dominio o proyecto particular. El **perfil del proyecto** (UI/LLM/API) activa fases y secciones condicionales.

## Arquitectura

```
/iniciar-harness → 1 refinar-requerimiento → 2 documento-diseno → 3 wireframes* → 4 especificacion-tecnica → 5 plan-implementacion → 6 desarrollo
```
\* omisible si el proyecto no tiene UI. Más `mejora` (router post-MVP con tracks A/B/C/D sobre `BACKLOG.md`).

- `skills/_harness/CONVENCIONES.md` — **única fuente de lo compartido**: rutas (convención fija `documentacion/01..05`), IDs, estados, patrón preguntas→merge, gate humano vs AskUserQuestion, esqueleto de skill, perfil, modo directo. Las skills referencian este doc y solo definen sus deltas.
- `skills/_harness/templates/` — plantillas que las skills copian (INDICE, BACKLOG, bitácora, seguimiento, herramientas, CLAUDE del proyecto destino). No incrustar plantillas en los SKILL.md.
- `skills/_harness/referencias/` — checklists de calidad desacopladas (seguridad, accesibilidad, performance, testing) que las fases **consultan bajo demanda**; recurso opcional condicionado por perfil (CONVENCIONES §12). No incrustar su contenido en los SKILL.md.
- `skills/_harness/agentes/` — personas de revisión adversarial (`revisor-codigo`, `auditor-seguridad`) que `desarrollo` invoca como sub-agente en los gates; opcionales con fallback manual (CONVENCIONES §13).
- `docs/MANUAL.md` — el *porqué* del harness. Cambios estructurales a una skill deben reflejarse ahí y en CONVENCIONES si tocan lo compartido.
- `analisis/` — working docs del kit: `analisis-harness.md` (diagnóstico) y `plan-mejoras.md` (tracking de mejoras aplicadas al kit).

## Invariantes al editar skills

Las skills forman una cadena de contratos: cambiar lo que una fase **produce** rompe lo que la siguiente **consume**. Antes de modificar un `SKILL.md`, revisar la fase anterior y la siguiente.

Reglas que las skills codifican (no eliminarlas al refactorizar):
- Separación por altitud: cada decisión vive en una sola fase; nunca se reabre desde una fase posterior.
- "Preguntar, no inventar": `preguntas-*.md` + merge (formato en CONVENCIONES §5).
- `AskUserQuestion` solo para forks reales; un gate de aprobación es una parada, no un menú.
- Gate humano entre documentación y desarrollo autónomo (`📝 documentado (espera OK)`).
- Herramientas del entorno (`code-review`, `verify`…) son opcionales con fallback manual; los gates no se saltan.

Secciones que cada skill de fase mantiene (esqueleto en CONVENCIONES §10): contrato de entrada, qué hace / qué NO hace, Fase 0 (con modo directo), generación, merge, cierre+INDICE, modo mejora (scoped), casos especiales, anti-patrones, racionalizaciones (excusa→realidad), output check.

## Convenciones de IDs y estados

IDs: `RF-N`, `RNF-N`, `F-N`, `V-N`, `ADR-N`, `F{n}-T{m}`, `B-{nnn}`, `MEJ-{nn}` (ver CONVENCIONES §3). Estados únicos: `⬜ 🔵 📝 🟡 ✅ ⛔` (CONVENCIONES §4) — no introducir variantes.

## Al agregar o cambiar contenido

- Mantener todo **genérico**: ejemplos de dominio neutro o placeholders. Verificar con `grep -ri "<término de dominio>"` antes de cerrar.
- Nombres de skills y comandos en **español**.
- Lo compartido va a CONVENCIONES/templates, nunca duplicado en una skill.
