---
name: iniciar-harness
description: 'Inicializa un proyecto para trabajar con el harness Spec-Design: crea la estructura de carpetas de documentación (convención fija), instala las plantillas (INDICE, CONVENCIONES, BACKLOG), genera o complementa el CLAUDE.md del proyecto, captura el perfil del proyecto (UI/LLM/API) y verifica que todo quedó listo. Es idempotente: detecta lo que ya existe y no pisa nada sin avisar. NO escribe requerimientos, no decide stack, no crea código. Trigger: el usuario quiere empezar un proyecto nuevo con el harness, o adoptar el harness en un proyecto existente ("inicializa el harness", "prepara el proyecto", "configura spec-design"). Slash command sugerido: /iniciar-harness.'
---

# iniciar-harness

Fase de arranque de la metodología Spec-Design (convenciones compartidas: `CONVENCIONES.md` del harness). Se corre **una sola vez** por proyecto, antes de la fase 1:

```
/iniciar-harness (este skill) → 1 refinar-requerimiento → 2 documento-diseno → 3 wireframes* → 4 especificacion-tecnica → 5 plan-implementacion → 6 desarrollo → código
```
\* `wireframes` se omite si el perfil del proyecto no tiene UI.

Su único trabajo es dejar el terreno listo: estructura de carpetas, archivos base y perfil del proyecto. Todo lo demás es de las fases siguientes.

---

## Qué hace este skill

1. **Diagnostica** el estado del proyecto destino (qué existe ya, qué falta).
2. **Pregunta lo mínimo** (nombre, perfil, si hay una idea escrita).
3. **Crea la estructura** de la convención del harness y **copia las plantillas**.
4. **Genera o complementa** el `CLAUDE.md` del proyecto.
5. **Verifica** y reporta con un checklist; handoff a `/refinar-requerimiento`.

## Qué este skill NO hace

- **No escribe el requerimiento** ni contenido de ningún artefacto de fase (las carpetas quedan vacías a propósito).
- **No decide stack, arquitectura ni alcance** — eso es de las fases 2+.
- **No crea código** ni scaffolding de la app (eso es F0 del plan de implementación).
- **No pisa archivos existentes en silencio** — si algo ya existe, lo informa y pregunta antes de tocarlo.
- **No inventa el perfil del proyecto** — lo pregunta.

---

## Fase 0: Diagnóstico

Antes de crear nada, inspeccionar el proyecto destino y reportar en un mensaje breve:

- ¿Existe `documentacion/`? ¿Con qué contenido?
- ¿Existe `CLAUDE.md` en la raíz?
- ¿Es un proyecto nuevo (carpeta casi vacía) o uno existente con código?
- ¿Hay algún `.md` que parezca una idea/requerimiento en bruto?

Tres escenarios:

| Escenario | Acción |
|-----------|--------|
| **Proyecto nuevo** (sin `documentacion/`, sin CLAUDE.md) | Flujo completo (Fases 1–3). |
| **Proyecto existente sin harness** (hay código y/o CLAUDE.md, no hay `documentacion/`) | Flujo completo, pero el CLAUDE.md se **complementa** (ver Fase 2), no se reemplaza. |
| **Harness ya iniciado** (existe `documentacion/INDICE.md`) | Modo **verificación**: reportar qué falta respecto a la convención (carpetas, plantillas, perfil) y ofrecer completar solo los huecos. No re-crear nada. |

---

## Fase 1: Preguntas mínimas

En un solo mensaje (o `AskUserQuestion` si el entorno lo soporta — esto sí es un fork de opciones):

1. **Nombre del proyecto** (para los placeholders `{proyecto}` de las plantillas).
2. **Perfil del proyecto** (`CONVENCIONES.md` §8): ¿tiene UI? ¿integra un LLM? ¿expone API/backend? ¿otra capacidad relevante?
3. **¿Existe ya una idea escrita?** (un `.md` con notas/brainstorm). Si sí, se moverá/copiará a `documentacion/01-requerimiento/` para que la fase 1 la consuma.
4. **Respaldo git (opcional):** ¿quieres que el harness lleve un **respaldo versionado local** de la documentación? (a) **no**, lo manejas tú (default); (b) **sí**, `git commit` local al cerrar cada fase — **sin remoto, sin push**. El repo remoto y el deploy del código quedan siempre fuera del harness.

No preguntar nada más sobre el producto: idioma, stack y alcance pertenecen a las fases siguientes.

---

## Fase 2: Crear estructura + instalar archivos base

1. **Crear las carpetas** de la convención (`CONVENCIONES.md` §1):

```
documentacion/
├── 01-requerimiento/
├── 02-diseno/
├── 03-wireframes/          ← se crea igual aunque no haya UI (quedará "— omitida" en el índice)
├── 04-especificacion-tecnica/
├── 05-desarrollo/
└── mejoras/
```

