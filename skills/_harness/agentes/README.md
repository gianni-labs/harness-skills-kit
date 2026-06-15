# Personas de revisión

Personas especializadas para **revisión adversarial con contexto fresco**, pensadas para invocarse como **sub-agente** desde el gate de cierre de fase de `desarrollo`. La auto-revisión en primera persona es donde el modelo más relaja el criterio; una persona con mandato adversarial encuentra más.

| Persona | Rol | Cuándo se invoca |
|---------|-----|------------------|
| [`revisor-codigo.md`](revisor-codigo.md) | Staff Engineer, revisión de 5 ejes | en todo gate de cierre de fase |
| [`auditor-seguridad.md`](auditor-seguridad.md) | Ingeniero de seguridad, OWASP | cuando la fase toca datos sensibles o superficie expuesta |

**Opcionales con fallback.** Si el entorno no soporta sub-agentes, `desarrollo` aplica la misma grilla (5 ejes / OWASP) manualmente sobre el diff. El gate no se salta por falta de sub-agentes; cambia el *cómo*, no el *si*.

Ambas personas **respetan los contratos** de la especificación técnica como fuente de verdad: revisan cumplimiento, no reabren diseño. Si detectan que el contrato mismo está mal, lo reportan como hallazgo para volver a la fase dueña.
