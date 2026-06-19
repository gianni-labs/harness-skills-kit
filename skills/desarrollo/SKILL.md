---
name: desarrollo
description: 'Ejecuta el plan de implementación tarea por tarea, construyendo el código contra los contratos de la especificación técnica, y manteniendo los archivos de control (seguimiento, bitácora, herramientas recomendadas). Desarrollo secuencial; sub-agentes solo para review/verify/research. Respeta contratos y ADRs; no re-planifica ni los reabre. Trigger: el usuario tiene plan.md cerrado y pide desarrollar, implementar, construir, "empecemos a codear" o continuar una fase. Slash command sugerido: /desarrollo [fase|tarea].'
---

# desarrollo

Sexta fase de la metodología Spec-Design, segunda mitad de la **construcción** (convenciones compartidas: `documentacion/CONVENCIONES.md` del harness): el ejecutor.

```
… → 5 plan-implementacion → 6 desarrollo (este skill) ──► código
                                │
                                ├─ mantiene  seguimiento.md          (estado)
                                └─ alimenta  bitacora.md             (memoria: decisiones/issues/aprendizajes)
```

Construye el proyecto **contra los contratos ya cerrados**, de forma ordenada (una tarea a la vez), verificable y con memoria. No diseña ni re-decide: implementa lo que la cadena de fases ya definió.

---

## Qué consume (contrato de entrada)

- **`05-desarrollo/plan.md`** — fases y tareas a ejecutar (con dependencias y criterios de done).
- **`especificacion-tecnica.md`** — contratos exactos (tipos, JSON Schema, endpoints, módulos, contrato LLM, errores). **Fuente de verdad del código.**
- **Archivos de control:** `seguimiento.md`, `bitacora.md`.
- **`BACKLOG.md`** (raíz de `documentacion/`) — cola viva del producto. No se ejecuta desde aquí, pero es el destino de cualquier alcance que quede fuera del release en curso (ver "Alcance que aparece fuera del release").
- Si hace falta, consulta hacia atrás `diseno.md` / `diseno-tecnico.md` / `wireframes.md`, pero **no los modifica**.

---

## Principios

1. **Los contratos mandan.** El código respeta tipos/schemas/endpoints/estructura tal como están en la spec. Nada de improvisar estructuras nuevas.
2. **Una tarea a la vez.** Desarrollo **secuencial**, respetando dependencias del plan.
3. **Verificar antes de cerrar.** Ninguna tarea se marca `✅` sin pasar su criterio de done.
4. **Memoria viva.** Lo notable (decisiones, issues, aprendizajes) se registra en `bitacora.md` cuando ocurre.
5. **No re-planifica ni re-decide.** Si el plan o un contrato está mal, lo **señala** y devuelve a la fase correspondiente; no lo parchea en silencio.

---

## Sub-agentes (modo mínimo)

- Por defecto, **todo secuencial en el hilo principal**.
- Usar sub-agentes **solo** para:
  - **review** (`code-review`, `security-review`) en los gates de fase,
  - **verify/run** de la app,
  - **research puntual** (p. ej. consultar la documentación de una API externa o de una librería).
- **No** paralelizar tareas de build aunque parezcan independientes (evita colisiones de archivos en un MVP de un dev).

---

## Loop por tarea

Para cada tarea del plan (en orden de dependencias):

1. **Seleccionar** la siguiente tarea pendiente cuyas dependencias estén `✅`.
2. **Marcar `🟡`** esa tarea en `seguimiento.md` (y actualizar "última actualización").
3. **Implementar** estrictamente contra los contratos de la spec (archivos/tipos/endpoint indicados en la tarea). Ver *Cómo implementar* abajo.
4. **Verificar** el criterio de done con **evidencia**, no con "se ve bien": lint/build en verde, y `verify`/`run` cuando aplique. Si la tarea cambia comportamiento, el criterio incluye un test que lo prueba (ver `${CLAUDE_PLUGIN_ROOT}/skills/_harness/referencias/testing.md`).
5. **Marcar `✅`** en `seguimiento.md` + recalcular barra de progreso.
6. **Anotar en `bitacora.md`** si hubo decisión, issue o aprendizaje (si no, no ensuciar). Usar el formato con referencia cruzada: `### B-{nnn} · {fecha} · {TIPO}` + `**Origen:** {tarea en curso, ej. desarrollo F2-T5} · **Refs:** {ADR/RF/V} · **Por:** usuario|modelo|ambos`. El ID es correlativo respecto a la última entrada.

