---
name: "sistema-de-diseno"
description: "Armar o auditar un sistema de diseño (color, tipografía, espaciado, tokens, componentes, estados) guiando con preguntas, con accesibilidad cruzada. Deriva escalas desde pocas decisiones base, exporta un paquete completo (JSON DTCG, CSS, componentes en código) y, si hay conexión, lo lleva a Figma y GitHub. Úsala para crear, documentar o revisar un design system."
---

# Sistema de diseño

Armar un sistema desde cero o auditar uno que ya existe. La idea no es solo producir valores, sino enseñar el criterio: qué se deriva solo (y por eso es coherente) y qué es decisión de marca que ninguna fórmula resuelve.

## Regla base del enfoque

Casi nada se elige a dedo. La persona define unos pocos valores raíz (una fuente, un color de marca, una base de espaciado) y de ahí se DERIVA el resto con una regla: tipografía (escala modular), espaciado (escala aditiva), color (rampa tonal 50 a 900) y opacidades (escala de alfas). Elegir cada valor suelto es lo que produce sistemas incoherentes.

Filosofía, la misma de la skill de accesibilidad: **la skill señala y explica, la persona decide.** Nunca corrige a escondidas ni impone. Lo mecánico (cálculos, conversiones) lo hace la máquina; lo de criterio (marca, semántica, cuándo romper la regla) lo consulta.

**Mostrar, no solo describir, pero sin inundar.** Lo que se decide con el ojo (rampa de color, escala tipográfica, ritmo de espaciado, estados y neutros) se muestra visual, INLINE en la conversación (con herramientas de widget/visual), no en archivos aparte. Un hex suelto no le dice nada a quien diseña. Pero lo masivo (una librería de ~30 componentes) NO se pinta todo en el chat: sería un larguero y gasta tokens para algo que la persona ya sabe cómo se ve; eso se entrega como código (archivos), a lo sumo mostrando uno representativo inline. Los colores de estado y los neutros se muestran en cuadritos chiquitos con hex pequeño.

**Liderar con la recomendación, no soltar opciones en el vacío.** Como al inicio ya se sabe el tipo de producto (0.2), cada pregunta de criterio arranca con la recomendación aterrizada y un sí fácil: "para tu producto te recomiendo esta, pero tengo estas otras, ¿vamos con la que recomiendo o prefieres otra?". No preguntar desde cero como si no se supiera nada.

## Arranque: cuatro preguntas que definen todo

Pocas a propósito; no convertir el arranque en interrogatorio.

**0. ¿Modo rápido o paso a paso?** Rápido: defaults sensatos, sistema completo, y solo una lista corta de cruces con criterio real. Paso a paso: conversado, dimensión por dimensión, para aprender o control fino (explicar qué se decide y qué se deriva, el porqué, y antes de la versión "buena" preguntar qué haría la persona). En ambos lo mecánico se hace y se muestra; cambia cuánto se frena a preguntar.

**0.1. ¿Idioma de los nombres: inglés o español?** Aplica a todo, parejo (tokens y nombres del banco de componentes). Si la persona habla español, el banco va en español. A unos pocos términos que en la práctica casi siempre se dejan en inglés (tooltip, toast, chip, FAB) se les puede poner el inglés entre paréntesis, pero la persona decide. Nunca mezclar idiomas a mitad de camino.

**0.2. ¿Para qué es el sistema? (tipo de producto).** Pregunta ABIERTA, en el idioma de la persona ("una app para cuidar gatos"), no una lista de cajas. Inferir el tipo (e-commerce, dashboard, app móvil, sitio institucional, SaaS) y devolverlo como confirmación suave. Si la persona no sabe, hacerle 2-3 preguntas simples y deducir. Nunca exigir jerga: llevar, no interrogar. Esa respuesta viaja por todo el proceso y alimenta las recomendaciones de cada dimensión.

**1. ¿Nuevo o ya existe?** Nuevo → construir (2a). Existe → auditar (2b).

**2a. Construir, ¿con referentes o desde cero?** Con referentes (marca, capturas, paleta, un sitio): analizarlos y proponer los valores base. Sin referentes: pedir descripción corta e ir preguntando.

**2b. Auditar, ¿dónde vive?** Figma / sitio en vivo / repo de tokens / capturas (la más débil, sin evidencia en código). Se adapta a lo que haya; nunca se traba por un conector ausente.

