<div align="center">

# Spec-Design Harness

### El harness que no deja **improvisar** al agente.

De la idea cruda a un MVP sólido. Una cadena de *skills* para Claude Code donde cada fase produce un artefacto con **contrato fijo** que la siguiente consume — sin inventar, sin reabrir lo cerrado.

[![Versión](https://img.shields.io/badge/versi%C3%B3n-0.3.0-2E8B81)](CHANGELOG.md)
![Skills](https://img.shields.io/badge/skills-8-D9952F)
![Pipeline](https://img.shields.io/badge/fases-6_+_ciclo_post--MVP-5E84C9)
![Claude Code](https://img.shields.io/badge/Claude_Code-plugin-16233F)
![Idioma](https://img.shields.io/badge/idioma-Español-be123c)
![Licencia](https://img.shields.io/badge/licencia-MIT-22c55e)

</div>

```bash
❯ /plugin marketplace add gianni-labs/harness-skills-kit
❯ /plugin install spec-design@gianni-labs
```

<div align="center"><sub><code>8 skills</code> · <code>6 fases + ciclo post-MVP</code> · <code>preguntas → merge</code> · <code>MIT</code></sub></div>

---

## El problema

| ✕ &nbsp; Sin harness | ✓ &nbsp; Con harness |
|---|---|
| Le pides a un agente que construya a partir de una idea a medio definir, y **rellena los huecos con suposiciones**: un stack que no elegiste, un alcance que no pediste, estructuras inventadas sobre la marcha. Se ve bien hasta que lo lees. | Lo lleva por **fases de altitud decreciente**. Cada fase **pregunta en vez de inventar**, y una decisión cerrada **nunca se reabre desde abajo**. |

> **qué quieres** → **cómo, a alto nivel** → **contratos exactos** → **orden** → **código**

No es un framework que ejecutas: es una **disciplina operativa** que vive en tus skills de Claude Code.

---

## El pipeline · una fase = una skill = un artefacto

Cada flecha es un **handoff con contrato fijo**: lo que una fase consume es exactamente lo que la anterior produce.

<p align="center"><img src="docs/assets/pipeline.png" alt="El pipeline de fases" width="880"></p>

| # | Skill | Consume | Produce |
|---|-------|---------|---------|
| **arranque** | `iniciar-harness` | — | estructura, plantillas, perfil (UI/LLM/API), `CLAUDE.md` |
| **1** | `refinar-requerimiento` | idea cruda | requerimiento limpio + preguntas |
| **2** | `documento-diseno` | requerimiento | diseño funcional + técnico (EARS · MoSCoW · FURPS+ · ADR) |
| **3** *(con UI)* | `wireframes` | §Vistas + §Flujos | wireframes ASCII + mapa de navegación |
| **4** | `especificacion-tecnica` | diseño + wireframes | tipos + contratos de API + módulos |
| **5** | `plan-implementacion` | especificación | fases → tareas + `BACKLOG.md` |
| **6** | `desarrollo` | plan + contratos | código + bitácora · **MVP verificado** |
| **post-MVP** | `mejora` | `BACKLOG.md` | router adaptativo (tracks A/B/C/D) + gate humano |

> La fase de wireframes **se omite sola** si el proyecto no tiene UI (CLI, API, librería). El ciclo post-MVP reaplica solo las fases que el cambio necesita.

---

## El MVP es un hito, no el final

La primera pasada entrega un **MVP funcional**, sin alcance inflado. Lo que queda fuera no se pierde — vive en `BACKLOG.md`. El skill `/mejora` elige **cuánto harness aplicar según el impacto del cambio**, reutilizando lo ya documentado en vez de reescribirlo.

| Track | Cuándo | Qué documenta |
|:---:|---|---|
| **A — Mínimo** | deuda técnica, toggle, refactor | solo `plan.md` |
| **B — UI** | feature visible sin tocar contratos | diseño ligero + wireframes + plan |
| **C — Contratos** | toca tipos / schema / endpoints / datos | diseño + especificación + plan |
| **D — Grande** | feature arquitectónica | ciclo completo (mini-MVP) |

> Ante la duda entre dos tracks, gana el más riguroso. Cada mejora vive como un *delta* que **referencia** el baseline sin reabrirlo.

<p align="center"><img src="docs/assets/ciclo-mejoras.png" alt="Ciclo de mejoras post-MVP" width="880"></p>

---

## Qué lo hace distinto

| | |
|---|---|
| **🔀 Preguntas → merge** | Cuando falta información, la skill genera un `preguntas-*.md` con opciones `(a)(b)(c)`. Respondes a tu ritmo y la skill integra. Cero invención. |
| **✋ Gate humano formalizado** | La documentación nunca fluye a desarrollo autónomo sin tu OK explícito. Codificado como una *parada de aprobación*. |
| **🧠 Memoria trazable** | Una bitácora con referencia cruzada (`B-007 → ADR-2 / RF-5 / tarea F2-T3`) registra el porqué de cada decisión. |
| **🛡️ Calidad sin perder disciplina** | Tablas **anti-racionalización** (excusa → realidad) + personas de revisión (`revisor-codigo`, `auditor-seguridad`) que el gate invoca. |
| **🎯 Perfil de proyecto** | Omite fases según el perfil (UI / LLM / API). Sin UI, la fase de wireframes se salta sola. |

---

## Dónde encaja

> **Orquestador con contratos y memoria — no otro catálogo de prácticas.** Responde *en qué orden, con qué contratos, con qué memoria, y qué pasa después del MVP*.

| Capacidad | Harness | Spec Kit | Kiro | BMAD |
|---|:---:|:---:|:---:|:---:|
| Fases con contrato fijo | ✓ | ✓ | ✓ | ✓ |
| Preguntar en vez de inventar | ✓ | ~ | ~ | ✕ |
| Ciclo de vida post-MVP | ✓ | ✕ | ✕ | ✕ |
| Gate de aprobación humano | ✓ | ✕ | ✕ | ~ |
| Memoria con trazabilidad cruzada | ✓ | ✕ | ~ | ✕ |
| Perfil que omite fases (UI/LLM/API) | ✓ | ✕ | ✕ | ✕ |

`agent-skills` (y similares) responden *"¿qué práctica aplico?"*; este harness responde *"¿en qué orden, con qué contratos, con qué memoria, y qué pasa después del MVP?"*. Son **complementarios**.

*Fuentes:* [github/spec-kit](https://github.com/github/spec-kit) · [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) · [Claude Code Plugins](https://code.claude.com/docs/en/plugin-marketplaces)

---

## ⚡ Instalación

**Opción A — Como plugin** *(recomendado)*

```bash
❯ /plugin marketplace add gianni-labs/harness-skills-kit
❯ /plugin install spec-design@gianni-labs
```

**Opción B — Instalación manual**

```bash
❯ git clone https://github.com/gianni-labs/harness-skills-kit.git
❯ cp -R harness-skills-kit/skills/. tu-proyecto/.claude/skills/
```

> Copia también `skills/_harness/` — contiene las convenciones y plantillas que las skills necesitan.

---

## 🚀 Uso rápido

```bash
# de la idea al MVP
❯ /iniciar-harness                                    # estructura + perfil
❯ /refinar-requerimiento idea.md                      # idea cruda → requerimiento
❯ /documento-diseno · /wireframes                     # diseño (wireframes se omite si no hay UI)
❯ /especificacion-tecnica · /plan-implementacion      # contratos → plan
❯ /desarrollo                                         # construye el MVP

# después del MVP, itera desde el backlog
❯ /mejora RF-15
```

En cada fase de diseño, la skill te deja un archivo `preguntas-*.md`. Lo respondes, le avisas, y la skill integra tus respuestas antes de cerrar. **Nada avanza sobre suposiciones.**

---

## 🧪 Validado en la práctica

El harness se probó **end-to-end** sobre un proyecto real: de una idea en una frase a un **MVP funcional verificado**, en una sola corrida. La separación por fases evitó el sobre-diseño (una base de datos completa quedó correctamente diferida al backlog en vez de colarse al MVP).

<p align="center"><img src="docs/assets/caso-estudio-desktop.png" alt="MVP construido con el harness" width="760"></p>

📖 **[Lee el caso de estudio completo →](docs/CASO-ESTUDIO.md)**

---

## 📂 Estructura del repo

```
harness-skills-kit/
├── skills/
│   ├── <fase>/SKILL.md        # las 8 skills instalables — una por fase del pipeline
│   └── _harness/              # lo compartido (nunca duplicado en una skill)
│       ├── CONVENCIONES.md    #   única fuente: rutas, IDs, estados, patrón preguntas→merge
│       ├── templates/         #   plantillas que las skills copian al proyecto destino
│       ├── referencias/       #   checklists de calidad (seguridad, accesibilidad, performance, testing)
│       └── agentes/           #   personas de revisión (revisor-codigo, auditor-seguridad)
├── docs/MANUAL.md             # el porqué del harness
├── docs/CASO-ESTUDIO.md       # prueba empírica end-to-end (idea → MVP)
└── .claude-plugin/            # manifiesto para instalar como plugin de Claude Code
```

**Una fase = una skill = un artefacto**, y **todo lo compartido vive en `_harness/`**.

---

## 🤝 Contribuir

Las propuestas de mejora se discuten en *issues*. Si usas el kit en un proyecto real y encuentras una fricción del flujo, ese feedback es justamente lo que lo hace evolucionar.

## 📄 Licencia

MIT — ver [`LICENSE`](LICENSE).

<div align="center">
<br>
<img src="docs/assets/mascota.png" alt="" width="64">
<br>
<sub><strong>el flujo correcto · las paradas correctas · la trazabilidad de cada decisión</strong></sub>
</div>
