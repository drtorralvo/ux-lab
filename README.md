# UX Lab

Un laboratorio de herramientas de UX construidas como *skills* para Claude. La idea es simple: en vez de depender de mi memoria o de repetir el mismo proceso a mano cada vez, cada herramienta queda empaquetada como un módulo reutilizable, con su método, sus criterios y su código de verificación adentro.

Lo construye **Diana Torralvo**, product designer, como parte de un UX Lab en crecimiento. Cada módulo nace de un caso real, se prueba sobre sitios de verdad y se va afinando con el uso.

## Filosofía

Las dos skills comparten una misma forma de trabajar:

- **La herramienta señala y explica, la persona decide.** Nunca corrige a escondidas ni impone. Propone con un porqué y deja la decisión en manos de quien diseña.
- **Separar máquina y ojo humano.** Lo mecánico (cálculos, mediciones, conversiones) lo hace la máquina; lo de criterio (marca, semántica, cuándo romper la regla) es juicio humano, y se dice cuál es cuál.
- **Verificar antes de afirmar.** Un chequeo automático puede mentir (un atributo presente pero inútil, un "blanco sobre blanco" que en realidad es texto sobre una foto). Ningún número se reporta sin confirmarlo.
- **Honestidad sobre los huecos.** El valor del trabajo está en no maquillar lo que está mal.

## Módulos

### `skills/auditoria-accesibilidad`
Audita la accesibilidad de una página o de un set de páginas de un sitio, según WCAG, en seis dimensiones: textos alternativos, contraste de color, jerarquía de encabezados, navegación por teclado, etiquetas de formularios y uso del color. Cada hallazgo viene con evidencia en código, un recorte visual que señala dónde pasa, y una sugerencia concreta de arreglo que la persona decide si toma. Se puede correr en modo paso a paso (para aprender) o en modo informe.

### `skills/sistema-de-diseno`
Arma un sistema de diseño desde cero o audita uno que ya existe, guiando con preguntas. Deriva las escalas (color en OKLCH, tipografía modular, espaciado aditivo, tokens) desde pocas decisiones base, cruza accesibilidad en cada dimensión, y exporta un paquete completo (JSON en formato DTCG, CSS y componentes en código). Si hay conexión, lo lleva a Figma y a GitHub.

## Cómo se usan

Son skills de Claude. Dentro de un chat de Claude con las skills disponibles, se invocan por su nombre o describiendo la tarea ("audítame la accesibilidad de esta pantalla", "ármame un sistema de diseño para esta app"). Cada `SKILL.md` contiene el método completo y el código de verificación que la herramienta ejecuta.

## En construcción

Este lab va creciendo. Próximos módulos en la mira: benchmarking de referentes, usability testing y research cuali/cuanti.
