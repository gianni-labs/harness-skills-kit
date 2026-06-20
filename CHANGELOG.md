# Changelog

Todos los cambios notables del harness Spec-Design se documentan acá.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es/1.1.0/) y el versionado sigue [SemVer](https://semver.org/lang/es/), adaptado a un kit de skills:

- **MAJOR** — rompe contratos entre fases o el flujo (proyectos existentes deben rehacer).
- **MINOR** — capacidades aditivas que no rompen nada.
- **PATCH** — correcciones de redacción o bugs sin cambio de comportamiento.

## [0.3.0] — 2026-06-20

Mejoras derivadas de la **primera validación empírica end-to-end** del harness (experimento autónomo con Codex: de idea cruda a MVP funcional). Aditivas; **no tocan los contratos entre fases** ni el flujo.

### Añadido
- **Gate pre-producción** en `desarrollo` + sección "Pendientes para producción" en el `BACKLOG`, con la referencia `_harness/referencias/pre-produccion.md`.
- **`AGENTS.md`** — espejo de `CLAUDE.md` para Codex y otros agentes que leen `AGENTS.md`; el repo queda legible fuera de Claude Code.
- **Modo directo formal** en `iniciar-harness` (Fase 0/1 + caso especial), alineado a `CONVENCIONES.md` §9: no se detiene a preguntar lo que el contexto inicial ya trae.
- **Checklist de gate manual** (fallback sin herramientas) en `desarrollo`, condicionado por perfil del proyecto.
- **Agentes provistos por el plugin**: `.claude-plugin/plugin.json` declara `revisor-codigo` y `auditor-seguridad` como sub-agentes invocables (antes solo eran Markdown de referencia).
- **`docs/CASO-ESTUDIO.md`** — prueba empírica end-to-end (idea → MVP) con diagramas y capturas reales del MVP construido (coach LLM en vivo); enlazado desde el README.

### Cambiado
- **`desarrollo`**: dogfooding aclarado como **modo contribuidor opt-in**, no auto-mejora automática para el usuario final.
- **Consistencia de redacción** en las skills (hallazgos P1 del análisis): fuentes técnicas (`diseno-tecnico.md` o §técnico integrado) y wording de fases.
- **`.gitignore`**: `codex-docs/` añadido como working docs internos (no se publican).

## [0.2.0] — 2026-06-14

Capacidades de calidad transversal, inspiradas en patrones de `addyosmani/agent-skills`, **sin tocar el núcleo** (contratos entre fases, preguntas→merge, gates, ciclo post-MVP, trazabilidad). Todo aditivo.

### Añadido
- **Tablas anti-racionalización** (`excusa → realidad`) en las 8 skills, para desarmar los atajos típicos del modelo en cada fase.
- **`skills/_harness/referencias/`** — checklists de calidad desacopladas, consultadas bajo demanda y condicionadas por perfil: `seguridad.md`, `accesibilidad.md`, `performance.md`, `testing.md`.
- **`skills/_harness/agentes/`** — personas de revisión adversarial invocadas desde los gates: `revisor-codigo` (5 ejes) y `auditor-seguridad` (OWASP), opcionales con fallback manual.
- **`plan-implementacion`**: tabla de tamaño de tarea (XS–XL) como criterio de atomicidad + checkpoints intermedios para fases largas.
- **`desarrollo`**: guía de implementación (rebanada vertical, rojo-verde-refactor), verificación con evidencia, revisión de 5 ejes en el gate y sección de triage de bugs (5 pasos).

### Cambiado
- **`CONVENCIONES.md`**: §10 incorpora "Racionalizaciones" al esqueleto de skill; nuevas §12 (referencias) y §13 (personas).
- **`docs/MANUAL.md`**: nueva §7.bis sobre recursos de calidad transversales.
- **README**: pipeline como imagen auto-explicativa, tabla "Consume → Produce", estructura del repo anotada, posicionamiento frente a `agent-skills` (orquestador vs. catálogo), énfasis en el ciclo MVP → mejoras, imágenes de altitudes y ciclo post-MVP con texto, mascota.

### Eliminado
- Material interno fuera del control de versiones: `analisis/` y `docs/assets/PROMPTS-IMAGENES.md` (working docs locales, ahora en `.gitignore`); comentarios HTML con prompts dentro del README.

## [0.1.0]

Primera versión empaquetable del harness.

### Añadido
- Las **8 skills** de la metodología Spec-Design (`iniciar-harness`, `refinar-requerimiento`, `documento-diseno`, `wireframes`, `especificacion-tecnica`, `plan-implementacion`, `desarrollo`, `mejora`), genéricas y desacopladas de cualquier dominio.
- **`CONVENCIONES.md`** como única fuente de lo compartido (rutas, IDs, estados, patrón preguntas→merge, gate humano, perfil, modo directo).
- **Plantillas** del kit (`INDICE`, `BACKLOG`, `bitacora`, `seguimiento`, `CLAUDE` del proyecto destino).
- **`docs/MANUAL.md`** con el porqué del harness.
- Empaquetado como **plugin de Claude Code** (`.claude-plugin/`).
- Licencia MIT.

[0.2.0]: https://github.com/gianni-labs/harness-skills-kit/releases/tag/v0.2.0
[0.1.0]: https://github.com/gianni-labs/harness-skills-kit/releases/tag/v0.1.0
