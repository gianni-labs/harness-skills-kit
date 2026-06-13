---
name: especificacion-tecnica
description: 'Toma un diseño cerrado (funcional + técnico) y los wireframes (si hay UI), y produce una especificación técnica detallada lista para codificar: tipos/modelos, contratos de API, estructura de carpetas/módulos, modelo de error, y contratos condicionales según el perfil del proyecto (LLM, integraciones externas). Respeta los ADRs ya decididos; no los re-discute. NO escribe lógica de negocio ni secuencia tareas. Trigger: el usuario tiene diseño (+ wireframes) cerrados y pide especificación técnica, contratos de API, schemas, tipos, estructura del proyecto o "preparar para implementar". Slash command sugerido: /especificacion-tecnica <archivo-diseño>.'
---

# especificacion-tecnica

Cuarta fase de la metodología Spec-Design, capa de **contratos exactos** (convenciones compartidas: `CONVENCIONES.md` del harness):

```
[requerimiento]  →  2 documento-diseno  →  [diseño funcional + técnico]
                                              ↓
                                         3 wireframes (si hay UI)
                                              ↓
                            4 especificacion-tecnica (este skill)  →  [contratos: tipos, schemas, APIs, módulos]
                                              ↓
                                    5 plan-implementacion  →  6 desarrollo → código
```

Cierra el hueco entre "decisiones de arquitectura" (que ya están en `diseno-tecnico.md`) y "tareas de implementación" (del plan). Su salida es el **contrato preciso contra el que se programa**: si está bien hecha, el plan solo secuencia tareas y el código no improvisa estructuras.

---

## Altitud: qué resuelve y qué NO

| Capa | Pregunta | Dónde |
|------|----------|-------|
| Diseño técnico | ¿Por qué estas piezas/decisiones? (ADRs) | `diseno-tecnico.md` (entrada) |
| **Especificación técnica** | **¿Cuál es el contrato exacto? (tipos, schemas, APIs, módulos)** | **este skill (salida)** |
| Plan de implementación | ¿En qué orden lo construyo? | fase siguiente |

---

## Qué consume (contrato de entrada)

- **`diseno-tecnico.md`** (o §técnico de `diseno.md`) — modelo de datos (ER), arquitectura, contratos conceptuales (puertos/interfaces entre componentes), y **ADRs `Aceptada`** (stack, persistencia, proveedores externos, estrategia de validación). Los ADRs son **fuente de verdad**: no se re-deciden aquí.
- **`diseno.md`** — RFs (qué debe persistir/operar), RNFs (p. ej. tiempos), §Alcance MVP, **perfil del proyecto** (UI/LLM/API — activa las secciones condicionales de esta spec).
- **`wireframes.md`** (si el proyecto tiene UI) — cada vista declara qué **datos muestra** y qué **acciones** dispara → eso determina los **endpoints** y sus request/response. Si no hay UI, esa derivación sale de los RFs y de la superficie definida en el diseño (comandos, API pública, interfaz de librería).

> Si algo necesario no está decidido en el diseño, **no inventar stack ni reabrir ADRs**: anotar en `preguntas-especificacion.md`.

---

## Qué hace este skill

1. Lee diseño + wireframes.
2. Produce en `documentacion/04-especificacion-tecnica/`:
   - `especificacion-tecnica.md` — los contratos detallados.
   - `preguntas-especificacion.md` — dudas que bloquean precisión (si las hay).
3. Espera respuestas, integra, cierra.
4. Actualiza `INDICE.md`.

## Qué este skill NO hace

- **No re-decide stack ni reabre ADRs.** Si un ADR está `Aceptada`, se usa tal cual.
- **No escribe lógica de negocio** ni implementaciones de funciones — solo **firmas, tipos y contratos**.
- **No crea archivos de código fuente** (`.ts`, etc.). Los tipos/schemas van en bloques dentro del `.md` como contrato; el código real es la fase posterior.
- **No secuencia tareas ni estima esfuerzo.** Eso es del plan.
- **No fija versiones de dependencias.** Puede **nombrar librerías candidatas** (p. ej. "un validador de JSON Schema") pero el pin de versión es del plan.
- **No diseña UI** (eso es wireframes) ni agrega RFs/vistas nuevas.

---

## Fase 0: Aclaraciones iniciales

Confirmar **en un solo mensaje breve**:

1. **Archivos fuente:** rutas de `diseno.md`, `diseno-tecnico.md`, `wireframes.md` (si aplica).
2. **Estilo de API/interfaz:** **derivar del ADR de framework/arquitectura**. Si el ADR no lo determina, proponer máximo 2 opciones coherentes con el stack y preguntar.
3. **Lenguaje de los tipos:** el del stack decidido en el ADR. Si el ADR de stack está abierto, es bloqueante — volver al diseño, no asumir.
4. **Resumen** en 3–4 viñetas de qué se va a producir.

No empezar sin confirmación.

> **Modo directo** (`CONVENCIONES.md` §9): si la skill fue invocada por el orquestador `mejora` o el usuario pasó `directo`, anunciar fuentes y defaults en un mensaje y proceder sin esperar confirmación.

---

## Fase 1: Generar la especificación

### Estructura objetivo de `especificacion-tecnica.md`

Secciones **núcleo** (siempre) + secciones **condicionales** según el perfil del proyecto:

```
1. Encabezado + alcance + nota "respeta ADRs, no los reabre"
2. Estructura de carpetas / módulos                          [núcleo]
3. Modelo de datos tipado (tipos + enums)                    [núcleo] ← alineado con el ER del diseño
4. Contratos de API / interfaz pública                       [núcleo] ← request / response / errores
5. Modelo de error                                           [núcleo]
6. Contrato del LLM                                          [solo si el perfil incluye LLM]
   (payload, respuesta, JSON Schema de validación, reintento, nº de llamadas)
7. Contratos de otras integraciones externas                 [solo si las hay]
8. Configuración externa (config de servicios, prompts/vN)   [solo si aplica]
9. Trazabilidad (RF / Vista → API / tipo)                    [núcleo]
10. Pendientes                                               [núcleo]
```

> Las secciones condicionales que no aplican **se omiten** (no se dejan vacías ni se inventa contenido para llenarlas).

### Reglas por sección

**§2 Estructura de carpetas/módulos:**
- Árbol de directorios coherente con el framework del ADR.
- Una línea por carpeta/módulo clave explicando su responsabilidad.
- Mapear los componentes del diseño técnico (dominio, persistencia, integraciones, etc.) a carpetas concretas.

**§3 Modelo de datos tipado:**
- Una `interface`/`type` por entidad del ER del diseño. Mismos nombres de campo.
- **Enums explícitos** para todo conjunto cerrado (estados, categorías, roles, etc.).
- Marcar campos opcionales (`?`) y nullables con criterio.
- No agregar entidades que no estén en el modelo de datos del diseño.

**§4 Contratos de API / interfaz pública:**
- Un bloque por endpoint (o comando/función pública, según el perfil): **método + path** (o firma), propósito, **request** (params/body tipados), **response** (tipado), **errores posibles** (códigos del §5), **RF/Vista que lo origina**.
- Derivar los endpoints de las acciones de las vistas (wireframes) y de los RFs.
- Cobertura: todo dato que una vista muestra o modifica debe tener su endpoint.

**§5 Modelo de error:**
- Shape único de error de API/interfaz.
- Tabla de **códigos de error** con significado y cuándo se produce.

**§6 Contrato del LLM** *(solo si el perfil incluye LLM)*:
- Respetar el nº de llamadas del ADR correspondiente.
- Para cada llamada: **input** (shape del payload), **output esperado**, **JSON Schema de validación** del output antes de persistir (alineado 1:1 con los tipos de §3 — si divergen, es un bug del spec; indicar versión del schema y dónde vive, ej. carpeta `schemas/`), **manejo de error/reintento/timeout** (atado al RNF de tiempo).
- Dejar claro qué llamada produce qué parte del artefacto generado.

**§7 Contratos de otras integraciones externas** *(solo si las hay)*:
- Por integración (pagos, email, almacenamiento, etc.): datos que cruzan el límite, autenticación, errores esperables y su mapeo al §5.

**§8 Configuración externa** *(solo si aplica)*:
- Forma de la configuración de servicios externos (modelo, temperatura, claves, etc.) — externa al código (RNF).
- Si hay prompts de LLM: layout de `prompts/vN/` y cómo se registra la versión del prompt en el artefacto generado.

**§9 Trazabilidad:**
- Tabla RF / Vista → endpoint(s) / tipo(s) que lo cubren. Detecta huecos (un RF sin endpoint = falta algo).

### Reglas para `preguntas-especificacion.md`

Formato canónico en **`CONVENCIONES.md` §5**. Deltas de esta fase:

