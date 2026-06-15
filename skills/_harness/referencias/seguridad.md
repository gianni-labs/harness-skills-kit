# Referencia: seguridad

Checklist de seguridad para usar junto a `especificacion-tecnica` (al definir límites y validación) y al gate de revisión de `desarrollo`. Genérica; adaptar al stack real.

## Modelado de amenazas (empezar por acá)

Antes de elegir controles, 5 minutos pensando como atacante:

- [ ] Límites de confianza mapeados (requests, uploads, webhooks, APIs de terceros, salida del LLM).
- [ ] Activos nombrados (credenciales, datos personales, datos de pago, acciones de admin, movimiento de dinero).
- [ ] STRIDE por límite (Spoofing, Tampering, Repudiation, Info disclosure, DoS, Elevation).
- [ ] Casos de abuso escritos junto a los casos de uso ("¿cómo abusaría yo de esto?").

## Pre-commit

- [ ] Sin secretos en el código (revisar el diff por `password`/`secret`/`api_key`/`token`).
- [ ] `.gitignore` cubre `.env`, `*.pem`, `*.key` y equivalentes.
- [ ] El archivo de ejemplo de entorno usa placeholders, no valores reales.

## Autenticación

- [ ] Contraseñas con hash fuerte (bcrypt ≥12 rounds, scrypt o argon2) — nunca en claro ni con hash débil.
- [ ] Cookies de sesión: `httpOnly`, `secure`, `sameSite`.
- [ ] Expiración de sesión configurada.
- [ ] Rate limiting en el endpoint de login.
- [ ] Tokens de reset: con tiempo límite y de un solo uso.

## Autorización

- [ ] Todo endpoint protegido verifica autenticación.
- [ ] Todo acceso a un recurso verifica propiedad/rol (previene IDOR — acceder a datos de otro cambiando un id).
- [ ] Endpoints de admin verifican rol de admin.
- [ ] Claves/tokens con el mínimo de permisos necesario.

## Validación de entrada

- [ ] Toda entrada del usuario se valida **en el límite del sistema** (rutas de API, handlers de formularios).
- [ ] Validación por **allowlist** (lo permitido), no denylist (lo prohibido).
- [ ] Largos de string acotados (min/max); rangos numéricos validados.
- [ ] Formatos (email, URL, fecha) validados con librería, no con regex casera.
- [ ] Consultas a datos parametrizadas (previene inyección); nunca concatenar input en una query.
- [ ] Salida escapada según el contexto (previene XSS en UI).

## Datos y configuración

- [ ] Datos sensibles cifrados en tránsito (TLS) y en reposo cuando aplique.
- [ ] Secretos fuera del código (variables de entorno / gestor de secretos).
- [ ] Headers de seguridad configurados (CSP, HSTS, X-Content-Type-Options) si hay superficie web.
- [ ] CORS restringido a orígenes conocidos (no `*` con credenciales).
- [ ] Errores no filtran stack traces ni detalles internos al cliente.

## Dependencias

- [ ] Auditoría de dependencias corrida (`npm audit`, `pip-audit`, o equivalente del stack).
- [ ] Sin dependencias con vulnerabilidades críticas conocidas sin mitigar.

## Seguridad LLM *(solo si el perfil incluye LLM)*

- [ ] La salida del LLM se trata como **entrada no confiable**: se valida contra el JSON Schema antes de persistir o ejecutar.
- [ ] Defensa ante inyección de prompt: separar instrucciones de datos del usuario; no concatenar input crudo en el system prompt.
- [ ] La salida del LLM nunca se ejecuta como código ni se interpola en una query sin sanitizar.
- [ ] Datos sensibles no se mandan al proveedor del LLM sin necesidad (minimización).
- [ ] Límites de costo/llamadas para evitar abuso (el contrato del LLM fija el nº de llamadas).

## OWASP Top 10 — referencia rápida

Inyección · Autenticación rota · Exposición de datos sensibles · Control de acceso roto · Mala configuración de seguridad · Componentes vulnerables · XSS · Deserialización insegura · Logging/monitoreo insuficiente · SSRF.
