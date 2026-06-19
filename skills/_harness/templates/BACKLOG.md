# Backlog del producto — {proyecto}

> Cola viva del alcance a lo largo de los releases. Puente entre "release construido" y "qué sigue".
> NO es la fuente de verdad de los requerimientos (esa es `02-diseno/diseno.md` §2) ni la bitácora del build (`05-desarrollo/bitacora.md`). Aquí se **referencia** y se sigue el estado.
> Es el insumo de alcance de la próxima corrida de `plan-implementacion` y la **única puerta de entrada** de `/mejora`.

**Estados:** ⬜ no iniciado · 🔵 planificado · 📝 documentado (espera OK) · 🟡 en desarrollo · ✅ hecho · ⛔ descartado

## Releases

| Release | Estado | Alcance |
|---------|--------|---------|
| MVP | ⬜ | RF Must. Ver `05-desarrollo/plan.md`. |

## Funcionalidades (RF) fuera del release

| RF | Prioridad | Estado | Release | Nota |
|----|-----------|--------|---------|------|
| RF-N | Should/Could/Won't | ⬜ | — | por qué quedó fuera · →diseno.md |

## Pendientes para producción

> Atajos tomados para construir/probar que hay que resolver —o aceptar conscientemente— antes de exponer el sistema. Se cobran con `_harness/referencias/pre-produccion.md` al preparar el release a prod. Mismo ID `MEJ-nn`.

| Ítem | Estado | Bloquea release a prod | Riesgo si no se resuelve | Ref (ADR/bitácora) |
|------|--------|------------------------|--------------------------|--------------------|
| MEJ-NN | ⬜ | sí/no | — | — |

## Deuda técnica y mejoras diferidas

> Deuda que no condiciona el paso a producción (refactors, mejoras internas, simplificaciones).

| Ítem | Estado | Disparador | Ref (ADR/bitácora) |
|------|--------|------------|--------------------|
| MEJ-NN | ⬜ | — | — |

## Insumos pendientes del usuario

| Insumo | Estado | Impacto | Ref |
|--------|--------|---------|-----|
| — | — | — | — |

## Cómo se groomea

1. Se eligen ítems para un release o mejora → 🔵.
2. Si necesitan definición → vuelven a diseño/spec (vía `/mejora` o re-plan).
3. Se corre `plan-implementacion` sobre ese subconjunto (o el track de `/mejora`).
4. Al cerrar → ✅.
