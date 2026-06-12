# CLAUDE.md — {proyecto}

> Proyecto desarrollado con el harness Spec-Design. La fuente de verdad del estado y los artefactos es `documentacion/INDICE.md`; las convenciones (rutas, IDs, estados, patrones) están en `documentacion/CONVENCIONES.md`.

## Cómo se trabaja aquí

- **Pipeline:** `/refinar-requerimiento` → `/documento-diseno` → `/wireframes` (si hay UI) → `/especificacion-tecnica` → `/plan-implementacion` → `/desarrollo`. Post-MVP: `/mejora <id>` sobre `documentacion/BACKLOG.md`.
- **Separación por altitud:** cada decisión vive en una sola fase; no se reabren decisiones desde fases posteriores — se vuelve a la fase dueña.
- **Preguntar, no inventar:** si falta información, se genera `preguntas-*.md` y se espera la respuesta del usuario; no se rellenan huecos.
- **Los contratos mandan:** el código respeta tipos/schemas/endpoints de `04-especificacion-tecnica/`. Si una desviación cambia un contrato o ADR, detenerse y volver a la fase dueña antes de seguir.
- **Memoria:** decisiones/issues/aprendizajes van a `documentacion/05-desarrollo/bitacora.md` (formato `B-{nnn}` con Origen/Refs/Por).
- **Nada se pierde:** alcance que aparece fuera del release en curso → `documentacion/BACKLOG.md`, no al código ni al olvido.

## Prácticas de trabajo

- Verificar antes de cerrar: ninguna tarea se marca ✅ sin pasar su criterio de done (lint/build/tests o verificación manual).
- Gates de fase: review + verify antes de avanzar de fase en desarrollo.
- Gate humano: la documentación de una mejora no fluye a desarrollo autónomo sin OK explícito del usuario.
- Mantener `seguimiento.md` e `INDICE.md` al día al cerrar tareas/fases.

## Proyecto

**Perfil:** UI: {sí/no} · LLM: {sí/no} · API/backend: {sí/no}
**Stack:** {se define en el diseño técnico — ver ADRs en `02-diseno/`}

{Notas específicas del proyecto que no se derivan de la documentación.}