> Si una tarea se bloquea, marcar `⛔`, anotar el motivo en `bitacora.md` y avisar al usuario; no saltarla en silencio si otras dependen de ella.

### Cómo implementar (paso 3)

El **qué** ya está cerrado (contratos); esto es disciplina del **cómo**:

- **Rebanada vertical:** construir un camino completo y funcional a la vez (p. ej. tipo → endpoint → vista de un recurso), no toda la capa de datos y luego toda la API. Cada rebanada deja algo verificable de punta a punta.
- **Si la tarea cambia comportamiento, test primero:** escribir el test que falla, implementar hasta que pase, y recién entonces limpiar (rojo → verde → refactor). Detalle y niveles en `${CLAUDE_PLUGIN_ROOT}/skills/_harness/referencias/testing.md`.
- **Simplicidad:** preferir la solución obvia y aburrida. Si una abstracción no se gana su complejidad, no va. Tocar **solo** lo que la tarea pide (sin refactors oportunistas de código vecino: eso va al backlog).
- **Seguridad desde el código:** validar la entrada en los límites y tratar la salida del LLM como no confiable, según el contrato de la spec y `${CLAUDE_PLUGIN_ROOT}/skills/_harness/referencias/seguridad.md`.

### Cómo verificar (paso 4)

Una tarea no está hecha hasta que hay **evidencia**:

- Lint/build en verde + el test de la conducta pasa.
- Si hay UI, la vista responde a la acción esperada (`verify`/`run` o chequeo manual); revisar accesibilidad básica con `${CLAUDE_PLUGIN_ROOT}/skills/_harness/referencias/accesibilidad.md` cuando la tarea entrega una pantalla.
- "Lo probé manualmente" no persiste: lo que prueba la conducta es el test, no la inspección visual de una vez.

---

## Gate de cierre de fase

Al terminar todas las tareas de una fase:

> Las herramientas nombradas aquí (`code-review`, `security-review`, `verify`, `run`) son **opcionales del entorno**: si alguna no está disponible, el fallback es lint/build/tests manuales y revisión propia del diff. El gate no se salta por falta de herramienta.

1. **Review:** lanzar `code-review` (y `security-review` si la fase toca datos sensibles o superficie expuesta) — o revisar el diff manualmente si no están disponibles. La revisión cubre **cinco ejes**: correctitud (¿hace lo que la tarea dice?, ¿bordes y errores?), legibilidad, arquitectura (¿respeta patrones y contratos?), seguridad y performance. Para una revisión adversarial con contexto fresco, usar las **personas de revisión** `revisor-codigo` (y `auditor-seguridad` si aplica), disponibles como referencia en `${CLAUDE_PLUGIN_ROOT}/skills/_harness/agentes/`: si tu entorno las expone como sub-agentes invocables, úsalas así; si no, aplica esa misma grilla manualmente sobre el diff. La auto-revisión "se ve bien" no cuenta como gate.
2. **Verify:** correr la app / pruebas relevantes con `verify` o `run` — o ejecutarlas manualmente.
3. **Actualizar** `seguimiento.md` (fase → ✅, progreso global).
4. **Bitácora:** resumen breve de la fase (qué quedó, qué aprendiste).
5. **Herramientas (opcionales):** considerar si alguna herramienta del entorno (las del recuadro de arriba, u otras según el stack) ayuda en la **fase siguiente** y proponérsela al usuario, explicando para qué. El usuario decide; no se instala nada sin su OK.
6. **Respaldo git (si la política del proyecto lo activó):** `git commit` **local** de la documentación actualizada, con mensaje estándar (ej. `harness: cierre fase X`). **Local, nunca `push` ni remoto.** Si la política es "no tocar git", omitir este paso.

No avanzar a la siguiente fase sin cerrar el gate.

---

## Gate de cierre de release

Al terminar **todas las fases** del plan (el release queda construido):

