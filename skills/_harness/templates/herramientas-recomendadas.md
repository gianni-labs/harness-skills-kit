# Herramientas recomendadas

Skills/agentes/plugins sugeridos por fase. El skill `desarrollo` propone usarlos al entrar a la fase; tú decides. **Todas son opcionales y dependen del entorno:** si una no está disponible, el fallback es lint/build/tests manuales y revisión propia del diff.

| Herramienta | Fase sugerida | Estado | Cómo se invoca |
|-------------|---------------|--------|----------------|
| code-review | gates de fase | sugerida | /code-review |
| security-review | gates (datos sensibles / superficie expuesta) | sugerida | /security-review |
| verify / run | gates de fase | sugerida | /verify, /run |
| {otras del entorno, según el stack} | — | — | — |

> Estados: sugerida · en uso · no disponible · descartada.
