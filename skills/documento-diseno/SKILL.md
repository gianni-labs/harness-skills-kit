---
name: documento-diseno
description: 'Toma un requerimiento cerrado y produce un documento de diseño funcional + técnico estructurado, listo para alimentar las fases siguientes (wireframes + especificación técnica). Usa metodología User Stories + EARS para RFs, MoSCoW para priorización, FURPS+ para RNFs, y ADR mini para decisiones técnicas. Genera preguntas para refinar el documento. Trigger: el usuario apunta a un requerimiento cerrado (ej. requerimiento-v2.md) y pide diseñar, documento de diseño, diseño funcional/técnico o preparar para wireframes. Slash command sugerido: /documento-diseno <archivo>.'
---

# documento-diseno

Segunda fase de la metodología Spec-Design (convenciones compartidas: `documentacion/CONVENCIONES.md` del harness):

```
[requerimiento cerrado]  →  2 documento-diseno (este skill)
                                  ↓
                            [diseño]  →  3 wireframes* (consume §Pantallas + §Flujos)
                                      →  4 especificacion-tecnica (consume diseño + wireframes)
                                      →  5 plan-implementacion (consume §Alcance MVP + dependencias)
                                                  ↓
                                          6 desarrollo → código
```
\* `wireframes` se omite si el proyecto no tiene UI (perfil del proyecto).

El objetivo es producir un diseño **estructurado, trazable y handoff-ready**: cada pieza (RFs, pantallas, alcance MVP, stack) tiene un formato fijo que los skills siguientes pueden consumir sin ambigüedad.

---

## Metodología

Combinación pragmática de cuatro piezas. Esta es la convención del skill:

| Para | Método | ID |
|------|--------|-----|
| Requerimientos funcionales | **User Story** + criterios estilo **EARS** | `RF-N` |
| Priorización / alcance | **MoSCoW** (Must / Should / Could / Won't) | — |
| Requerimientos no funcionales | **FURPS+** (Functionality, Usability, Reliability, Performance, Supportability, +) | `RNF-N` |
| Decisiones técnicas | **ADR mini** (Contexto / Decisión / Alternativas / Consecuencias) | `ADR-N` |
| Pantallas / vistas (si hay UI) | Sección estructurada propia | `V-N` |
| Flujos | Sección estructurada propia (+ mermaid si aporta) | `F-N` |

### Formato RF (User Story + EARS)

```markdown
### RF-N: [Título corto]
**Prioridad:** Must / Should / Could / Won't
**User story:** Como [rol], quiero [acción], para [beneficio].

**Criterios de aceptación (EARS):**
- Cuando [evento/condición], el sistema deberá [respuesta].
- Cuando [evento/condición], el sistema deberá [respuesta].

**Depende de:** RF-X, RF-Y (si aplica)
```

### Formato RNF (FURPS+)

```markdown
### RNF-N: [Título corto]
**Categoría:** Functionality / Usability / Reliability / Performance / Supportability / Design / Implementation / Interface
**Descripción:** 1–2 líneas.
**Criterio medible:** (si aplica, ej. "respuesta del servicio externo < 30s en p95") o "n/a".
```

### Formato ADR mini

```markdown
### ADR-N: [Título corto de la decisión]
- **Contexto:** qué problema se resuelve.
- **Decisión:** qué se eligió.
- **Alternativas consideradas:** A, B (con razones cortas de descarte).
- **Consecuencias:** trade-offs aceptados.
- **Estado:** Aceptada / Propuesta (pendiente confirmación) / Reemplazada por ADR-X.
```

### Formato V (Vista / Pantalla)

```markdown
### V-N: [Nombre de la pantalla]
- **Propósito:** 1 línea.
- **Datos que muestra:** lista.
- **Acciones del usuario:** lista.
- **Cubre RFs:** RF-X, RF-Y.
- **Pertenece a flujos:** F-A, F-B.
- **Estado inicial / vacío:** descripción.
```

> Esta sección es **el contrato con el skill de wireframes**. Mantener este formato es obligatorio.

### Formato F (Flujo)

```markdown
### F-N: [Nombre del flujo]
- **Actor:** quién lo dispara.
- **Disparador:** qué evento lo inicia.
- **Pasos:** lista numerada.
- **Vistas involucradas:** V-X, V-Y.
- **Diagrama:** (mermaid si el flujo lo amerita, sino omitir)
```

---

## Qué hace este skill

1. Lee el requerimiento cerrado (último `<archivo>-vN.md` o el indicado por el usuario).
2. Produce **dos archivos** (por defecto) en `documentacion/02-diseno/`:
   - `diseno.md` — diseño funcional + técnico.
   - `preguntas-diseno.md` — preguntas para refinar.
3. Espera respuestas del usuario en `preguntas-diseno.md`.
4. Integra las respuestas en `diseno.md`.
5. Hace preguntas de seguimiento cortas si las respuestas abren nuevos hilos.
6. Cierra el documento, actualiza `documentacion/INDICE.md` (fase 2 → ✅) e indica el handoff a las siguientes fases.

## Qué este skill NO hace

- **No agrega RFs que no estén en el requerimiento.** El scope se cerró antes. Si emerge algo nuevo durante el diseño, va a `preguntas-diseno.md`.
- **No escribe tasks de implementación** ni cronograma ni estimaciones de esfuerzo. Eso es de fases posteriores.
- **No diseña UI/UX detallado** (colores, tipografías, layouts pixel-perfect). Las vistas se describen funcionalmente; el wireframe traduce.
- **No decide stack** que el usuario no haya validado. Cuando hay alternativas abiertas, propone máximo 2 y marca como `Propuesta pendiente`.
- **No inventa RNFs** sin evidencia en el requerimiento. Los inferidos llevan `(?)` y van a `preguntas-diseno.md`.
- **No lista dependencias específicas** (paquetes npm/pip, versiones). Eso es del plan de implementación.
- **No diseña pruebas, CI/CD, deploy detallado.** Decisiones de alto nivel sí (ej. "deploy en un PaaS"); detalles operativos no.

---

## Fase 0: Aclaraciones iniciales

Antes de tocar archivos, confirmar **en un solo mensaje breve**:

1. **Archivo de requerimiento fuente:** ruta exacta del documento cerrado.
2. **Técnico integrado o separado:**
   - Por defecto: **integrado** en `diseno.md` como §Stack + §Decisiones técnicas.
   - Separable a `diseno-tecnico.md` si el usuario lo pide o si la parte técnica crece mucho (> ~30% del documento).
3. **Profundidad de arquitectura:** alto nivel (componentes y responsabilidades) o detallado (con interfaces/contratos entre componentes). Por defecto: **alto nivel**.
4. **Resumen de qué se va a hacer** en 4–5 viñetas.

No empezar hasta tener la confirmación.

> **Modo directo** (`documentacion/CONVENCIONES.md` §9): si la skill fue invocada por el orquestador `mejora` o el usuario pasó `directo`, anunciar fuentes y defaults en un mensaje y proceder sin esperar confirmación.

---

## Fase 1: Generar diseño + preguntas

### Estructura objetivo de `diseno.md`

```
1. Resumen ejecutivo
2. Requerimientos funcionales (RF)        ← User Stories + EARS, MoSCoW
3. Requerimientos no funcionales (RNF)    ← FURPS+
4. Arquitectura general                   ← mermaid si aporta
5. Modelo de datos                        ← mermaid ER
6. Flujos clave (F-N)
7. Pantallas / Vistas (V-N)               ← solo si el proyecto tiene UI; formato fijo, handoff a wireframes
8. Stack tecnológico
9. Decisiones técnicas (ADR mini)
10. Integraciones externas
11. Alcance del MVP                       ← formato fijo, handoff a plan de implementación
12. Riesgos técnicos
13. Pendientes por definir
```

> Si el perfil del proyecto no tiene UI, §7 se reemplaza por la superficie equivalente (comandos del CLI, endpoints públicos de la API, interfaz de la librería), manteniendo la trazabilidad RF ↔ superficie.

### Reglas por sección

**§1 Resumen ejecutivo (3–5 párrafos):**
- Qué se construye, para quién, qué problema resuelve.
- Una línea con el alcance del MVP.
- Una línea con el stack principal.

**§2 RFs:**
- Numeración global `RF-1, RF-2, ...`.
- Cada RF lleva `Must / Should / Could / Won't`.
- "Must" → entra al MVP.
- "Should" / "Could" → deseables post-MVP (ya identificados en el requerimiento).
- "Won't" → no en esta versión (fuera de scope).
- Si el requerimiento no especifica prioridad para algo, marcar como pregunta.
- **Depende de:** declarar dependencias funcionales explícitas.

**§3 RNFs:**
- Categorías FURPS+: Functionality, Usability, Reliability, Performance, Supportability, + (Design, Implementation, Interface).
- RNFs inferidos (no presentes literalmente en el requerimiento) → marcar con `(?)` y agregar pregunta.
- Ejemplos típicos a evaluar (sin inventar): responsive mobile, accesibilidad básica, tiempos de respuesta tolerables (especialmente si hay servicios externos o LLM), persistencia confiable, idioma del UI.

**§4 Arquitectura general:**
- Diagrama de componentes (mermaid `graph` o `C4`) si la app tiene > 3 piezas.
- Para cada componente: nombre, responsabilidad, tecnología, comunicación con otros.

**§5 Modelo de datos:**
- Diagrama ER en mermaid.
- Entidades, atributos clave, relaciones, cardinalidad.
- Solo modelar lo que los RFs del MVP requieren persistir. Lo post-MVP queda anotado pero no diagramado.

**§6 Flujos clave (F-N):**
- Un flujo por proceso end-to-end importante (ej. F-1 el proceso central de creación del artefacto principal de la app, F-2 su consumo/seguimiento).
- Mermaid `sequenceDiagram` si hay multi-actor (usuario ↔ frontend ↔ servicio externo ↔ DB).
- Mermaid `flowchart` si hay ramas/condiciones.
- Si el flujo se entiende leyéndolo (3-4 pasos lineales), omitir diagrama.

**§7 Pantallas / Vistas (V-N):**
- Formato fijo (ver arriba).
- Cubrir **todos los RFs interactivos del MVP**. Si un RF no tiene vista asociada, es señal de hueco.
- Incluir vistas de estado (empty, loading, error) cuando sean relevantes.

**§8 Stack tecnológico:**
- Tabla por capa: Frontend / Backend / DB / Hosting / proveedores externos / etc.
- Lo que el usuario ya definió → respetar y justificar.
- Lo abierto → **máximo 2 alternativas**, tabla pros/contras breve, recomendación marcada como `Propuesta pendiente`.

**§9 Decisiones técnicas (ADR mini):**
- Formato ADR (ver arriba).
- Una ADR por decisión no trivial: persistencia del MVP, framework, proveedor de un servicio externo (LLM, pagos, email…), estrategia de validación, etc.
- ADR resueltas → `Aceptada`. ADR abiertas → `Propuesta pendiente` + pregunta.

**§10 Integraciones externas:**
- Lista corta: proveedor, propósito, datos que cruzan el límite, autenticación.

**§11 Alcance del MVP:**
Formato fijo (es el **contrato con el skill de plan de implementación**):

```markdown
**Dentro del MVP (Must):**
- RF-1, RF-2, RF-N (priorizados Must)
- Vistas: V-1, V-2, V-N
- Integraciones: [las que el MVP requiera]
- Decisiones técnicas activas: ADR-1, ADR-3

**Fuera del MVP:**
- Should: RF-A, RF-B (deseables post-MVP)
- Could: RF-C
- Won't (esta versión): RF-D

**Dependencias funcionales (orden sugerido de implementación):**
- RF-1 (sin dependencias)
- RF-2 depende de RF-1
- RF-3 depende de RF-1, RF-2
- ...
```

**§12 Riesgos técnicos:**
- Lista corta de riesgos identificados durante el diseño (ej. "la calidad del output depende fuertemente de un servicio externo", "la persistencia elegida no soporta concurrencia").
- Cada riesgo: descripción + mitigación sugerida.

**§13 Pendientes por definir:**
- Lo que no se cerró ni en el requerimiento ni en este diseño.
- Se traen los pendientes del requerimiento y se actualizan (algunos se resolvieron al diseñar).

### Reglas para diagramas Mermaid

- **Solo cuando aportan claridad.** Texto bien escrito > diagrama por compromiso.
- **Tipo por uso:**
  - `sequenceDiagram` → interacciones multi-actor.
  - `flowchart` → procesos con ramas.
  - `erDiagram` → modelo de datos.
  - `graph` o `C4` → arquitectura de componentes.
- **Cada diagrama lleva 1–2 líneas de texto** antes (qué muestra) y opcional después (qué destacar).
- **Validar sintaxis** mentalmente antes de escribir — un mermaid roto rompe el render del documento entero.

### Reglas para `preguntas-diseno.md`

Formato canónico en **`documentacion/CONVENCIONES.md` §5**. Deltas de esta fase:

- Máximo ~15 preguntas.
- Bloques sugeridos (adaptar al proyecto):
  - Sobre requerimientos no funcionales (los marcados con `(?)`).
  - Sobre arquitectura (decisiones abiertas).
  - Sobre modelo de datos (ambigüedades estructurales).
  - Sobre stack técnico (cuando hay `Propuesta pendiente`).
  - Sobre alcance MVP (cuando un RF no tiene prioridad clara).
  - Sobre integraciones (autenticación, manejo de errores externos).

---

## Fase 2: Merge de respuestas

Aplicar el merge según `documentacion/CONVENCIONES.md` §5. Específico de esta fase — clasificar cada respuesta:

- **Confirmación de propuesta** → cambiar estado del ADR de `Propuesta pendiente` a `Aceptada`. Quitar el `(?)` de RNFs.
- **Rechazo de propuesta** → mover la decisión a `Alternativas consideradas` y actualizar la decisión con la elegida.
- **Nueva información** → integrar en la sección correspondiente.
- **"Lo vemos después"** → llevar a §13 Pendientes.

Además: limpiar §13 (quitar lo resuelto). Si una respuesta abre 1–3 hilos nuevos, preguntas de seguimiento en el chat; si abre >3, agregar un nuevo bloque a `preguntas-diseno.md` y avisar al usuario.

---

## Fase 3: Cierre + handoff

Al terminar: actualizar `documentacion/INDICE.md` (fase 2 → ✅) y generar un **resumen de handoff** en el chat (no en archivos):

```
✅ Diseño cerrado.

Listo para wireframes (si el proyecto tiene UI):
- Pantallas detectadas: V-1, V-2, ..., V-N
- Flujos detectados: F-1, ..., F-M

Listo para especificación técnica y plan:
- MVP: RF-1, RF-2, ..., RF-K (Must)
- Vistas MVP: V-1, ..., V-J
- Stack confirmado: [lista corta]
- Decisiones técnicas resueltas: ADR-1, ADR-3, ...
- ADRs pendientes que fases posteriores deben respetar como abiertas: ADR-N
```

**Archivar** `preguntas-diseno.md` (integrado) en `02-diseno/_archivo/` — automático, según `documentacion/CONVENCIONES.md` §5, sin preguntar. Luego ofrecer continuar con `/wireframes` (o directamente `/especificacion-tecnica` si el proyecto no tiene UI).

---

## Modo mejora (scoped)

Cuando este skill se invoca dentro del **track de mejoras post-MVP** (orquestado por el skill `mejora`), produce un **diseño reducido (delta)**, no un diseño completo:

- **Escribe en** `documentacion/mejoras/<id>-<slug>/diseno.md` (y `diseno-tecnico.md` solo si la mejora toca un ADR/arquitectura), **no** en `02-diseno/`.
- **Alcance = solo lo que la mejora cambia o agrega:** los RF nuevos/afectados, las vistas/flujos nuevos y los ADR nuevos si los hay. El `diseno.md` del MVP es **baseline de solo lectura** que se **referencia, no se reabre**.
- Continuar la numeración global de IDs del MVP (no reiniciar RF-1). Marcar qué es nuevo vs qué se referencia del baseline.
- Si la mejora **rompe** una decisión cerrada del MVP, no editarla aquí: registrarlo como ADR nuevo en el `diseno-tecnico.md` de la mejora y avisar.

El resto de la metodología (EARS, MoSCoW, FURPS+, ADR mini, patrón preguntas→merge) se mantiene, a escala de la mejora.

---

## Manejo de casos especiales

**El requerimiento tiene huecos que debieron resolverse antes.**
No "rellenar" inventando. Listar en `preguntas-diseno.md` con prefijo "⚠️ Esto debió cerrarse en el requerimiento — confirmar para avanzar:".

**El usuario pide agregar features nuevas durante el diseño.**
Permitido si vienen del usuario. Reformularlo en el formato RF (User Story + EARS) y agregarlo. Si lo propone el skill, pedir aprobación antes.

**El usuario pide saltar la sección de stack ("ya lo decidiremos en código").**
Resistir suavemente: sin stack decidido (al menos a nivel de qué runtime/framework) las fases siguientes quedan atadas. Si insiste, dejar §8 como "por definir en fase de implementación" y registrar el riesgo en §12.

**El requerimiento original no menciona ciertos RNFs típicos (mobile, accesibilidad, i18n).**
Inferirlos con `(?)` y pregunta. No asumir. Casos comunes a chequear:
- ¿Mobile responsive necesario?
- ¿Accesibilidad básica (WCAG)?
- ¿Idioma único o multi-idioma?
- ¿Funciona offline en algún caso?
- ¿Hay datos sensibles que requieran encriptación o tratamiento especial?

**Conflicto entre requerimiento y respuestas posteriores.**
Preguntar: "El requerimiento dice X pero ahora indicas Y — ¿actualizamos el requerimiento, o tomamos Y solo para el diseño?". No asumir.

---

## Anti-patrones a evitar

- ❌ Crear RFs que no estén en el requerimiento (incluso si parecen obvios).
- ❌ Inventar RNFs con métricas específicas sin que nadie las haya pedido ("performance < 200ms", "soporta 10k usuarios").
- ❌ Listar dependencias npm/pip con versiones (es del plan de implementación).
- ❌ Diseñar la UI: hex de colores, fuentes, spacing en px (es del wireframe).
- ❌ Estimaciones de esfuerzo o fechas (es del plan de implementación).
- ❌ Diagramas mermaid "decorativos" que no aportan información.
- ❌ ADRs que solo dicen "elegimos X" sin alternativas ni razones.
- ❌ User Stories tipo "Como sistema, quiero..." (no es una user story).
- ❌ Pantallas sin RFs asociados (síntoma de over-design).
- ❌ RFs sin pantallas asociadas (síntoma de hueco en §7).
- ❌ Documentos que repiten el requerimiento en vez de derivarlo (el diseño es **traducción técnica**, no copia).

---

## Output check (definition of done)

El diseño está cerrado cuando se cumple **todo**:

- [ ] Todos los RFs tienen prioridad MoSCoW asignada.
- [ ] Todos los RFs interactivos del MVP tienen al menos una vista (V-N) asociada (o superficie equivalente si no hay UI).
- [ ] Cada vista lista qué RFs cubre y qué flujos integra.
- [ ] El modelo de datos cubre todos los RFs persistentes del MVP.
- [ ] El stack está decidido para el MVP (sin `Propuesta pendiente` en lo que es Must).
- [ ] Las ADRs críticas para el MVP están en estado `Aceptada`.
- [ ] §11 Alcance MVP lista RFs, vistas y dependencias funcionales.
- [ ] No hay `(?)` ni TODO sin resolver en lo que es MVP.
- [ ] `INDICE.md` refleja la fase 2 cerrada.

Si algún check falla, el skill no termina — vuelve a la fase de preguntas.