1. Cerrar el gate de la última fase (review + verify).
2. **Actualizar `BACKLOG.md`:** subir el release a ✅ en la tabla de Releases. Si alguna funcionalidad del backlog se implementó en este release, marcarla ✅.
3. **Actualizar `INDICE.md`:** reflejar que el release está construido (no dejar "no iniciado" cuando ya está hecho).
4. Confirmar que el alcance diferido sigue visible en `BACKLOG.md` para el próximo ciclo (nada se "pierde" al cerrar el MVP).
5. *(Si el release va a producción)* Consultar `_harness/referencias/pre-produccion.md`: revisar que cada **"Pendiente para producción"** con `Bloquea = sí` esté resuelto o aceptado conscientemente. Recordatorio, no traba — el gate sigue siendo humano.
6. *(Opcional, si el proyecto tiene UI)* **Encuadre visual + `DESIGN.md`:** dejar claro que la UI del MVP es **base funcional, no identidad de marca** (decisión, no límite; ver `documentacion/CONVENCIONES.md` §7). Proponer generar `DESIGN.md` en la raíz (formato Google Stitch, leyendo el codebase) como punto de partida del **pulido visual en un track B** post-MVP. Si el usuario declina, el track B de `/mejora` lo generará cuando haga falta.

---

## Alcance que aparece fuera del release

Durante el build pueden surgir ideas, mejoras o requerimientos que **no están en el plan actual**. No se implementan al vuelo ni se descartan en silencio:

- Si es una **funcionalidad/requerimiento nuevo** → anotarlo en `BACKLOG.md` (y, si amerita, abrir el RF en `diseno.md` en la próxima pasada de diseño). No meterlo en el release en curso.
- Si es **deuda técnica** detectada al construir → registrarla en `BACKLOG.md` con su disparador. Si es un atajo que hay que deshacer antes de prod (config por env var, datos de prueba, mock de un servicio externo…) va a **"Pendientes para producción"** con `Bloquea release a prod = sí/no`; si es deuda interna que no condiciona el paso a prod, a **"Deuda técnica y mejoras diferidas"**.
- Si cambia un contrato/ADR → aplica la "Disciplina de contratos" (detenerse y avisar).

Así el backlog queda como la **memoria de alcance** del producto, no solo lo que el diseño anticipó.

---

## Disciplina de contratos y cambios

- Si durante el build surge una **desviación necesaria** respecto a la spec:
  - **Anotar en `bitacora.md`** (tipo `DECISIÓN` o `ISSUE`).
  - Si la desviación **cambia un contrato o un ADR**, **detenerse y avisar**: hay que volver a `especificacion-tecnica.md` (o al diseño) a actualizar la fuente de verdad antes de seguir. No editar el comportamiento divergiendo del documento.
- Si la desviación es solo de implementación interna (no cambia contrato), seguir y dejar nota si es relevante.

---

## Cuando algo se rompe (triage)

Si un test falla, el build se cae o el comportamiento es inesperado, **parar la línea**: no acumular más cambios encima de algo roto. Triage en cinco pasos:

1. **Reproducir.** Conseguir un caso mínimo y determinista que falle. Sin reproducción confiable no hay arreglo confiable.
2. **Localizar.** Acotar dónde ocurre (bisección, logs, leer el stack trace completo). No adivinar.
3. **Reducir.** Quitar lo accesorio hasta el mínimo que reproduce el fallo.
4. **Arreglar.** La causa raíz, no el síntoma. Si el arreglo desvía de un contrato, aplica la "Disciplina de contratos" (volver a la fase dueña).
5. **Blindar.** Agregar el test que falla antes del fix y pasa después (la regla del bug, `${CLAUDE_PLUGIN_ROOT}/skills/_harness/referencias/testing.md`), para que la regresión no vuelva en silencio.

Anotar en `bitacora.md` lo notable (tipo `ISSUE`/`DECISIÓN`). No marcar la tarea `✅` mientras el fallo siga vivo.

---

## Dogfooding del harness (si aplica)

Si el proyecto incluye un **log de mejoras del harness** (por convención, un `MEJORAS-HARNESS.md` en una carpeta meta como `harness/`), registra ahí las observaciones sobre **el harness mismo** (no sobre el proyecto): fricciones del flujo, mejoras a skills, curiosidades, bugs. Es una memoria **de distinta altitud** que la bitácora del proyecto:

- **`bitacora.md` (del proyecto)** → decisiones/issues/aprendizajes de la app que se construye.
- **`MEJORAS-HARNESS.md` (meta)** → cómo mejorar el harness/skills, para aplicarlo al empaquetarlo.

Formato de entrada: `### M-{nnn} · {fecha} · {TIPO: MEJORA|FRICCIÓN|CURIOSIDAD|BUG} · {skill/fase afectada}` + `**Detectado en:** {tarea/fase} · **Estado:** abierta|aplicada|descartada`.

**Si el archivo no existe, omitir** (el skill es genérico; solo registra cuando el proyecto activa el dogfooding con ese archivo). No mezclar nunca estas entradas con la bitácora del proyecto.

