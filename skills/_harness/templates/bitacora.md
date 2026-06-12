# Bitácora de desarrollo

Registro vivo del proyecto: se alimenta durante la documentación y sigue vivo en desarrollo, mejoras y mantenimiento. Cada entrada lleva **ID**, **tipo**, **origen** (fase/tarea que la produjo) y **refs** cruzadas, para rastrear de dónde viene cada decisión/aprendizaje.

**Tipos:** DECISIÓN · ISSUE · APRENDIZAJE
**Formato de entrada:**
`### B-{nnn} · {fecha} · {TIPO}` + línea `**Origen:** {fase/tarea} · **Refs:** {ADR/RF/V/pregunta} · **Por:** usuario|modelo|ambos`

---

<!-- Ejemplo (borrar al registrar la primera entrada real):

### B-001 · {fecha} · APRENDIZAJE
**Origen:** Especificación técnica · **Refs:** ADR-2 · **Por:** usuario+modelo
El proveedor de hosting elegido no soporta escritura en disco persistente. → Se mantuvo la persistencia del ADR-2 para el MVP; para desplegar habrá que migrar a un servicio de almacenamiento gestionado.
-->
