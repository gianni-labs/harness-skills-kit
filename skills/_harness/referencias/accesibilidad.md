# Referencia: accesibilidad

Checklist WCAG 2.1 AA esencial. La consultan `wireframes` (para no diseñar barreras) y `desarrollo` (al construir UI). **Solo aplica a proyectos con UI.**

## Navegación por teclado

- [ ] Todo elemento interactivo es enfocable con Tab.
- [ ] El orden de foco sigue el orden visual/lógico.
- [ ] El foco es **visible** (anillo/outline) — nunca se elimina sin reemplazo.
- [ ] Widgets propios tienen soporte de teclado (Enter para activar, Escape para cerrar).
- [ ] Sin trampas de foco: siempre se puede salir de un componente con Tab.
- [ ] Los modales atrapan el foco mientras están abiertos y lo devuelven al cerrar.

## Lectores de pantalla

- [ ] Toda imagen tiene texto alternativo (`alt`), o `alt=""` si es decorativa.
- [ ] Todo campo de formulario tiene etiqueta asociada (`<label>` o `aria-label`).
- [ ] Botones y enlaces tienen texto descriptivo (no "haz clic aquí").
- [ ] Botones que son solo ícono tienen `aria-label`.
- [ ] Un solo `<h1>` por página; los encabezados no saltan niveles.
- [ ] Los cambios de contenido dinámico se anuncian (`aria-live`).

## Visual

- [ ] Contraste de texto ≥ 4.5:1 (texto normal) o ≥ 3:1 (texto grande).
- [ ] Componentes de UI con contraste ≥ 3:1 contra el fondo.
- [ ] El color **no** es el único medio para transmitir información (agregar ícono/texto/patrón).
- [ ] El texto se puede escalar al 200% sin romper el layout.
- [ ] Sin contenido que parpadee más de 3 veces por segundo.

## Formularios

- [ ] Cada campo tiene etiqueta visible.
- [ ] Campos obligatorios indicados (no solo por color).
- [ ] Mensajes de error específicos y asociados al campo, visibles por más que el color (ícono, texto, borde).
- [ ] Errores de envío resumidos y enfocables.

## Contenido

- [ ] Idioma declarado (`<html lang="...">`).
- [ ] La página tiene un `<title>` descriptivo.
- [ ] Objetivos táctiles ≥ 44×44px en móvil.
- [ ] Estados vacíos con sentido (no pantallas en blanco).

## Anti-patrones frecuentes

| Anti-patrón | Problema | Arreglo |
|---|---|---|
| `div` como botón | No enfocable, sin teclado | Usar `<button>` |
| Sin `alt` | Imagen invisible al lector de pantalla | Agregar `alt` descriptivo |
| Estado solo por color | Invisible para daltónicos | Agregar ícono/texto/patrón |
| Eliminar el outline de foco | El usuario no sabe dónde está | Estilizar el outline, no removerlo |
| Dropdown propio sin ARIA | Inusable por teclado/lector | Usar `<select>` nativo o ARIA listbox correcto |
| `tabindex > 0` | Rompe el orden natural | Usar solo `0` o `-1` |

## Herramientas

Auditoría automática (axe, pa11y, Lighthouse → Accessibility) + prueba real con lector de pantalla (VoiceOver en macOS, NVDA en Windows). La automática detecta ~30%: la prueba manual con teclado y lector es insustituible.
