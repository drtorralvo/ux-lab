---
name: "auditoria-accesibilidad"
description: "Auditar la accesibilidad de una página (o un set de páginas representativas de un sitio) según WCAG, dimensión por dimensión, con evidencia en código, recorte visual y sugerencias concretas de arreglo que la persona decide. En modo paso a paso o informe. Úsala cuando pidan revisar accesibilidad, contraste, alt, foco, encabezados, labels o uso del color."
---

# Auditoría de accesibilidad

Auditar una página web en seis dimensiones prácticas de accesibilidad, con un navegador abierto en la URL o pantalla a revisar. El objetivo no es solo encontrar fallas, sino enseñar el criterio: qué caza una máquina y qué necesita ojo humano.

## Antes de empezar: pregunta modo y alcance

Al arrancar, antes de abrir el navegador, pregunta dos cosas (si la persona ya lo dijo, no repreguntes):

**Modo de entrega:**
- **Paso a paso (enseñanza):** vas dimensión por dimensión en el chat, mostrando hallazgo + evidencia, explicando y preguntando qué haría ella antes de dar la solución. Es el modo por defecto cuando alguien está aprendiendo.
- **Informe:** corres todo y entregas un informe estructurado al final, con los pantallazos (completo + zoom) incrustados en cada hallazgo, el veredicto por dimensión y las prioridades. Arma el informe como documento o artefacto (no solo texto en el chat), una sección por dimensión, cada hallazgo con su evidencia en código, su recorte visual y su sugerencia de arreglo, y cierra con el panorama y las prioridades. Igual verifica a ojo antes de afirmar: el informe no es excusa para soltar números sin confirmar.

**Alcance:**
- La auditoría es siempre POR PÁGINA: el script lee el DOM de la pantalla cargada, no puede ver todo el sitio de un golpe.
- Para auditar "un sitio completo", corre la skill sobre un SET de páginas representativas, porque cada plantilla falla distinto (el login suele fallar donde el home no). Set típico de e-commerce: home, una de categoría o listado, una ficha de producto, el carrito o checkout, y el login. Pregunta cuáles cubrir y repite el proceso en cada una.

## Tres reglas de oro (aplican a TODO el proceso)

**1. Verifica siempre, nunca confíes en un chequeo automático a la primera.** Las mediciones rápidas dan falsos positivos y negativos. Un atributo presente NO es un atributo útil (un `alt` puede existir y ser basura técnica). Un número rápido puede mentir: el script de contraste reporta "blanco sobre blanco" cuando el fondo real es una imagen; el conteo de botones sin nombre incluye botones ocultos de 0x0 en menús colapsados; el test de foco con `.focus()` da falsos negativos porque `:focus-visible` solo se activa al tabular de verdad. Cuando un resultado huela raro, vuelve a probarlo bien: pon foco REAL tabulando con el teclado y toma pantallazo, filtra a solo elementos visibles, lee el CONTENIDO del atributo y no solo su existencia. Si un número no es confiable en esta página, DÍLO y verifica a ojo en vez de reportar un conteo falso.

**2. Cada hallazgo se muestra con evidencia en código Y con un recorte visual.** Nunca afirmes "esto está mal" a secas, ni ubiques el problema solo con palabras ("el botón de arriba a la derecha"): eso no ubica a la persona. Muestra el valor real (el `alt` exacto, la razón de contraste con su número, el `<input>` sin label, el color del enlace) Y un pantallazo con zoom a la zona puntual, diciendo "acá pasa esto". Flujo: screenshot completo para ubicar, luego `zoom` a la región exacta. Excepción: la jerarquía de encabezados no se ve en la pantalla sino en la estructura; ahí la evidencia es la lista de encabezados, no un recorte.

**3. Separa en cada dimensión** qué parte es objetiva y la caza una herramienta, y qué parte es criterio humano que la máquina no puede juzgar.

## Sugerencias de arreglo (la persona decide)

En CADA hallazgo, además de señalar el problema y el principio, ofrece una o dos sugerencias CONCRETAS de cómo cambiarlo: un valor puntual (ej. "sube el gris #9C9C9C a #595959, que da 7:1"), un color de reemplazo ya calculado para que pase, el `aria-label` exacto que le pondrías, el nivel de encabezado correcto. Nada de quedarte en "mejora el contraste" a secas. PERO enmárcalas siempre como sugerencia, no como orden: la persona es la diseñadora y decide. Usa fórmulas tipo "yo probaría", "una opción sería", "tú decides". Si una sugerencia toca la marca o la identidad visual, ofrece 2 caminos (uno más conservador, uno más atrevido) y deja que ella escoja. Cuando propongas un color de reemplazo, calcúlalo y verifica que pase antes de sugerirlo, no lo tires a ojo.

