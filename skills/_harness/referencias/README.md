# Referencias del harness

Checklists de calidad **desacopladas de las skills**: las fases las **consultan bajo demanda** en vez de incrustar el contenido en cada `SKILL.md` (progressive disclosure — mantiene los prompts cortos y el conocimiento en un solo lugar).

Son **recurso opcional**, igual que las herramientas del entorno (`code-review`, `verify`): si una fase las necesita, las carga; si no aplican al perfil del proyecto, se omiten.

| Referencia | Cubre | La consultan | Condición de perfil |
|------------|-------|--------------|---------------------|
| [`seguridad.md`](seguridad.md) | OWASP, validación en límites, secretos, seguridad LLM | `especificacion-tecnica`, `desarrollo` | siempre (sección LLM solo si perfil LLM) |
| [`accesibilidad.md`](accesibilidad.md) | WCAG 2.1 AA esencial | `wireframes`, `desarrollo` | solo perfil **con UI** |
| [`performance.md`](performance.md) | Core Web Vitals, backend, anti-patrones | `documento-diseno` (RNF), `desarrollo` | siempre (sección frontend solo con UI) |
| [`testing.md`](testing.md) | Pirámide, niveles, naming, anti-patrones | `desarrollo` | siempre |
| [`pre-produccion.md`](pre-produccion.md) | Cobra deuda diferida antes de prod (secretos, datos de prueba, flags, observabilidad) | `desarrollo` (cierre de release), `mejora` (cierre) | solo si va a producción |

> Genéricas por diseño: no asumen un stack concreto. Donde un ejemplo es inevitablemente de un ecosistema (web), está marcado como ilustrativo — traducir al stack real del proyecto.

Estas referencias se instalan junto al resto del kit. Una fase las cita por ruta relativa (p. ej. "ver `_harness/referencias/seguridad.md`") cuando son relevantes; no las copia.