- Solo lo que **bloquea precisión** del contrato (no preferencias cosméticas).
- Máximo ~10 preguntas.
- Casos típicos: operación síncrona vs asíncrona/polling (afecta API y vista de carga), identificador de una entidad (índice vs fecha vs UUID), enum exacto de un campo ambiguo, ubicación de datos semilla (archivo aparte vs dentro del store).

---

## Fase 2: Merge de respuestas

1. Integrar respuestas en la sección correspondiente (tipos/API/schema).
2. Si una respuesta cambia un contrato, propagar el cambio a §3/§4/§5 para mantener coherencia 1:1.
3. Limpiar §Pendientes.
4. Reformular, no copiar literal.

---

## Fase 3: Cierre + handoff

Resumen en el chat:

```
✅ Especificación técnica cerrada.

- Tipos/entidades: N
- Endpoints: M
- JSON Schema: vX
- Estructura de módulos definida

Listo para el plan de implementación:
- El plan secuencia tareas contra estos contratos (sin redefinirlos).
```

**Archivar** `preguntas-especificacion.md` (si se generó, ya integrado) en `04-especificacion-tecnica/_archivo/` — automático, según `CONVENCIONES.md` §5. Actualizar `INDICE.md` y ofrecer continuar con el plan de implementación.

---

## Modo mejora (scoped)

Cuando este skill se invoca dentro del **track de mejoras post-MVP** (orquestado por el skill `mejora`), produce una **especificación delta**, no la spec completa:

- **Escribe en** `documentacion/mejoras/<id>-<slug>/especificacion-tecnica.md`, **no** en `04-especificacion-tecnica/`.
- **Alcance = solo los contratos nuevos o que cambian:** tipos/campos nuevos, endpoints nuevos, deltas de JSON Schema, módulos nuevos. La spec del MVP es **baseline de solo lectura**: se **referencia** (p. ej. "extiende el tipo `Routine` del MVP con el campo X"), no se reescribe.
- **Extender, no romper:** si la mejora agregaría un campo/endpoint, documentarlo como adición. Si **rompería** un contrato existente del MVP, detenerse y avisar — es un cambio de contrato que exige decisión explícita (y probablemente un ADR en el diseño de la mejora).
- Mantener la coherencia 1:1 entre tipos/schema/endpoints **dentro del delta**.

---

## Manejo de casos especiales

**Un ADR necesario está abierto (`Propuesta pendiente`).**
Si es crítico para un contrato del MVP, marcar la pregunta con ⚠️ y no inventar la decisión. Si es post-MVP (p. ej. DB futura), seguir con lo del MVP.

**El diseño y los wireframes se contradicen sobre un dato.**
Preguntar cuál manda; no asumir. El diseño es fuente de verdad sobre *qué existe*; los wireframes sobre *cómo se navega/usa*.

**Tentación de empezar a implementar.**
Resistir: aquí solo se fijan contratos. La implementación es la fase siguiente. Nada de lógica dentro de las funciones, solo firmas.

---

## Anti-patrones a evitar

- ❌ Reabrir o "mejorar" un ADR ya aceptado.
- ❌ Escribir cuerpos de funciones / lógica de negocio.
- ❌ Crear archivos `.ts`/código fuente (va en bloques en el `.md`).
- ❌ Tipos que no cuadran con el modelo de datos del diseño.
- ❌ JSON Schema desalineado de los tipos.
- ❌ Endpoints sin RF/vista que los origine (over-spec) o RFs sin endpoint (hueco).
- ❌ Fijar versiones de paquetes (es del plan).
- ❌ Secuenciar tareas o estimar (es del plan).

---

## Output check (definition of done)

- [ ] Toda entidad del ER del diseño tiene su tipo, con enums explícitos.
- [ ] Todo dato que una vista muestra/modifica tiene endpoint (o superficie equivalente).
- [ ] Todo RF persistente/operativo del MVP está en la tabla de trazabilidad con su endpoint/tipo.
- [ ] *(Si el perfil incluye LLM)* El contrato del LLM respeta el nº de llamadas del ADR, su JSON Schema está alineado 1:1 con los tipos y define validación + manejo de error.
- [ ] Hay estructura de carpetas/módulos coherente con el ADR de framework.
- [ ] Las secciones condicionales que no aplican fueron omitidas (no rellenadas).
- [ ] No se reabrió ningún ADR; no hay lógica de negocio ni versiones de paquetes.
- [ ] No quedan `(?)` ni dudas que bloqueen la implementación del MVP.
- [ ] `INDICE.md` refleja la fase 4 cerrada.

Si algún check falla, el skill no termina — vuelve a la fase de preguntas.
