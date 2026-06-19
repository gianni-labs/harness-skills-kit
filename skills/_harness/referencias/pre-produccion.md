# Referencia: pre-producción

Checklist para usar **antes de soltar un release a producción** (`desarrollo` al cerrar un release que va a prod; `mejora` al cerrar una mejora que toca el camino a prod). Su función es **cobrar la deuda operativa diferida**: los atajos que se tomaron para construir/probar y que hay que resolver —o aceptar conscientemente— antes de exponer el sistema.

**Recurso opcional, no bloqueante.** Es un recordatorio, no una puerta que detenga el cierre. El gate de aprobación sigue siendo humano. Genérica; adaptar al stack real.

## El gate: cobrar los pendientes marcados

La fuente de verdad es la subsección **"Pendientes para producción"** de `BACKLOG.md`. Antes de prod:

- [ ] Revisar cada ítem con **"Bloquea release a prod" = sí**: o está resuelto (→ ✅), o se **acepta conscientemente** (registrar quién y por qué; pasa a deuda diferida normal). Ninguno se cruza a prod en silencio.
- [ ] Los ítems "Bloquea = no" siguen visibles como deuda diferida; no se pierden.
- [ ] Si durante esta revisión aparece deuda nueva, se anota en el backlog **antes** de decidir el release (no después).

## Disparadores típicos de deuda "antes de prod"

Categorías donde suele esconderse el atajo de pruebas. No son obligatorias: sirven para **no olvidar dónde mirar**.

### Secretos y configuración
- [ ] Credenciales/secretos fuera del código y fuera de variables de entorno planas → gestor de secretos (o el mecanismo que decida el proyecto).
- [ ] Config separada por entorno (dev/staging/prod); nada de valores de prod hardcodeados.
- [ ] Endpoints, claves y URLs apuntan a los servicios de **producción**, no a los de prueba.

### Datos y persistencia
- [ ] Datos de prueba / seeds / cuentas de demo removidos o aislados de prod.
- [ ] Migraciones aplicables y reversibles; estrategia de backup/restore definida.
- [ ] Sin rutas de "borrar/resetear todo" accesibles en prod.

### Flags y modos de desarrollo
- [ ] Feature flags en el estado correcto para el release.
- [ ] Modo debug/verbose apagado; mocks y stubs de servicios externos reemplazados por los reales.
- [ ] Logs no filtran secretos ni datos sensibles; nivel de log adecuado a prod.

### Operación y resiliencia
- [ ] Observabilidad mínima: logs, métricas y/o alertas para detectar fallas.
- [ ] Límites configurados (rate limits, timeouts, tamaños máximos) donde aplique.
- [ ] Comportamiento ante fallo de dependencias externas definido (degradar, reintentar, fallar claro).
- [ ] Costos acotados si hay servicios de pago por uso (incluye llamadas a LLM si el perfil lo incluye).

### Cierre cruzado
- [ ] La referencia `seguridad.md` (sección *Datos y configuración*) ya se aplicó: hay solapamiento intencional —secretos fuera del código se chequea en ambas.
