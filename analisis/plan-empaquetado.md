# Plan de empaquetado como plugin de Claude Code

> Rama: `empaquetado-plugin` (aislada de `main`, que mantiene el kit estable instalable por clone+copiar).
> Objetivo: distribuir el kit como plugin instalable con `/plugin install`, sin perder la opción manual.

## Decisiones abiertas (confirmar)

| # | Tema | Estado / propuesta |
|---|------|--------------------|
| DEC-1 | **Licencia** | ✅ **MIT** — `LICENSE` creado. |
| DEC-2 | **Nombre del plugin** | ✅ `spec-design` — confirmado. |
| DEC-3 | **Nombre del marketplace** | ✅ `gianni-labs` — comando: `/plugin install spec-design@gianni-labs`. |

## Tracking

> Estados: ⬜ pendiente · 🟡 en curso · ✅ hecho

| Tarea | Descripción | Estado |
|-------|-------------|--------|
| E-1 | Estructura base: `.claude-plugin/plugin.json` + `marketplace.json` | ✅ |
| E-2 | Resolver rutas de `_harness/` (ver abajo) | ✅ |
| E-3 | Agregar `LICENSE` (según DEC-1) | ✅ |
| E-4 | Ajustar README: instalación como plugin disponible (quitar "próximamente") | ✅ |
| E-5 | Probar instalación local (marketplace add + install + correr `iniciar-harness`) | ⬜ |
| E-6 | Hacer repo público + merge a `main` + tag `v0.1.0` | ⬜ |

---

## E-1 · Estructura base ✅
- Creados `.claude-plugin/plugin.json` (ficha del plugin) y `.claude-plugin/marketplace.json` (catálogo, `source: "./"` = plugin en la raíz del mismo repo). JSON validados.

## E-2 · Resolver rutas de `_harness/` ⬜
El problema: un plugin se instala en una caché interna, no en `.claude/skills/`. Las referencias relativas/ambiguas a `_harness/` dejan de ser fiables. Solución (separar leer de copiar):
- **Skills que solo LEEN convenciones** (las 6 de fase + mejora): cambiar las referencias de `CONVENCIONES.md` → **`documentacion/CONVENCIONES.md`** (la copia que `iniciar-harness` deja en el proyecto; ruta estable, no depende del plugin).
- **Skills que COPIAN plantillas** (`iniciar-harness`, `plan-implementacion`): referenciar los originales con **`${CLAUDE_PLUGIN_ROOT}/skills/_harness/...`** (la caché del plugin instalado).
- Verificar: tras `iniciar-harness`, ninguna skill de fase necesita la ruta del plugin.

## E-3 · LICENSE ⬜
- Crear `LICENSE` (MIT propuesto, DEC-1). Alinear el campo `license` de `plugin.json`.

## E-4 · README ⬜
- La sección de instalación ya describe ambos métodos; al publicar, marcar el plugin como disponible (quitar el "(próximamente)") y dejar `clone+copiar` como alternativa.

## E-5 · Prueba local ⬜
- `/plugin marketplace add <ruta-local-o-repo>` → `/plugin install spec-design@gianni-labs`.
- Verificar que las 8 skills cargan como comandos y que `iniciar-harness` corre y copia las plantillas correctamente (valida E-2).

## E-6 · Publicar ⬜
- Repo público; merge `empaquetado-plugin` → `main`; tag `v0.1.0`.
- Validación final: instalar desde el repo público en un proyecto limpio.
