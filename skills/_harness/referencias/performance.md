# Referencia: performance

Checklist de performance. La consultan `documento-diseno` (al fijar RNF de Performance, FURPS+) y `desarrollo` (al construir y en el gate). Principio rector: **medir primero, optimizar lo que importa** — nunca optimizar por intuición.

## Core Web Vitals *(objetivos, si hay UI web)*

| Métrica | Bien | Aceptable | Malo |
|--------|------|-----------|------|
| LCP (Largest Contentful Paint) | ≤ 2.5s | ≤ 4.0s | > 4.0s |
| INP (Interaction to Next Paint) | ≤ 200ms | ≤ 500ms | > 500ms |
| CLS (Cumulative Layout Shift) | ≤ 0.1 | ≤ 0.25 | > 0.25 |

## Frontend *(si hay UI)*

- [ ] Imágenes en formato moderno (WebP/AVIF), con tamaño responsivo y `width`/`height` explícitos (evita CLS).
- [ ] Imágenes bajo el fold con carga diferida; la imagen LCP con prioridad alta y **sin** lazy.
- [ ] Bundle inicial acotado; code splitting por rutas y features pesadas.
- [ ] Sin JS bloqueante en el `<head>` (usar `defer`/`async`).
- [ ] Tareas largas (>50ms) partidas para no bloquear el hilo principal (palanca principal de INP).
- [ ] Trabajo no crítico (analytics, logging) fuera de los handlers de interacción.
- [ ] Animaciones con `transform`/`opacity` (aceleradas por GPU), no con propiedades que disparan layout.
- [ ] Listas largas virtualizadas.
- [ ] Fuentes: WOFF2, 2–3 familias máximo, `font-display: swap`, subsetting.

## Backend

- [ ] Sin patrones N+1 (usar joins / eager loading).
- [ ] Consultas con índices apropiados en columnas filtradas/ordenadas.
- [ ] Endpoints de listado **paginados** (nunca traer la tabla entera).
- [ ] Connection pooling configurado; logging de consultas lentas activo.
- [ ] Sin cómputo pesado **síncrono** dentro del handler de la request.
- [ ] Operaciones en lote en vez de bucles de llamadas individuales.
- [ ] Compresión de respuesta (gzip/brotli) y caché donde aplique.

## Infraestructura

- [ ] CDN para estáticos; servidor cerca de los usuarios (o edge).
- [ ] Estáticos cacheados con `max-age` largo + hash de contenido.
- [ ] Endpoint de health check para el balanceador (si aplica).

## Anti-patrones

| Anti-patrón | Impacto | Arreglo |
|---|---|---|
| Consultas N+1 | Carga de DB crece lineal | Joins / includes / batch |
| Consultas sin límite | Memoria/timeouts | Paginar siempre, agregar LIMIT |
| Faltan índices | Lecturas lentas al crecer | Indexar columnas filtradas/ordenadas |
| Imágenes sin optimizar | LCP lento, ancho de banda | WebP, tamaños responsivos, lazy |
| Bundle grande | Time to Interactive lento | Code split, tree shake, auditar deps |
| Bloquear el hilo principal | INP pobre, UI trabada | Partir tareas largas, offload a workers |
| Fugas de memoria | Memoria creciente, crash | Limpiar listeners, intervals, refs |

## Cómo medir

1. **Datos de campo primero** (usuarios reales) antes de optimizar.
2. **Perfilar** la interacción/consulta lenta concreta (DevTools Performance, profiler del backend, EXPLAIN en la query).
3. **Probar en hardware/condiciones realistas** (móvil de gama media, red 4G) — muchos problemas solo aparecen ahí.
4. Optimizar, **re-medir**, confirmar la mejora. Sin medición no hay optimización, hay adivinanza.
