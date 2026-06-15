# Referencia: testing

Patrones de testing. La consulta `desarrollo` al verificar tareas y en el gate de fase. Principio rector: **los tests son la prueba** de que algo funciona — "se ve bien" no basta.

## Pirámide de tests

Distribución sana del esfuerzo (aproximada):

| Nivel | Proporción | Qué prueba | Velocidad |
|-------|-----------|------------|-----------|
| **Unitarios** | ~80% | lógica de una unidad aislada | milisegundos |
| **Integración** | ~15% | varias piezas juntas (DB, API, módulos) | segundos |
| **End-to-end** | ~5% | flujo completo de usuario | lento |

Anti-pirámide (mayoría E2E, pocos unitarios) = suite lenta, frágil y difícil de diagnosticar. Si dudas dónde poner un test, bájalo al nivel más bajo que pruebe la conducta.

## Qué testear

- **Comportamiento, no implementación.** El test sobrevive a un refactor si verifica *qué* hace el código, no *cómo*.
- **Caminos de error y bordes:** null/vacío, límites, entradas inválidas, fallos de servicios externos. Es donde viven los bugs.
- **La regla del bug:** todo bug arreglado se acompaña de un test que falla **antes** del fix y pasa después (previene la regresión).
- No testear lo trivial (getters, librerías de terceros) ni framework ajeno: testea **tu** lógica.

## Estructura de un test

- **AAA / Arrange-Act-Assert:** preparar, ejecutar, verificar — en ese orden, visible.
- **DAMP sobre DRY:** en tests, la claridad gana a la no-repetición. Un test debe leerse de corrido sin saltar a helpers. Algo de duplicación es aceptable si hace el test obvio.
- **Un concepto por test.** Si el nombre lleva "y", probablemente son dos tests.
- **Nombres que describen la conducta:** `crea_usuario_falla_si_el_email_ya_existe`, no `test1`.
- **Deterministas:** sin depender de fecha real, orden de ejecución, ni red sin mockear. Un test que falla intermitente es ruido que entrena a ignorar la suite.

## Dobles de prueba

- **Mock** solo los límites: servicios externos, red, reloj, aleatoriedad.
- No mockear lo que estás probando ni todo el mundo interno (eso prueba el mock, no el código).
- En integración, preferir lo real (DB de test, contenedor) sobre el mock cuando sea barato.

## Anti-patrones

| Anti-patrón | Problema |
|---|---|
| Test que sigue al código | Verifica la implementación; se rompe en cada refactor |
| Aserciones sobre detalles internos | Frágil; acopla el test a la estructura |
| Tests interdependientes | Fallan en cascada; no se pueden correr aislados |
| Sin caminos de error | El happy path pasa, producción se cae en el borde |
| Suite lenta ignorada | Si tarda, nadie la corre; equivale a no tener tests |
| Mockear todo | Verde que no prueba nada real |

## En el flujo del harness

- Cada tarea del plan declara su **criterio de done** verificable; el test es el medio para cumplirlo.
- El **gate de cierre de fase** corre los tests relevantes (`verify`/`run` o ejecución manual). No se cierra fase con tests en rojo.
- Si el entorno tiene la herramienta `verify`, usarla; si no, el fallback es correr la suite manualmente.