## Cómo trabajar

Ve dimensión por dimensión, no todas de golpe. En modo enseñanza, en cada una: muestra el hallazgo con su evidencia (código + recorte), explica por qué importa, di qué es máquina y qué es ojo humano, pregunta qué haría ella, y luego da tu sugerencia concreta para que decida. Corre los scripts en la consola del navegador (son el punto de partida, ajústalos a la página).

---

## 1. Textos alternativos (alt)

Qué revisar: que las imágenes informativas tengan un `alt` que DESCRIBA, no el nombre interno del archivo ni un slug del CMS. Las decorativas deben tener `alt=""` vacío a propósito. OJO especial: una imagen que es ENLACE con `alt=""` vacío es peor que una decorativa suelta, porque el lector llega a un enlace sin nombre (un enlace mudo).

Verificación (no te quedes con "tiene alt / no tiene alt", LEE el contenido del alt):
```js
(() => {
  const imgs = Array.from(document.querySelectorAll('img'));
  let noAlt=0, emptyAlt=0; const sample=[];
  imgs.forEach(img=>{
    const alt=img.getAttribute('alt');
    if(alt===null) noAlt++;
    else if(alt.trim()==='') emptyAlt++;
    else if(sample.length<30) sample.push(alt.slice(0,60));
  });
  return { total:imgs.length, noAlt, emptyAlt, muestraDeAlts: sample };
})()
```
Verificación extra (separa las vacías peligrosas — las que son ENLACE o grandes — de las decorativas legítimas):
```js
(() => {
  const imgs=[...document.querySelectorAll('img')];
  const vacias=imgs.filter(i=>i.getAttribute('alt')!==null&&i.getAttribute('alt').trim()==='');
  const peligrosas=vacias.map(i=>{const r=i.getBoundingClientRect();return {w:Math.round(r.width),h:Math.round(r.height),esEnlace:!!i.closest('a'),src:(i.currentSrc||i.src||'').split('/').pop().slice(0,40)};}).filter(o=>o.esEnlace||(o.w>150&&o.h>80));
  const sinAlt=imgs.filter(i=>i.getAttribute('alt')===null).map(i=>{const r=i.getBoundingClientRect();return {w:Math.round(r.width),h:Math.round(r.height),src:(i.currentSrc||i.src||'').split('/').pop().slice(0,40)};});
  return { vaciasPeligrosas:peligrosas.slice(0,12), sinAltAbsoluto:sinAlt.slice(0,12) };
})()
```
Evidencia a mostrar: los textos de `alt` reales + un recorte del banner o imagen con problema. Señala los slug técnicos (ej. `Web-Slider-ETTO-Celulares`: existe, un validador lo aprueba, pero el lector lee basura) y los enlaces mudos (imagen-enlace con alt vacío).

Máquina vs ojo: la máquina caza si el atributo existe o está vacío. Que el texto DESCRIBA de verdad es siempre juicio humano (hay que mirar la imagen real).

Sugerencia de arreglo: tres preguntas para un buen alt: ¿qué es, qué comunica y, si es un enlace, a dónde lleva? Si la imagen es clicable, el alt se piensa como la etiqueta del botón. Si es puramente decorativa, `alt=""`. Propone el alt concreto que le pondrías a la imagen revisada, para que la persona lo ajuste.

## 2. Contraste de color

Qué revisar: texto normal mínimo 4.5:1, texto grande (24px, o 18.66px en negrita) mínimo 3:1.

