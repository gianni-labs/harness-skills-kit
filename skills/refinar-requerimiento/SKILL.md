---
name: refinar-requerimiento
description: Convierte un documento de requerimiento en bruto (idea, brainstorm, notas) en un requerimiento limpio, reorganizado y validado, listo para alimentar al agente de diseño funcional/técnico. NO agrega contenido nuevo ni decisiones técnicas — solo limpia, reordena y detecta huecos para preguntárselos al usuario. Trigger: el usuario apunta a un documento (ej. `requerimiento.md`) y pide "afinar", "reordenar", "limpiar", "mejorar", "ordenar" o "preparar para diseño". Slash command sugerido: `/refinar-requerimiento <archivo>`.
---

# refinar-requerimiento

Primera fase de la metodología Spec-Design (convenciones compartidas: `CONVENCIONES.md` del harness):

```
/iniciar-harness → [idea en bruto]
                        ↓
        1 refinar-requerimiento (este skill)
                        ↓
        2 documento-diseno → 3 wireframes* → 4 especificacion-tecnica → 5 plan-implementacion → 6 desarrollo → código
```
\* `wireframes` se omite si el proyecto no tiene UI (perfil del proyecto).

El objetivo es producir un requerimiento **limpio, sin ambigüedades y con alcance explícito** (MVP / deseables / fuera de scope / pendientes), sin contaminarlo con decisiones de arquitectura ni con propuestas técnicas. Eso vive en el documento de diseño posterior.

---

## Qué hace este skill

1. Lee un documento de requerimiento en bruto (por convención, en `documentacion/01-requerimiento/`).
2. Genera dos archivos en el mismo directorio:
   - `<nombre>-v2.md` — versión limpia y reorganizada.
   - `preguntas.md` — preguntas numeradas para validar huecos y ambigüedades.
3. Espera a que el usuario responda `preguntas.md`.
4. Integra las respuestas en `<nombre>-v2.md` (no como apéndice).
5. Hace **preguntas de seguimiento cortas** si las respuestas abren nuevos hilos.
6. Cierra el documento y actualiza `documentacion/INDICE.md` (fase 1 → ✅).

## Qué este skill NO hace

Esta lista es **el corazón del skill**. Romperla degrada el output.

- **No agrega contenido nuevo** que el usuario no haya dicho. Si una sección está vacía, queda vacía (o se convierte en pregunta).
- **No propone stack tecnológico** ni alternativas (DB, framework, hosting, librerías). Si el usuario las menciona, se respetan tal cual; si no, no se inventan.
- **No diseña arquitectura** ni propone estructuras de datos, endpoints, modelos de tablas, etc.
- **No decide** cosas que son del usuario (alcance del MVP, qué entra en deseable, qué queda fuera).
- **No reescribe la voz** del usuario. Si escribe simple y no-técnico, el output queda igual de simple.
- **No agrega comentarios de "buenas prácticas"** ni recomendaciones que el usuario no pidió.
- **No mezcla preguntas dentro del requerimiento** — siempre archivo separado.

> Esta restricción es contraintuitiva para un LLM. Resistir el impulso de "ayudar" agregando es la disciplina central del skill.

---

## Fase 0: Aclaraciones iniciales (antes de tocar nada)

Antes de escribir cualquier archivo, confirmar **en un solo mensaje breve**:

1. **Destino del archivo final:** ¿sobrescribir el original o crear `<nombre>-v2.md` y dejar el original intacto?
2. **Confirmar enfoque:**
   - Idioma: heredado del documento original (no asumir, decirlo explícitamente).
   - Tono: heredado del original. Si es coloquial / no técnico, mantenerlo.
   - Preguntas en archivo separado (`preguntas.md`).
   - No se agrega contenido nuevo ni decisiones técnicas.
3. **Perfil del proyecto:** si no está ya capturado en `documentacion/INDICE.md` (proyecto iniciado sin `/iniciar-harness`), preguntarlo aquí — ¿UI? ¿integra LLM? ¿API/backend? — y registrarlo al inicio del requerimiento cerrado (ver `CONVENCIONES.md` §8).
4. **Resumen de qué se va a hacer** en 4–5 viñetas (reordenar, corregir ambigüedades, unificar formato, separar lo que está mezclado).

No empezar hasta tener la confirmación. Si el usuario dice algo como "procede" o "dale", arrancar.

> **Modo directo** (`CONVENCIONES.md` §9): si el usuario pasó el argumento `directo`, anunciar fuentes y enfoque en un mensaje y proceder sin esperar confirmación.

---

## Fase 1: Generar v2 + preguntas

### Cómo reordenar el documento

Estructura objetivo (adaptar nombres al dominio del proyecto):

```
1. Visión general
   - 1.x Perfil del proyecto y decisiones base (idioma, unidades, etc., si las hay)
2. [Funcionalidad principal A]
3. [Funcionalidad principal B]
4. [Funcionalidad principal C]
5. [Funcionalidad principal …]
6. Stack técnico  (solo lo que el usuario ya definió)
7. IA / Prompt  (solo si la app usa LLM)
8. MVP
9. Deseables post-MVP   ← aparece después del merge si emergen
10. Fuera de scope      ← aparece después del merge
11. Pendientes por definir
```

Reglas:

- **Mover bloques mezclados a su sección correcta.** Ejemplo típico: una lista de variantes o plantillas enterrada en un párrafo de otra sección — se mueve a su propia subsección.
- **Usar citas en bloque (`>`)** para preservar énfasis del usuario (ej. "Acá reitero el punto..."). No reescribirlas como afirmaciones neutras.
- **Listas con viñetas planas**, no anidamiento excesivo.
- **Negritas** solo para términos clave que el usuario marcó.
- **Numerar secciones y subsecciones** (1, 1.1, 1.2, 2, 2.1...).
- Si una idea aparece dos veces, consolidar en un solo lugar y referenciar.

