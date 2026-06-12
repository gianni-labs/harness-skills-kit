# Prompts de imágenes del README

> Banco de prompts para generar los visuales del README. Los mismos prompts están como comentarios HTML dentro de `README.md` (invisibles en la vista renderizada, por diseño). Acá quedan **visibles** para iterarlos.
>
> **Mecánica:** generá la imagen → guardala en `docs/assets/<archivo>.png` → descomentá la línea `![]` correspondiente en el `README.md`.
>
> **Idioma de los prompts:** en inglés a propósito — los generadores (Midjourney, DALL·E, Ideogram, etc.) rinden mucho mejor así.
>
> **Coherencia visual (importante):** las 4 imágenes deben compartir la misma identidad para verse como un set. Paleta única: **indigo (#4f46e5) + teal (#0d9488) + amber (#f59e0b) sobre off-white**. Linework fino y preciso, sombras suaves, fondo con textura sutil de grilla tipo blueprint, sin texto incrustado (los generadores lo escriben mal; el texto va como caption en el README).

---

## 1 · Hero / Banner

- **Archivo:** `docs/assets/hero.png` · **Formato:** 21:9 (banner ancho)

**Prompt (estilo editorial/tech):**
```
Wide hero banner for an AI-assisted software development methodology called
'Spec-Design'. Clean editorial tech illustration: a single elegant line that
starts as a loose hand-drawn scribble on the left (a raw idea) and gradually
transforms, left to right, into crisp geometric blueprint lines and finally into
neat stacked code blocks on the right. Limited palette: deep indigo, teal, warm
amber accents on an off-white paper background, subtle blueprint grid texture,
fine precise linework, soft long shadows. Minimal, premium, modern developer-tool
aesthetic. No text. 21:9 aspect ratio, high detail.
```

**Alternativa estilo dibujo:**
```
Same concept but as a soft hand-drawn ink-and-watercolor sketch, architectural
notebook style, loose confident pen strokes with light color washes.
```

---

## 2 · Diagrama del pipeline *(el visual central)*

- **Archivo:** `docs/assets/pipeline.png` · **Formato:** 16:9

**Prompt (estilo editorial/tech):**
```
Clean isometric technical illustration of a software pipeline drawn as an assembly
line of connected translucent glass panels, left to right, 8 stations. Each station
is a distinct labeled module representing a phase of work that hands a document to
the next. Style: editorial developer-tool aesthetic, thin precise linework, limited
palette of deep indigo + teal + warm amber on off-white, subtle blueprint grid in
the background, soft long shadows, gentle depth. Leave clear empty space under each
station for a caption. No readable text inside the art. 16:9, premium, high detail.
```

**Alternativa estilo dibujo:**
```
Hand-drawn architect's notebook diagram, ink lines with light watercolor fills,
arrows connecting labeled boxes left to right, casual but precise.
```

---

## 3 · Separación por altitudes

- **Archivo:** `docs/assets/altitudes.png` · **Formato:** 4:3

**Prompt (estilo editorial/tech):**
```
Conceptual illustration of 'separation by altitude' in software design: a
cross-section of layered horizontal strata, like a clean geological diagram or a
stack of floating translucent platforms at different heights. Top layer = a light
cloud / idea; descending layers become progressively more structured: wireframe
grids, then typed contracts, then an ordered task list, then solid code at the
bottom. A single thin vertical line connects all layers, showing decisions flowing
top-down only. Editorial tech style, indigo-teal-amber on off-white, fine linework,
soft shadows, no text. 4:3, premium, high detail.
```

---

## 4 · Ciclo post-MVP / rigor adaptativo

- **Archivo:** `docs/assets/ciclo-mejoras.png` · **Formato:** 16:9

**Prompt (estilo editorial/tech):**
```
Illustration of an adaptive improvement loop for software. Center: a circular flow
arrow returning a finished product (a small built cube) back into a funnel. The
funnel sorts incoming improvement requests into four lanes of increasing rigor,
labeled A B C D, each lane visibly longer and more detailed than the previous
(A = a single short step, D = a full multi-step pipeline). A small human-hand
'approval gate' icon sits before the build stage. Editorial tech style,
indigo-teal-amber on off-white, thin linework, soft shadows, room for captions,
no readable text. 16:9, premium, high detail.
```