2. **Copiar las plantillas del kit** (`_harness/templates/` del harness) rellenando `{proyecto}`, `{fecha}` y el perfil:
   - `templates/INDICE.md` → `documentacion/INDICE.md` (con el perfil registrado; si no hay UI, fase 3 → "— omitida").
   - `_harness/CONVENCIONES.md` → `documentacion/CONVENCIONES.md`.
   - `templates/BACKLOG.md` → `documentacion/BACKLOG.md`.
   - `templates/INDICE-mejoras.md` → `documentacion/mejoras/INDICE.md` (ancla la carpeta de mejoras; queda vacío hasta el primer `/mejora`).
   - Los archivos de control de `05-desarrollo/` **no** se crean aquí — los inicializa `plan-implementacion` en la fase 5.

3. **CLAUDE.md del proyecto** (`templates/CLAUDE-proyecto.md`):
   - Si **no existe**: copiarlo rellenando placeholders.
   - Si **existe**: proponer un **merge** — añadir el bloque del harness (pipeline, prácticas, punteros) al CLAUDE.md actual, mostrando el delta antes de aplicar. No reemplazar contenido del usuario.

4. **Idea en bruto** (si la hay): mover/copiar el `.md` a `documentacion/01-requerimiento/` (confirmar antes de mover un archivo del usuario).

5. **Respaldo git** (solo si se activó en la Fase 1): `git init` si el proyecto aún no es un repo; registrar la política en el perfil. A partir de aquí los cierres de fase harán `git commit` **local** de `documentacion/`. El harness **nunca** configura remoto ni hace `push`.

---

## Fase 3: Verificación + handoff

1. **Checklist de verificación** (reportar en el chat):

```
✅ Harness inicializado — {proyecto}

- documentacion/ con las 6 carpetas de la convención
- INDICE.md (perfil: UI {sí/no} · LLM {sí/no} · API {sí/no})
- CONVENCIONES.md instalado
- BACKLOG.md inicializado
- CLAUDE.md {creado / complementado}
- Idea en bruto: {documentacion/01-requerimiento/<archivo> / "no hay aún"}

Siguiente paso:
{si hay idea}  → corre /refinar-requerimiento documentacion/01-requerimiento/<archivo>
{si no la hay} → escribe tu idea en un .md dentro de documentacion/01-requerimiento/ y corre /refinar-requerimiento sobre él
```

2. Marcar nada en el índice (todas las fases quedan ⬜ — este skill no es una fase del pipeline).

---

## Manejo de casos especiales

**`documentacion/` existe con otra estructura (proyecto con docs propias).**
No mover ni renombrar nada del usuario. Proponer: crear las carpetas de la convención junto a lo existente, o (si el usuario prefiere) usar un subdirectorio. Informar que las skills de fase asumen la convención.

**El CLAUDE.md existente contradice las prácticas del harness.**
Señalar el conflicto concreto y dejar que el usuario decida qué manda. No sobreescribir su contenido.

**El usuario quiere arrancar directo con la idea ("aquí está mi doc, parte").**
Inicializar primero (este skill es rápido), mover el doc a `01-requerimiento/` y encadenar el handoff a `/refinar-requerimiento` en el mismo mensaje.

**Re-ejecución sobre un harness ya iniciado.**
Modo verificación (ver Fase 0): solo completar huecos, nunca regenerar `INDICE.md`/`BACKLOG.md` existentes (contienen estado real del proyecto).

---

## Anti-patrones a evitar

- ❌ Sobrescribir o regenerar archivos con estado (INDICE, BACKLOG, CLAUDE.md) en un proyecto ya iniciado.
- ❌ Preguntar decisiones que pertenecen a fases posteriores (stack, alcance, prioridades).
- ❌ Escribir contenido de requerimiento/diseño "para adelantar".
- ❌ Crear scaffolding de código de la app.
- ❌ Inventar el perfil del proyecto en vez de preguntarlo.
- ❌ Mover archivos del usuario sin confirmar.

---

## Output check (definition of done)

- [ ] Las 6 carpetas de `documentacion/` + `mejoras/` (con su `INDICE.md`) existen.
- [ ] `INDICE.md` instalado con el perfil del proyecto registrado (y fase 3 marcada "— omitida" si no hay UI).
- [ ] `CONVENCIONES.md` y `BACKLOG.md` instalados.
- [ ] `CLAUDE.md` creado o complementado (sin pisar contenido del usuario).
- [ ] Política de git registrada en el perfil (y `git init` hecho **solo** si el usuario activó el respaldo).
- [ ] Checklist de verificación reportado con el siguiente paso concreto.
- [ ] Nada existente fue sobrescrito sin aviso.

Si algún check falla, el skill no termina.