---

## Modo mejora (scoped)

Cuando se ejecuta una **mejora post-MVP** (track orquestado por el skill `mejora`), el loop es el mismo, con estas diferencias:

- **El plan a ejecutar es** `documentacion/mejoras/<id>-<slug>/plan.md` (no el `05-desarrollo/plan.md` del MVP).
- **Los contratos de referencia** son los del MVP (`04-especificacion-tecnica/`) **más** el delta de la mejora (`mejoras/<id>-<slug>/especificacion-tecnica.md` si existe). El código de la mejora **extiende** lo existente; no rompe contratos del MVP sin volver a la fase dueña.
- **Memoria unificada:** las entradas de `bitacora.md` (global) llevan `Origen = <id de la mejora>`.
- **Cierre:** aplica el "Gate de cierre de release" → marcar la fila del ítem en `BACKLOG.md` como ✅ y actualizar `INDICE.md`.

---

## Manejo de casos especiales

**El plan tiene un hueco o una tarea mal especificada.**
Señalarlo y volver a `plan-implementacion`; no improvisar la tarea faltante.

**Falta una decisión que el diseño dejó abierta (un parámetro de negocio aún por definir).**
Usar lo acordado como provisional (está en el diseño), anotar en bitácora que es provisional, y continuar. No bloquear el MVP por insumos pendientes del usuario.

**El usuario pide saltar la verificación para ir más rápido.**
Resistir suavemente: el gate de verificación es lo que mantiene el desarrollo "seguro". Si insiste, registrar en bitácora que la fase se cerró sin verificar.

---

## Anti-patrones a evitar

- ❌ Implementar estructuras/tipos/endpoints distintos a los de la spec sin volver a actualizarla.
- ❌ Reabrir o re-decidir ADRs durante el código.
- ❌ Marcar tareas `✅` sin cumplir su criterio de done.
- ❌ Paralelizar build con sub-agentes (modo mínimo).
- ❌ Avanzar de fase sin pasar el gate (review/verify).
- ❌ Olvidar actualizar `seguimiento.md` o alimentar `bitacora.md` cuando corresponde.
- ❌ Instalar herramientas sin aprobación del usuario (el menú es opt-in).
- ❌ Dejar que se "pierda" alcance fuera del release (ideas/RFs/deuda nuevos): va a `BACKLOG.md`, no al olvido ni al release en curso.

---

## Racionalizaciones (excusa → realidad)

Esta fase es donde el modelo más tiende a tomar atajos, porque está "produciendo". Las excusas frecuentes:

| Excusa | Realidad |
|--------|----------|
| "Marco la tarea ✅ porque escribí el código." | ✅ exige pasar el **criterio de done**, no escribir el archivo. Sin verificación, no está hecha. |
| "El contrato está mal, lo parcho acá y sigo." | No se re-decide en silencio. Se señala y se vuelve a la fase dueña (spec/diseño). |
| "Esta estructura nueva es mejor que la de la spec." | La spec manda. Improvisar estructuras rompe el contrato que el resto del código asume. |
| "Lo probé manualmente, funciona." | El testeo manual no persiste. El cambio de mañana lo rompe sin que nadie se entere. |
| "Aprovecho y refactorizo este módulo vecino." | Fuera del scope de la tarea. Se toca **solo** lo que la tarea pide; lo demás va al backlog. |
| "Reviso mi propio diff, se ve bien." | La auto-revisión es donde más se relaja el criterio. Usar la persona revisora / el gate, no el "se ve bien". |

## Output check (por tarea y por fase)

**Por tarea:**
- [ ] Implementada contra el contrato de la spec.
- [ ] Criterio de done verificado.
- [ ] `seguimiento.md` actualizado (🟡→✅, progreso).
- [ ] `bitacora.md` actualizado si hubo algo notable.

**Por fase (gate):**
- [ ] Todas las tareas de la fase en `✅`.
- [ ] Review + verify ejecutados.
- [ ] `seguimiento.md` refleja la fase cerrada.
- [ ] Tools de la próxima fase propuestas al usuario.

**Por release (todas las fases hechas):**
- [ ] `BACKLOG.md` actualizado (release → ✅; alcance diferido sigue visible).
- [ ] `INDICE.md` refleja el release construido.
- [ ] *(Si va a prod)* "Pendientes para producción" con `Bloquea = sí` revisados vía `pre-produccion.md` (resueltos o aceptados).

Si algún check falla, la tarea/fase no se da por cerrada.