## Reglas de oro

1. **Derivar antes que elegir.** Todo valor sale de una base y una regla.
2. **Cada decisión con su valor real y su porqué.** Al auditar, el token/estilo tal cual como evidencia.
3. **Separar máquina y ojo humano.**
4. **Accesibilidad cruzada, no al final** (contraste en color, tamaños en tipografía, foco y 'no solo color' en componentes). Engancha con la skill de accesibilidad.
5. **Lo manual se marca** como intencional en el token, con nota, para no pisarlo al regenerar.
6. **El JSON es la fuente completa de verdad; verificar paridad entre formatos.** Toda dimensión que exista en cualquier formato (Figma, CSS) tiene que estar TAMBIÉN en el JSON, y al revés. Antes de cerrar, cruzar que ninguna dimensión esté en un sitio y falte en otro (huecos reales detectados en pruebas: tipografía quedó en JSON y Figma pero no en el CSS; radios/sombras/breakpoints quedaron solo en Figma y faltaron en el JSON). Ese chequeo es parte del cierre, no opcional.

---

## 1. Color

Decisión base: los colores de marca. Se deriva todo lo demás.

**Rampa tonal en OKLCH, no HSL.** En HSL los pasos de brillo no se ven parejos (amarillos, tonos medios) y el tono se corre; en OKLCH (perceptual) se sienten parejos y el contraste queda predecible. L por paso (ajustable): 50→0.97, 100→0.93, 200→0.86, 300→0.77, 400→0.67, 500→0.58, 600→0.49, 700→0.41, 800→0.33, 900→0.25; croma suavizado en los extremos.

**Anclar el color de marca EXACTO.** El hex que da la persona se clava tal cual en su escalón (500 o 600 según su luminancia) y la rampa se construye alrededor; NUNCA se recrea un lookalike en su lugar (error real detectado en pruebas). Se marca como color de marca.

```python
import math
def oklch_to_hex(L, C, H_deg):
    H=math.radians(H_deg); a,b=C*math.cos(H),C*math.sin(H)
    l_=L+0.3963377774*a+0.2158037573*b; m_=L-0.1055613458*a-0.0638541728*b; s_=L-0.0894841775*a-1.2914855480*b
    l,m,s=l_**3,m_**3,s_**3
    r=+4.0767416621*l-3.3077115913*m+0.2309699292*s
    g=-1.2684380046*l+2.6097574011*m-0.3413193965*s
    bb=-0.0041960863*l-0.7034186147*m+1.7076147010*s
    def gm(c): c=max(0,min(1,c)); return 1.055*c**(1/2.4)-0.055 if c>0.0031308 else 12.92*c
    return "#%02X%02X%02X"%tuple(round(gm(x)*255) for x in (r,g,bb))
```
Para pasar el hex de marca a OKLCH (sacar L,C,H) usar la inversa (rgb→linear→LMS→OKLab→LCh) o `coloraide`.

**Contraste (WCAG), mismo motor que accesibilidad** (normal 4.5:1, grande 3:1). Marcar qué escalones sirven como texto sobre blanco/negro y avisar los que quedan en el filo (ej. 4.45 casi pasa).
```js
const lum=([r,g,b])=>{const a=[r,g,b].map(v=>{v/=255;return v<=0.03928?v/12.92:Math.pow((v+0.055)/1.055,2.4);});return .2126*a[0]+.7152*a[1]+.0722*a[2];};
const ratio=(c1,c2)=>{const L1=lum(c1),L2=lum(c2),hi=Math.max(L1,L2),lo=Math.min(L1,L2);return Math.round((hi+.05)/(lo+.05)*100)/100;};
```

**Mostrar la rampa visual e INLINE** (muestras reales, hex, "Aa" en el tono legible, uso por contraste). El escalón ancla se señala.

**Color NO es solo la marca.** Un sistema siempre necesita, y la skill los PROPONE y los muestra (compactos, cuadritos chiquitos con hex pequeño):
- **Estados:** error, advertencia, éxito, info (armonizados con la marca; cada uno puede tener su mini rampa).
- **Neutros:** escala de grises, idealmente con un tinte muy leve de la marca para cohesión (opción: gris puro).