IMPORTANTE sobre el script: en páginas con muchas imágenes de fondo y degradados (homes de e-commerce), el cálculo automático NO es confiable y da falsos positivos (reporta "blanco sobre blanco" cuando el texto va sobre una foto). Por eso este script excluye etiquetas de código (`script`, `style`, `noscript`), excluye elementos ocultos, y cuando el fondo es una imagen o es incierto lo aparta para revisar a ojo en vez de inventar un número. NUNCA reportes el conteo crudo como la verdad; usa el script para encontrar sospechosos y CONFIRMA los reales a ojo con un recorte.
```js
(() => {
  const rgb=s=>{const m=s.match(/\d+(\.\d+)?/g);return m?m.slice(0,3).map(Number):null;};
  const lum=([r,g,b])=>{const a=[r,g,b].map(v=>{v/=255;return v<=0.03928?v/12.92:Math.pow((v+0.055)/1.055,2.4);});return .2126*a[0]+.7152*a[1]+.0722*a[2];};
  const ratio=(c1,c2)=>{const L1=lum(c1),L2=lum(c2),hi=Math.max(L1,L2),lo=Math.min(L1,L2);return Math.round((hi+.05)/(lo+.05)*100)/100;};
  const bg=el=>{let n=el;while(n&&n.nodeType===1){const s=getComputedStyle(n);
    if(s.backgroundImage&&s.backgroundImage!=='none')return {img:true};
    const c=rgb(s.backgroundColor);
    if(c&&!/,\s*0\)$/.test(s.backgroundColor))return {color:c};
    n=n.parentElement;}
    return {incierto:true};};
  const EXCLUIR=new Set(['SCRIPT','STYLE','NOSCRIPT','CODE']);
  const reales=[]; let sobreImagen=0, incierto=0;
  document.querySelectorAll('body *').forEach(el=>{
    if(EXCLUIR.has(el.tagName))return;
    if(!el.getClientRects().length)return; // oculto
    const t=Array.from(el.childNodes).some(n=>n.nodeType===3&&n.textContent.trim().length>1);
    if(!t)return; const st=getComputedStyle(el),fg=rgb(st.color); if(!fg)return;
    const b=bg(el);
    if(b.img){sobreImagen++;return;}
    if(b.incierto){incierto++;return;}
    const size=parseFloat(st.fontSize),bold=+st.fontWeight>=700,large=size>=24||(size>=18.66&&bold),req=large?3:4.5;
    const cr=ratio(fg,b.color);
    if(cr<req)reales.push({txt:el.textContent.trim().slice(0,38),color:st.color,fondo:'rgb('+b.color.join(',')+')',px:Math.round(size),ratio:cr,needs:req});
  });
  const seen=new Set(); const uniq=reales.filter(o=>{const k=o.txt+o.color+o.fondo;if(seen.has(k))return false;seen.add(k);return true;});
  return { fallasRealesColorVsColor:uniq.length, sobreImagen_revisarAOjo:sobreImagen, fondoIncierto:incierto, peores:uniq.sort((a,b)=>a.ratio-b.ratio).slice(0,16) };
})()
```
Evidencia a mostrar: el texto, su color y la razón exacta (ej. gris #9C9C9C sobre #F5F5F5 = 2.52, necesita 4.5) + un recorte con zoom al elemento para que se vea lo desvaido. Si el conteo trae ruido, dilo y quédate solo con los confirmados a ojo.

Máquina vs ojo: el número es objetivo CUANDO el fondo es color plano. Con fondo de imagen, es ojo. CÓMO arreglarlo sin romper la marca es criterio de diseño.

Sugerencia de arreglo: un color de marca de tono medio suele fallar como TEXTO sobre blanco pero brillar como FONDO con texto oscuro, o sobre fondo oscuro. Para un texto de tono medio, el blanco puro ya es el mejor fondo claro posible; cualquier gris resta contraste. El dato crítico (precios) va en gris muy oscuro casi negro; el color vibrante se reserva para acentos y fondos. Calcula y propone un valor concreto que sí pase (ej. el color más cercano a la marca que llegue a 4.5), y si toca la identidad, ofrece dos caminos para que la persona elija.

## 3. Jerarquía de encabezados

Qué revisar: un solo `h1` que sea el tema de la página y esté arriba; niveles que no se salten (no pasar de h1 a h3); que secciones y subelementos usen niveles distintos; sin encabezados vacíos; y que cosas que no son título (ej. un precio) no sean encabezados.

Verificación:
```js
(() => {
  const hs=[...document.querySelectorAll('h1,h2,h3,h4,h5,h6')];
  const list=hs.map(h=>({nivel:+h.tagName[1],texto:h.textContent.trim().slice(0,45)}));
  const skips=[]; let prev=0;
  list.forEach(h=>{if(prev&&h.nivel>prev+1)skips.push(`h${prev}->h${h.nivel}: ${h.texto}`);prev=h.nivel;});
  const counts={}; list.forEach(h=>counts['h'+h.nivel]=(counts['h'+h.nivel]||0)+1);
  const vacios=list.filter(h=>!h.texto).length;
  return { counts, saltosDeNivel:skips, encabezadosVacios:vacios, orden:list.slice(0,20) };
})()
```
Evidencia a mostrar: el conteo por nivel, los saltos concretos y dónde aparece el h1 en el orden (esta dimensión es estructural: la evidencia es la lista, no un recorte).

Máquina vs ojo: la máquina caza saltos, h1 faltante o duplicado y vacíos. Si la jerarquía REFLEJA la estructura real del contenido es juicio humano.

Sugerencia de arreglo (regla de oro): el nivel lo manda la jerarquía de la información, NO el tamaño visual. El tamaño se ajusta aparte con CSS. Un producto debería colgar (nivel más bajo) de su sección, no ser su hermano. Propone el árbol de niveles corregido concreto para la página revisada.

## 4. Navegación por teclado

Qué revisar: skip link ("saltar al contenido"); que no haya `tabindex` positivos; que todo lo clicable sea alcanzable con Tab; que el foco SE VEA; que los botones de solo ícono tengan nombre accesible.

Verificación estática (filtra a botones VISIBLES: los de 0x0 son menús ocultos y ensucian el conteo):
```js
(() => {
  const skip=[...document.querySelectorAll('a[href^="#"]')].find(a=>/salt|skip|contenido|content/i.test(a.textContent+a.getAttribute('href')));
  const posTab=[...document.querySelectorAll('[tabindex]')].filter(e=>+e.getAttribute('tabindex')>0).length;
  const btns=[...document.querySelectorAll('button')];
  const visiblesSinNombre=btns.filter(b=>{const r=b.getBoundingClientRect();if(r.width<4||r.height<4)return false;const st=getComputedStyle(b);if(st.visibility==='hidden'||st.display==='none')return false;return !(b.textContent.trim()||b.getAttribute('aria-label')||b.getAttribute('title')||b.querySelector('img[alt]:not([alt=""])'));});
  const ej=visiblesSinNombre.slice(0,8).map(b=>{const r=b.getBoundingClientRect();return {x:Math.round(r.x),y:Math.round(r.y),w:Math.round(r.width),clase:(b.className||'').toString().slice(0,35)};});
  return { tieneSkipLink:!!skip, tabindexPositivos:posTab, botonesVisiblesSinNombre:visiblesSinNombre.length, ejemplos:ej, totalBotones:btns.length };
})()
```
Verificación del foco: el script con `.focus()` da falsos negativos (muchos sitios usan `:focus-visible`, que solo se activa al tabular de verdad). Por eso la forma CONFIABLE de verificar el foco es manual: tabula de verdad con el teclado (presiona Tab varias veces) y TOMA PANTALLAZO para ver si aparece el recuadro de foco. Usa el script solo como pista inicial; el pantallazo manda.
```js
// Pista inicial (puede dar falsos negativos, confirmar con pantallazo tabulando):
(() => {
  const els=[...document.querySelectorAll('a[href],button')].filter(e=>{const r=e.getBoundingClientRect();return r.width>4&&r.height>4;}).slice(0,40);
  let visible=0; const sinFoco=[];
  els.forEach(e=>{e.focus();const st=getComputedStyle(e);
    const ok=(st.outlineStyle!=='none'&&parseFloat(st.outlineWidth)>0)||(st.boxShadow&&st.boxShadow!=='none');
    if(ok)visible++; else if(sinFoco.length<6)sinFoco.push((e.textContent.trim()||e.getAttribute('aria-label')||e.tagName).slice(0,28));
  });
  document.activeElement&&document.activeElement.blur();
  return { probados:els.length, conFocoVisible:visible, sinFocoVisible:els.length-visible, ejemplosSinFoco:sinFoco };
})()
```
Evidencia a mostrar: si falta el skip link; cuántos botones VISIBLES no tienen nombre y cuáles; y un pantallazo con zoom al recuadro de foco (o su ausencia) tras tabular de verdad.

Prueba manual (es la que vale para el foco): clic en la página y Tab, Tab, Tab. Tres preguntas: ¿llego a todo?, ¿siempre veo dónde estoy?, ¿activo con Enter o espacio?

Sugerencia de arreglo: el indicador de foco no se elimina, se rediseña acorde a la marca (propone un `outline` o `box-shadow` visible). Botones de ícono llevan `aria-label` (propone el texto exacto, ej. "Agregar al carrito"). Skip link al inicio del `body`.

## 5. Etiquetas en formularios

Qué revisar: cada campo necesita un nombre en código. El placeholder NO es un nombre (desaparece al escribir, suele ser de bajo contraste, el lector no lo trata confiablemente como nombre).

Verificación (detecta el campo que solo tiene placeholder):
```js
(() => {
  const fields=[...document.querySelectorAll('input,select,textarea')].filter(e=>(e.getAttribute('type')||'')!=='hidden');
  return fields.map(e=>{
    const id=e.id; let label=null;
    if(id){const l=document.querySelector('label[for="'+(window.CSS?CSS.escape(id):id)+'"]');if(l)label=l.textContent.trim();}
    if(!label){const w=e.closest('label');if(w)label=w.textContent.trim();}
    const aria=e.getAttribute('aria-label')||e.getAttribute('aria-labelledby');
    return {campo:e.getAttribute('name')||e.type,label,aria,placeholder:e.getAttribute('placeholder'),soloPlaceholder:!label&&!aria&&!!e.getAttribute('placeholder')};
  });
})()
```
Revisa los formularios REALES (login, checkout), no solo el buscador del home. El login suele ser el que peca. NO inicies sesión ni escribas datos personales reales; solo inspecciona el etiquetado.

Evidencia a mostrar: el campo sin label tal cual, con todos sus atributos, señalando que solo el placeholder lo nombra + recorte del formulario.

Sugerencia de arreglo (jerarquía de preferencia): primero `label` visible conectado al campo (sirve a quien ve y a quien no, y no desaparece); solo si el diseño no da espacio, `aria-label` invisible. El placeholder queda para un ejemplo del formato, nunca para nombrar. Propone el label o `aria-label` concreto para cada campo revisado.

## 6. No depender solo del color

Qué revisar: ninguna información puede comunicarse SOLO con color (daltonismo, ~8% de hombres; modos de alto contraste). Siempre una segunda pista: texto, ícono, subrayado, forma. Puntos típicos: enlaces dentro de texto corrido distinguidos solo por color, estados (error/éxito) solo en rojo/verde, descuentos solo por color.

Verificación (enlaces en texto que se distinguen solo por color: sin subrayado y sin cambio de peso respecto al texto que los rodea):
```js
(() => {
  const res=[];
  document.querySelectorAll('p a, li a, span a').forEach(a=>{
    const st=getComputedStyle(a),p=a.parentElement?getComputedStyle(a.parentElement):null;
    const subrayado=st.textDecorationLine.includes('underline');
    const mismoPeso=p&&st.fontWeight===p.fontWeight;
    const colorDistinto=p&&st.color!==p.color;
    if(!subrayado&&mismoPeso&&colorDistinto&&a.textContent.trim().length>1)
      res.push({texto:a.textContent.trim().slice(0,30),colorEnlace:st.color,colorTexto:p.color});
  });
  return res.slice(0,15);
})()
```
CUIDADO con este conteo: agarra ítems de menú (Mi cuenta, Categorías, etc.) que NO son el problema, porque ya tienen ícono y contexto de navegación. El pecado real es un enlace DENTRO de un párrafo de texto corrido. Filtra mentalmente los de menú y quédate con los que están en prosa.

Evidencia a mostrar: el enlace en prosa que solo se distingue por color, con su color y el del texto vecino + recorte. Contrasta con uno bien hecho (ej. distinguido por negrita) si existe en la misma página.

Máquina vs ojo: la máquina caza enlaces sin subrayado ni otra pista. Si un estado o dato depende solo del color suele necesitar revisión visual.

Sugerencia de arreglo: enlaces en texto corrido van subrayados (pista universal). Estados siempre con ícono o palabra además del color. Descuentos con texto ("-33%") y/o precio tachado, no solo rojo. Propone la segunda pista concreta que le agregarías al caso revisado.

---

## Cierre de la auditoría

Al terminar las seis (en cada página del set), da un panorama honesto: patrones de raíz que se repiten (muy común: cosas bien resueltas visualmente pero mal conectadas en el código), y prioriza por impacto (lo que afecta a cada producto o a cada página pesa más). Si auditaste varias páginas, señala qué falla es común a todo el sitio y qué es propio de una plantilla. Cierra con una lista de arreglos sugeridos priorizada, dejando claro que son sugerencias y que la persona decide cuáles toma. No maquilles el resultado: la honestidad sobre las fallas es el valor de la auditoría. Si algún script no fue confiable en esta página, dílo también: reconocer una limitación es parte de una auditoría seria.