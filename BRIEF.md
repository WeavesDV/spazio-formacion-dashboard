# BRIEF — Dashboard de formación, Institute Spazio Bodywork

## Qué hay que construir
Un **dashboard de una sola página** que explique, en lenguaje llano, la clasificación de la base de
contactos de Spazio: quién hizo qué módulos de la formación y hasta dónde llegó. El destinatario es la
dueña de la escuela (perfil no técnico). Debe entenderse sin explicación previa.

## Entregable
- **Un único archivo `index.html`**, autocontenido: CSS y JS embebidos, **datos embebidos (inline)** y
  **cero peticiones de red**. Debe abrirse bien con doble clic (`file://`).
  - NO usar `fetch()` del JSON: Chrome bloquea fetch/XHR en `file://`. Los datos se leen del JSON con un
    paso de build (Python) y se incrustan como `const DATA = {...};` en el HTML final.
- Sin dependencias externas: nada de CDN, ni librerías de gráficos, ni Google Fonts. Tipografía del sistema:
  `-apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`.
- Gráficos con SVG o barras CSS escritas a mano. Sin canvas salvo imprescindible.

## Fuente de datos — única fuente de verdad: `datos_clasificacion.json`
- `meta`: fuente, contactos_totales (5205), personas_con_modulos (1213), metodo.
- `kpis`: contactos, con_modulos, formacion_larga, con_anatomia, solo_intro, solo_complementarios,
  sin_formacion, declarada_sin_modulos, retomar, deuda, aptos_envio, compras_pagadas, a_revisar.
- `niveles`: {basico, intermedio, avanzado, anatomia} → personas de formación larga.
- `situacion`: completa_nivel / abandono_parcial / recursante / en_curso.
- `formato`: semanal / mensual / intensivo_residencial / online / mixto / desconocido.
- `sede`: barcelona / madrid / baleares / pais_vasco_valencia / andalucia_galicia / extranjeros / sin_dato.
- `cohorte`: tramos de años (2011_2014 … 2026, desconocida).
- `grupos`: formacion_larga / anatomia_solo / complementarios_solo / solo_introductorio / sin_formacion.
- `interes`: curso por el que pidieron información.
- `modulos`: lista de {prog, cod, mod, personas} — **personas distintas** por módulo.
  `cod` ordena: INTRO, BAS, INT, AVZ, ANA, HAM, TEP, ESA, EM, COMP, ANUAL.
- `personas`: 1213 registros — {n nombre, mail, mov, sede, loc, largo (SI/NO), nivel, sit, nm (nº módulos),
  ht (horas anotadas), det: [{p programa, m módulo, h horas, f formato, n aviso, e true=horas fuera de norma}],
  retomar, deuda, apto (bool), ua (última actividad), act (actividad 0-3), revisar}.
- `calidad`: 10 hallazgos de desorden del CRM {hallazgo, detalle, filas, accion}.
- `comparativa_crm`: {categoria_alumnos 722, campaign_students 1261, modulos_reales 1213, marcados_alumno 1338}.

**Regla dura: todos los números salen de este JSON, calculados en JS desde `DATA`. No inventar, no
recalcular por otra vía, no redondear a la ligera.**

## Secciones obligatorias (en este orden)
1. **Cabecera**: título, subtítulo con fuente y fecha (13 de octubre de 2026) y la frase titular:
   «1.213 personas de la base tienen algún módulo de formación hecho».
2. **KPIs** (6 tarjetas): contactos totales · con módulos · formación larga · solo introductorio ·
   aptos para envío · compras pagadas.
3. **Embudo de la formación**: barras horizontales del recorrido real, mostrando la caída:
   Introductorio 995 → Básico II 691 → Básico III 629 → Básico IV 594 → Básico V 159 →
   Intermedio I 258 → Intermedio IV 226 → Avanzado I 92 → Avanzado IV 73.
4. **Nivel alcanzado** y **situación** (completo / abandono parcial / recursante / en curso).
5. **Módulo por módulo**: tabla agrupada por programa con el nº de personas.
6. **Dónde están y de cuándo son**: sede y cohorte.
7. **Por qué el CRM no cuadraba**: bloque comparativo con las tres cifras (Categoría = 722 alumnos,
   Campaign List = 1.261 Students, módulos reales = 1.213) y una línea explicando que cada campo se llenó
   a mano en momentos distintos y solo los módulos son comprobables.
8. **Calidad de datos**: los 10 hallazgos en tarjetas con su acción sugerida.
9. **Tabla de alumnos** (1213): buscador por nombre/email, filtros por nivel, situación, sede,
   «¿formación larga?» y «apto envío», orden por nº de módulos, botón de exportar a CSV lo filtrado, y
   detalle expandible con los módulos y horas (marcando las que están fuera de norma).
10. **Pie**: origen de los datos, método (catálogo canónico de cursos + clasificación con Jev/TypeSafe
    System One) y las tres preguntas abiertas (qué es el código EM; horas oficiales por módulo; si Anatomía
    cuenta dentro de la formación larga).

## Honestidad de los datos (hay que mostrarlo)
- «Formato desconocido en 477 fichas»: vacío del CRM, no error del análisis.
- La «actividad 0-3» es una estimación razonada: sirve para priorizar, no para excluir.
- Solo 70 fichas tienen datos reales de pedido; el resto de indicios de compra vienen de notas.

## Diseño
- Estética editorial y sobria, tipo informe de consultoría. Nada de plantilla genérica de dashboard.
- Paleta: fondo `#FBF9F6`, tinta `#1F2933`, acento principal `#0E7C7B`, acento secundario `#E0A458`,
  apoyo `#5B6670`, éxito `#2F8F5B`, alerta `#B4453C`. Contraste AA mínimo.
- Una columna de lectura en pantallas estrechas; máximo 1200 px de ancho en escritorio.
- Cifras grandes y legibles; el número manda y la etiqueta acompaña.
- Sin animaciones llamativas, sin emojis decorativos, sin sombras exageradas.
- Debe imprimirse bien en A4 (filtros y botones ocultos al imprimir).

## Restricciones técnicas
- JavaScript clásico (ES2020), sin frameworks ni transpilación.
- Sin `localStorage` obligatorio: si está bloqueado, la página sigue funcionando.
- Accesible: etiquetas en los inputs, foco visible, navegación por teclado en la tabla, `aria-label`
  donde haga falta.
- Español de España, sin anglicismos innecesarios.

## Verificación antes de terminar (el agente debe comprobarlo)
1. El HTML abre sin errores de consola y sin peticiones de red.
2. Los 6 KPIs muestran exactamente: 5205 / 1213 / 801 / 295 / 725 / 115.
3. El embudo muestra 995 → 691 → 629 → 594 → 159 → 258 → 226 → 92 → 73.
4. La tabla tiene 1213 filas; los filtros y el export funcionan.
5. Se ve bien a 375 px, 768 px y 1440 px.