**Dos capas.** Primitivos (`blue/600`): inventario crudo. Semánticos (`primary`): apuntan a un primitivo con alias. Si cambia la marca, se reapunta una flecha y se actualiza todo. Componentes consumen semánticos, nunca primitivos directos. (En proyecto chiquito de una marca, la doble capa es opcional.) Set base de semánticos: primary, secondary, surface, background, border + estados.

**Opacidades / alfas.** Escala fija sobre blanco y negro (4%, 8%, 16%, 40%, 60%) para overlays, hover, disabled.

## 2. Tipografía

Base: familia + tamaño base (16) + ratio. **Escala modular (multiplicativa):** `base × ratio^n`. Multiplica (no suma) porque el tamaño se percibe por proporción. Ratios: 1.2 suave, 1.25 versátil, 1.333 contraste, 1.5 dramático.

**Elegir el ratio VIENDO, inline:** mostrar 2-3 escalas con la fuente real, lado a lado, y que la persona elija con el ojo (o con botón). No soltar un ratio a ciegas. Para interfaces densas, los ratios suaves (1.2-1.25) suelen ir mejor (recomendar según producto).

**px y rem, los dos.** rem (`px÷16`) por responsive y accesibilidad; px al lado como referencia.

**Roles por función (Material 3):** Display, Headline, Title, Body, Label (cada uno large/medium/small). A cada rol su combo completo: tamaño + peso + line-height + tracking. En web, se puede mapear a H1–H6.

**OJO con la tipografía en el export** (regla de oro 6): un rol es un COMBO (familia + tamaño + peso + line-height), no un valor suelto. Un generador de CSS ingenuo que solo maneja valores sueltos la SALTA. Hay que descomponer cada rol en variables por propiedad (`--font-title-size`, `--font-title-weight`, `--font-title-line-height`, `--font-title-family`) y dar las clases listas (`.text-title { ... }`). En Figma va como estilo de texto. Verificar que la tipografía llegue a los tres (JSON, CSS, Figma).

## 3. Espaciado y grid

Base: la persona elige, y la skill lidera con la recomendación según producto: múltiplos de 4 (control fino), de 8 (orden, estándar; recomendado en móvil y denso), o escala personalizada. **Aditiva** (el espacio se percibe lineal). **Abrir arriba:** 4, 8, 12, 16, 24, 32, 48, 64 (quitar intermedios grandes que casi no se usan). Una escala es una paleta de la que se saca lo necesario; no se usan todos los pasos. En personalizada, marcar lo que rompe el patrón y preguntar (¿corrijo o intencional?), nunca corregir a escondidas. Mostrar el ritmo visual (barras) inline.

## 4. Tokens y entregable

No genera nada nuevo: nombra lo definido para exportarlo. Token = decisión guardada; variable = como Figma le dice adentro. **Nombres por función** (`color-primary-background`, `space-md`), no por número. **Formato DTCG** (JSON con `$value`/`$type`; semánticos con alias `{color.blue.600}`): idioma común de Tokens Studio, Style Dictionary, herramientas de código. De ahí salen las variables CSS solas.

**El JSON debe incluir TODAS las dimensiones**, no solo color/tipo/espaciado: también radios, sombras/elevación, breakpoints, z-index y, si aplica, movimiento. Tipos DTCG: `color`, `dimension` (tamaños, radios, espaciado, breakpoints), `typography` (combo), `shadow` (combo: color, offsetX/Y, blur, spread). Nota: el formato `shadow` de DTCG algunas versiones de Tokens Studio lo leen distinto; si no entra limpio, ajustar al formato que esa versión espera.

**El export va COMPLETO y AL FINAL, un solo paquete, no por partes.** Carpeta/zip con: `tokens/` (JSON DTCG + CSS), `components/` (cada componente como código), y un README. Durante el proceso se muestran previews, pero el archivo descargable se arma una sola vez con todo. **Antes de entregar, correr la verificación de paridad (regla 6):** cada dimensión presente en JSON, CSS y Figma; nada cojo.

## 5. Componentes

Banco en seis familias (Material 3 + HIG + web, ~30). La persona MARCA de un banco agrupado lo que usa (seleccionable inline, con checkboxes); lo que no marca, no se hace. **Liderar con recomendación por producto:** premarcar lo típico del tipo detectado (panel clínico → tablas, cards, campos, fechas, navegación; móvil → otra cosa) y decirlo de frente ("como tu producto es X, te recomiendo estos"), no en silencio.