### Cómo detectar lo que va a `preguntas.md`

Marcar como pregunta:

- **Frases que dicen "por definir"** explícitamente.
- **Decisiones implícitas no explicitadas.** Ejemplo: el usuario dice "los datos pueden ser editados", pero no dice qué pasa con los registros ya derivados de esos datos cuando se editan. → Pregunta.
- **Términos ambiguos.** Ejemplo: "el usuario evalúa cómo le fue" sin definir qué se evalúa ni en qué escala. → Pregunta con opciones sugeridas.
- **Contradicciones internas** entre partes del documento.
- **Asunciones de scope** (idioma, unidades, multiusuario, notificaciones, integraciones). Confirmar aunque parezca obvio.

No marcar como pregunta:

- Decisiones técnicas que pertenecen al documento de diseño (stack frontend, base de datos específica si el usuario ya dio dirección general, etc.).
- Detalles de implementación.
- Cosas que el usuario ya respondió explícitamente.

### Formato de `preguntas.md`

El formato canónico del patrón preguntas→merge está en **`CONVENCIONES.md` §5** (bloques temáticos del proyecto, numeración global continua, contexto por pregunta, opciones (a)(b)(c), campo `R:`, bloque final "Fuera de scope (confirmar)").

Deltas de esta fase:
- **Máximo ~15 preguntas.** Si pasa de eso, el documento original está muy crudo y conviene una pasada de conversación previa.
- Los bloques temáticos salen de las funcionalidades principales del propio documento.

---

## Fase 2: Merge de respuestas

Cuando el usuario indique que respondió `preguntas.md`, aplicar el merge según `CONVENCIONES.md` §5. Específico de esta fase — clasificar cada respuesta en uno de estos destinos:

- **Integración directa** al cuerpo del v2 (cambio o adición a la sección correspondiente).
- **Sección "Deseables post-MVP"** — el usuario dijo "esto sería bonito a futuro" o "esto es deseable pero no MVP".
- **Sección "Fuera de scope"** — el usuario confirmó "no por ahora".
- **Sección "Pendientes por definir"** — el usuario dijo "lo definimos después" o "lo vemos en el diseño".

Además: limpiar §Pendientes (quitar lo respondido) y detectar respuestas que abren hilos nuevos (máximo 3–4 preguntas de seguimiento en el chat).

### Patrón observado: "Deseables post-MVP" suele emerger en esta fase

Varias respuestas del usuario tienden a ser "esto déjalo para después". Esto genera orgánicamente la sección §9 Deseables post-MVP. **No** forzarla antes — solo crearla cuando aparezca de las respuestas.

---

## Fase 3: Cierre

Al terminar el merge y las preguntas de seguimiento:

1. Actualizar `documentacion/INDICE.md`: fase 1 → ✅ (y registrar el perfil del proyecto si se capturó aquí).
2. **Archivar los intermedios automáticamente** (`CONVENCIONES.md` §5): mover a `01-requerimiento/_archivo/` el `preguntas.md` (ya integrado) y el documento de entrada que quedó reemplazado (si se trabajó con `-v2`); renombrar `<nombre>-v2.md` → `<nombre>.md` como artefacto vivo de la fase. Sin preguntar — es la convención.
3. Ofrecer pasar al documento de diseño (`/documento-diseno`). Avanzar de fase sí es decisión del usuario.

---

## Manejo de casos especiales

### El usuario se contradice entre el doc original y la conversación

Preguntar directamente: "El documento original dice X pero ahora indicas Y — ¿cuál mantenemos?". No asumir.

### El usuario pide agregar contenido nuevo durante el flujo

Permitido — siempre que **venga del usuario**. Si propone tú algo, primero pedir aprobación.

### El usuario pide recomendaciones técnicas

Recordarle (amable, una vez): "Eso lo dejaríamos para el documento de diseño. ¿Quieres que lo anote como pendiente o lo discutimos ahora y lo dejamos como decisión del requerimiento?". Si insiste, hacerlo.

### El documento original es muy corto o muy ambiguo

Si después de leer no hay material suficiente para producir un v2 útil, decirlo: "El doc original está muy crudo, ¿quieres que hagamos una pasada de conversación antes — te hago preguntas exploratorias — y después arrancamos con el skill?".

---

## Anti-patrones a evitar

- ❌ Agregar "Métricas de éxito", "KPIs", "Roadmap", "Análisis de riesgos" si el usuario no los mencionó.
- ❌ Insertar tablas comparativas de tecnologías por iniciativa propia.
- ❌ Usar lenguaje corporativo ("stakeholders", "alineación estratégica") si el usuario habla coloquial.
- ❌ Reescribir las palabras del usuario para sonar "más profesional".
- ❌ Hacer preguntas que el usuario ya respondió en otra parte del documento.
- ❌ Hacer preguntas con opciones cerradas cuando la pregunta es realmente abierta (o viceversa).
- ❌ Mover los pendientes al final y olvidar quitar de §Pendientes los que ya se respondieron.

---

## Output check (definition of done)

Al cerrar, el documento limpio debe poder leerse por una persona ajena al proyecto y entender en **5 minutos**:

- Qué se va a construir.
- Para quién.
- Qué hace y qué no hace (MVP vs deseable vs fuera de scope).
- Qué quedó pendiente para el diseño técnico.

Además:
- [ ] El perfil del proyecto está registrado (aquí o ya venía de `/iniciar-harness`).
- [ ] `INDICE.md` refleja la fase 1 cerrada.

Si no se cumple eso, el skill no terminó.