- Acciones: botón, botón de ícono, FAB, botón segmentado.
- Entrada y selección: campo de texto, área de texto, lista desplegable, casilla, opción (radio), interruptor, deslizador, selector de fecha, carga de archivos.
- Navegación: barra superior, barra inferior, menú lateral, pestañas, migas de pan, paginación, menú.
- Contenedores: tarjeta, ventana modal, hoja inferior, acordeón, lista, tabla, separador.
- Comunicación y feedback: alerta/banner, notificación (toast), globo de ayuda (tooltip), insignia (badge), barra de progreso, carga esqueleto, estado vacío.
- Multimedia y apoyo: avatar, etiqueta (chip), ícono, imagen, video.

Ningún sistema grande (Material, Ant, Fluent, Carbon, Atlassian, Polaris) inventa familias nuevas; solo le meten profundidad a los que ya están. Por producto, inspirarse en Polaris (e-commerce) o Carbon (dashboards).

**Los componentes se entregan como CÓDIGO que consume los tokens** (lee `var(--color-...)`, sin colores pegados a mano, así cambian solos con la marca). Ese código es lo portable: sube a Figma con Code Connect y se usa en Codex / Claude Code / Antigravity. NO se pintan los 30 en el chat; se genera el código (archivos). Generar los 30 bien es trabajo de producción (un rato largo): avisarlo y, si la persona quiere, ir por tandas. En test basta con 2-3 de tipos distintos (ej. botón = táctil, campo = entrada) para validar que el patrón sirve.

## 6. Estados y variantes

Variante = versión (primario vs secundario); estado = condición (ese primario en hover). Se definen UNA vez por tipo de componente. Táctiles: default, hover, focus, active, disabled, loading. Entrada: default, focus, relleno, error, disabled (a veces success). Seleccionables: unchecked/checked + anteriores. La estructura la heredan los estándares; el estilo (color del hover, opacidad del disabled) sale de los tokens. **Foco visible y error con texto, no solo color** (accesibilidad, no negociable).

---

## 7. Handoff: Figma y GitHub (al final, automático si hay conexión)

Para que la persona no haga nada manual, al cerrar PREGUNTA por las conexiones y hace el empujón:

**¿Conectado a Figma?** Si sí, crear en Figma (en un archivo NUEVO, nunca pisar uno existente sin permiso). **Comprobado que funciona de verdad:** colecciones de variables (primitivos, semánticos con alias como `primary`→`blue/600`, espaciado, radios, breakpoints), estilos de texto (tipografía, usa la fuente real si está instalada), estilos de efecto (sombras), y COMPONENTES con variantes de estado enlazadas a las variables (ej. botón: relleno→semántico, padding→espaciado, radio→radius). Si cambias un token, el componente cambia solo. **Lo que NO se puede:** las grillas/layout grids como estilo reutilizable global (la API de Figma no lo soporta confiable; van marco por marco). Flujo: cargar las skills figma-use / figma-generate-library antes de escribir; colores en 0–1; `setBoundVariableForPaint` devuelve un paint nuevo (reasignar); cargar la fuente antes de texto. Decir honestamente qué se pudo y qué no.

**¿Conectado a GitHub?** Matiz importante: no basta con que Claude esté AUTORIZADO; el conector debe estar INSTALADO con acceso al repo (en GitHub, Settings → Installed GitHub Apps → Claude → repos). Si solo está autorizado (Authorized, no Installed), guiar a reconectar eligiendo repositorios. Además, la sesión NO puede crear un repo nuevo desde cero (está atada a repos ya configurados; `gh repo create` y la API REST de crear repo fallan). Camino comprobado: la persona crea el repo VACÍO en GitHub (sin README ni .gitignore), luego `add_repo` lo adjunta, se clona, se copia el paquete, commit y `git push`. Eso sí funciona de punta a punta. Si no se puede conectar, armar el paquete y guiar a subirlo a mano (web de GitHub, "uploading an existing file") en dos clics.

## Cierre

**Verificación de paridad primero** (regla 6): confirmar que cada dimensión esté en JSON, CSS y Figma; nada en un sitio y ausente en otro. Luego, panorama honesto: inconsistencias de raíz (cosas bien resueltas visualmente pero sin token detrás, o tokens que nadie usa) y prioridad por impacto. Sin maquillar: la honestidad sobre los huecos es el valor del trabajo.