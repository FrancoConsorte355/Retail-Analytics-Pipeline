# 📊 Retail Analytics: Data & Modelo de Inteligencia de Negocios

## Resumen Ejecutivo del Proyecto

Este proyecto consiste en el desarrollo de un **reporte de ventas integral para una empresa del sector retail**, diseñado específicamente para la alta gerencia. Su propósito es proveer una visibilidad clara sobre las métricas clave de rendimiento (KPIs) y facilitar la toma de decisiones estratégicas basadas en datos. 

**¿Qué se hizo?** Se analizó, limpió y normalizó un voluminoso conjunto de datos de ventas, corrigiendo anomalías (duplicados, valores atípicos y nulos) para construir un modelo de datos robusto. Sobre este modelo estructurado se diseñó un dashboard interactivo, enfocado en la experiencia e interfaz del usuario (UI/UX).

**Herramientas utilizadas:** 
- **Python (Pandas, NumPy):** Para la extracción, transformación, auditoría y limpieza técnica de los datos.
- **Power BI (DAX):** Para el modelado de datos, inteligencia de negocios y visualización gerencial.

---

## 1. Contexto de Negocio y Descripción del Proyecto

Este proyecto consiste en una canalización de datos. El objetivo central de la iniciativa es consolidar, limpiar y establecer la integridad relacional sobre 4 datasets crudos transaccionales y dimensionales de una empresa de retail: **Clientes**, **Productos**, **Cabeceras de Venta (Orders)** y **Líneas de Venta (Order Lines)**.

A través de esta ingesta, se corrigen anomalías históricas, datos cruzados, falta de estandarización en nomenclaturas y duplicidades críticas. El resultado es un modelo de datos robusto, matemáticamente auditado y estructurado en su Primera Forma Normal (1NF), listo para integrarse a herramientas de visualización (BI), reportes contables y modelos predictivos, garantizando la fiabilidad total de los KPIs (Key Performance Indicators) financieros del negocio.

---

## 2. Stack Tecnológico

El proyecto se despliega íntegramente sobre el ecosistema analítico de Python.

- **Lenguaje Principal:** Python 3.x
- **Manipulación y Análisis de Datos:** Pandas, NumPy
- **Entorno de Ejecución Exploratorio:** Jupyter Notebooks (`.ipynb`)
- **Formatos de Ingesta/Egresa:** Archivos separados por comas (`.csv`), codificación UTF-8.

---

## 3. Estructura del Proyecto

El flujo de directorios está lógicamente separado por etapas del ciclo de vida del dato:

```text
📁 Directorio Raíz
 ┣ 📂 Raw               # Contiene los archivos crudos originales y sin procesar (.csv)
 ┣ 📂 Processed         # Carpeta de egreso generada automáticamente para albergar datos limpios cruzables
 ┣ 📜 Limpieza_Clientes.ipynb
 ┣ 📜 Limpieza_Productos.ipynb
 ┣ 📜 Limpieza_Ventas_Order.ipynb
 ┗ 📜 Limpieza_Ventas_Order_Line.ipynb
```
> **Nota:** La carpeta `/Processed` es creada y poblada dinámicamente al correr los notebooks, albergando las "Golden Tables" finales.

---

## 4. Justificación Profesional: Transformaciones Previas a Power BI

Realizar la limpieza y transformación de datos en una capa de procesamiento previa (como Python o SQL) en lugar de hacerlo directamente dentro de Power BI (Power Query o DAX) es una **práctica fundamental y estratégica en la ingeniería de bases de datos y BI**:

1. **Optimización del Rendimiento:** Power BI y su motor analítico están altamente especializados para cálculos en memoria orientados a la rápida visualización. Forzar al entorno a ejecutar transformaciones estructurales pesadas fila a fila impacta severamente en el tamaño de los archivos, monopoliza recursos de RAM local, e incrementa significativamente el tiempo general de actualización.
2. **Desacoplamiento Tecnológico:** Mantener el estado purificado de los datos en un ambiente neutral como Python nos entrega una fuente de la verdad (*Golden Records*) indepenzdiente y desacoplada de un proveedor visual concreto. De esta manera, el set resultante en la carpeta `/Processed` es fácilmente consumible por Tableau, Qlik, u otros ecosistemas.
3. **Control de Versiones y Auditoría de Scripts:** En el lenguaje M nativo para Power Query controlar y rastrear los cambios con herramientas estándar (como Git) es ineficiente. Las celdas de Python/Pandas ofrecen auditoría paso a paso, revisión de pares (*code review*) en repositorios, y control local temporal.
4. **Aislamiento Temprano y Corrección de Errores Transaccionales:** Normalizar nomenclaturas, cruzar nulos, reparar errores de tipeo negativos y recuperar métricas faltantes antes de ser leídas por bases relacionales o análisis semántico, previene roturas lógicas del reporte empresarial, protegiendo las medidas de rentabilidad.

---

##  5. Minirresumen de Limpieza de Datos (Fase ETL)

Resumen de las intervenciones de calidad para asegurar la integridad analítica de los cuatro datasets principales:

1. **Integridad de los registros (Duplicados):** Se testearon las *Primary Keys* en busca de clones. Exitosamente se purgaron **20 IDs de órdenes duplicadas** y **38 IDs de detalle de líneas** idénticas, previniendo reportar ingresos transaccionales fantasmas (Producto Cartesiano).
2. **Valores numéricos clave:** Precios y Subtotales financieros presentaban en ciertos casos entraños saldos negativos en el sistema origen; dichos números se transformaron matemáticamente en valores absolutos (`abs()`). Además, se realizó auditoría cuadrática calculando que la cantidad multiplicada por el precio coincida fielmente sin descuentos ocultos.
3. **Formulación, Formatos y Codificación:** Se forzó que todas las extracciones CSV estuvieran bajo la estructura `UTF-8` para la protección idiomática (ñ, acentos). Los Ids lógicos sufrieron recasting a `string` impidiendo cualquier función matemática equivocada.
4. **Separación de columnas combinadas:** La métrica `vendedor_region` concatenada fue separada (Split) en dos variables atómicas para forzar la Primera Forma Normal (1NF) y responder a *Quién vende y Dónde* de manera aislada.
5. **Normalización por variantes de texto:** Variables categóricas de llenado manual (Canal de Venta, Terminales, Marcas, Turnos, Categorías) presentaban falta de homogeneidad. Se capitalizaron (`Title_Case`), limpiando espacios extra para centralizar bajo segmentaciones fiables.
6. **Estrategia para valores faltantes (Imputación):** Frente al vacío en precios y márgenes críticos de productos, la regla procedió rellenando lógicamente precios partiendo de: `Costo * (1+Margen)`; y si faltaba el margen, se recalculó: `(Precio - Costo)/Costo`.
7. **Inconsistencias y Outliers del margen:** Se corrigió a aquellos márgenes que rebasaban el 100% atípicamente (inputs de >1), dividiéndolos por cien (`/100`) para nivelar la escala porcentual.
8. **Tasa impositiva:** Mediante Feature Engineering sobre la variable `iva`, se crearon dos nuevas columnas fiscales esenciales: `monto_total_con_iva` y `monto_impuesto`.
9. **Relevancia del Analítico (Tablas Esbeltas):** Columnas puramente de logueo técnico y remanentes pre-split fueron *droppeadas* dejando ejes descriptivos puros (Dim) y económicos (Fact).

---
## 6. Documentación Detallada por Notebook

A continuación, se detalla la intervención técnica y estratégica sobre cada paso del pipeline.

### 📝 Archivo: `Limpieza_Clientes.ipynb`
- **Objetivo Principal:** Limpiar y estandarizar la dimensión de clientes para su posterior segmentación y análisis de retención, asegurando la calidad estructural de los datos.
- **Qué se hizo:**
  - Conversión del campo `fecha_alta` a formato `datetime` controlando posibles variaciones mixtas.
  - Casteo del `id_cliente` a un formato de cadena de texto (`string`/`object`).
  - Casteo de las variables `provincia` y `segmento` a tipo `category`.
  - Chequeo forense de valores nulos y duplicados (exactos y por ID).
  - Verificación final de esquemas de datos `dtypes`.
  - Exportación del DataFrame final hacia `Processed/clientes_procesado.csv`.
- **Decisiones Técnicas y de Negocio (El Por Qué):**
  - **ID como objeto:** Los IDs de los clientes fueron alterados intencionalmente a texto puro porque son identificadores nominales. Impedir que sean números evita que motores analíticos intenten operar con ellos matemáticamente (como sumar IDs o sacar un promedio de código de clientes).
  - **Eficiencia en memoria (Categorías):** Castear las provincias y segmentos a variables de tipo `category` ahorra drásticamente el uso de RAM y agiliza los cruces lógicos posteriores (`GROUP BY`, `JOIN`), pues el motor mapea internamente enteros en lugar de objetos de caracteres extensos.
  - **Estandarización temporal:** Forzar el parsing de fechas bajo el parámetro estructural `dayfirst=True` resuelve incidencias donde la carga transaccional mezcla convenciones europeas o latinoamericanas previniendo que clientes dados de alta en abril aparezcan erróneamente en el sistema contable pero de otros meses.

### 📝 Archivo: `Limpieza_Productos.ipynb`
- **Objetivo Principal:** Refactorizar y purgar el catálogo transaccional (Dimension Table), unificando categorías, reparando inconsistencias de registros y restaurando la veracidad de los precios de lista sin sacrificar amplitud del stock.
- **Qué se hizo:**
  - Tratamiento de identificadores a formato `string` text para conservación relacional.
  - Remoción de símbolos monetarios (`$`), comas e indicativos de medidas (`kg`) de columnas como `costo_unitario` y `peso_kg` con posterior casteo a flotante numérico.
  - Limpieza algorítmica y unificación de formatos de porcentajes en el `margen_ganancia`.
  - Estandarización de nomenclaturas a través de normalización y capitalización (.title()) en marcas y categorías.
  - Reglas de agrupación (`hardcoding` controlado) vía Diccionario Maestro para mapeo correcto de líneas en `marca` y `categoria`.
  - Imputación de registros nulos en `precio_lista` mediante matemática calculada en caliente.
  - Eliminación destructiva de productos redundantes que compartían el mismo código interno.
  - Imputación de vacíos (NaN) en `margen_ganancia` a partir del costo y el precio existente.
  - Detección y normalización temporal de Outliers en márgenes superiores al 100% (> 1), dividiéndolos por 100 y recalculando el precio subsidiario.
  - Exportación de los datos a `Processed/productos_procesado.csv`.
- **Decisiones Técnicas y de Negocio (El Por Qué):**
  - **Corrección Jerárquica vía Diccionario Maestro:** Se detectó suciedad relacional entre las marcas y categorías. Utilizar dependencias fuertes a través del Diccionario corrige proactivamente que ciertos productos figuran erróneamente adscritos, garantizando que cuando se reporten "Ventas por Categorías" para tomadores de decisión, el margen atribuido sea impecable.
  - **Imputación Recursiva de Precios y Márgenes:** Los valores en blanco ni en metas ni precios fueron removidos para no causar reducciones artificiales de catálogo. Si faltaba el precio, calculamos `Costo * (1 + Margen)`. Y más importante: si faltaba el margen, calculamos lógicamente `(Precio - Costo)/Costo`. Esto recupera productos esenciales para la operatividad.
  - **Corrección Lógica de Outliers de Margen:** Encontramos productos con márgenes anómalos (>1, o mayores a 100%, típicamente un error humano al tipear "25" en vez de "25%" o "0.25"). Ajustar el valor de vuelta a un formato porcentual real y recalcular el precio del catálogo sanea distorsiones críticas en la proyección de ganancias del negocio.
  - **Resolución de Códigos Internos Duplicados:** Cuando hay colisiones en los SKU (Stock Keeping Units), mantener la dualidad descompone sistemas logísticos. Se priorizó el primer registro histórico existente asegurando una entidad única indisputable para cada producto.

### 📝 Archivo: `Limpieza_Ventas_Order.ipynb`
- **Objetivo Principal:** Normalizar estructuralmente la tabla de cabeceras de ventas (Order Header), asegurando el respeto estricto de la "1ra Forma Normal" y mitigación absoluta de riesgos de sobre-facturación por incidencias transaccionales.
- **Qué se hizo:**
  - Reconversión de Foreign y Primary Keys (`id_orden`, `id_cliente`) a texto puro.
  - Detección exhaustiva y purgado de 20 IDs de órdenes duplicadas priorizando el registro pionero.
  - Corrección de acentuaciones anómalas ("é"), eliminación de signos de puntuación extraños en `canal_venta` y normalización visual del vocabulario.
  - Desacoplamiento (Split dimensional) de la columna concatenada `vendedor_region` en campos atómicos: `vendedor` y `region_venta`.
  - Formateo del historial de sesiones (de SESSION a SES) y uppercase terminales informáticas (`id_terminal`).
  - Parseo del timestamp lógico `fecha_orden`.
  - Despliegue hacia `Processed/ventas_order_procesado.csv`.
- **Decisiones Técnicas y de Negocio (El Por Qué):**
  - **Purgado de Transacciones Duplicadas:** Suprimir las cabeceras duplicadas (manteniendo la versión original) es mandatorio. Si no se extrajeran, a la hora de relacionarlo con las líneas detalladas de esa venta se formaría un "Producto Cartesiano" que multiplicaría exponencialmente los tickets emitidos, falseando fraudulentamente el Revenue del Retail.
  - **Atomicidad Textual (Separar Vendedor y Región):** Es un pilar arquitectónico no mantener atributos concatenados. Divorciar al vendedor del territorio, garantiza aislar los rendimientos; es decir, habilita los cruces y la capacidad de responder a gerencia incógnitas tales como "¿Qué región factura más limitando sesgos de qué vendedores están asignados ahí?".
  - **Unificación de Metadatos y Sistemas de Log:** Transformar terminales o sesiones (prefijos y capitalizaciones) mitiga drásticamente roturas si luego el equipo de ciberseguridad o auditoría necesita cruzar los cierres de caja contra bases de datos más rígidas y log files.

### 📝 Archivo: `Limpieza_Ventas_Order_Line.ipynb`
- **Objetivo Principal:** Depurar la "Fact Table" de detalles de órdenes, priorizando como absoluto rigor metodológico la perfección fiscal y la auditoría de cada peso transicionado en el sistema.
- **Qué se hizo:**
  - Conversión del bloque entero de llaves dependientes (`id_linea`, `id_orden`, `id_producto`) bajo matriz de tipo `string`.
  - Escaneo e higienización de 38 filas de facturación estrictamente clonadas.
  - Transformación financiera a Valor Absoluto (`abs()`) de las métricas `precio_unitario_s_iva` y `monto_linea`.
  - Evaluación forense y cuadratura contable validando que `cantidad * precio_unitario_s_iva` fuera idéntico por celdura al subtotal arrojado.
  - Feature Engineering Tributario: Construcción sintética de los campos `monto_total_con_iva` y `monto_impuesto`.
  - Casteo final de tipos nativos (int64, float64, string) para todas las columnas de métrica pesada previo a exportación.
  - Exportación del compendio hacia `Processed/ventas_order_line_procesado.csv` (y backup `ventas.csv`).
- **Decisiones Técnicas y de Negocio (El Por Qué):**
  - **Transformación de Negativos a Valores Absolutos:** En retail, capturar métricas o subtotales negativos para una venta normal indica fallos y excepciones graves de tipeo desde la lectura del ticket (a menos que sean notas de crédito formales). Transformarlos a absolutos previene que la "facturación" y "subtotales" finales se resten equivocadamente al totalizar la performance mensual de un negocio.
  - **Purgado de Detalles Duplicados:** Remover líneas clonadas sobre id_linea purga específicamente clics dobles de carritos fallidos, retardos de red u otros bugs habituales en orígenes de venta. No eliminarlos infla directamente las cifras de negocio al cierre mes.
  - **Auditoría Matemática:** La comprobación entre cantidades netas y variables unitarias es la única forma confiable de garantizar que en el backend no existan descuentos informales arrastrados ocultamente en la venta.
  - **Ingeniería de Características Financieras (Impuestos generados):** La arquitectura previa mostraba el subtotal "Sin IVA". A nivel negocio, este enfoque sirve para que área de Ventas (Comercial) mida de forma neta el impacto. Sin embargo, no era admisible para Administración o Contaduría. Crear estas dos nuevas métricas satisface paralelamente las necesidades de control para la declaración ante agencias fiscales y la visualización de flujo de caja libre neto para el Retail.

## 7. Modelo de Datos (Relaciones, Cardinalidad y Filtro)
Para este caso, el modelo ideal diseñado es un **Esquema de Estrella Híbrido** (específicamente un modelo con dimensiones).

- **Tabla Dimensiones:** `Clientes` y `Productos` (y la tabla `Calendario`).
- **Tablas de Hechos (Fact Tables):** `Ventas_Order` (Cabecera) y `Ventas_Order_Line` (Detalle).

**Relaciones:**
- `Clientes[id_cliente]` **(1)** ➔ `Ventas_Order[id_cliente]` **(Varios / \*)**
- `Ventas_Order[id_orden]` **(1)** ➔ `Ventas_Order_Line[id_orden]` **(Varios / \*)**
- `Productos[id_producto]` **(1)** ➔ `Ventas_Order_Line[id_producto]` **(Varios / \*)**

**Justificación de Decisiones:**
- **Cardinalidad (1 a Varios):** Las tablas maestras (Clientes y Productos) tienen identificadores únicos sin duplicados. Las tablas de ventas registrarán estos IDs múltiples veces en el tiempo.
- **Dirección del Filtro (Única / Single):** Todo el filtrado fluye invariablemente desde las Dimensiones *(los catálogos)* hacia las tablas de Hechos *(las métricas)*. Bi-direccionalidad en este modelo no se habilita bajo ningún punto de vista, porque impactaría severamente el rendimiento (motor VertiPaq) y generaría ambigüedad de cálculo.

## 8. Dimensión de Columnas Calculadas (DAX)
Todas las columnas pesadas de cálculos analíticos se destinaron a dimensiones, esquivando gravar con memoria RAM artificial al módulo de Facturación:

**A. Aperturas Descriptivas de Calendario:** 
(Generadas vía `CALENDARAUTO()`). Aportan legibilidad amigable al gerente. Ej.: `Año`, `Trimestre`, `Trimestre Año`, `Mes Año`, `Mes Nombre`, `Día`, `Semana`.
**B. Auxiliares de Ordenamiento Natural:** 
Fungen como propiedades de *"Sort By Column"* silenciosas para que abril jamás aparezca antes que enero:
* `Orden Trimestre` = `YEAR(Calendario[Date]) * 10 + FORMAT(Calendario[Date], "Q")`
* `Orden Mensual` = `YEAR(Calendario[Date]) * 100 + MONTH(Calendario[Date])`
**C. Target de Rentabilidad (Productos):**
Calculado sobre `Productos` para segmentación táctica veloz: `1- Alta Rentabilidad` (>=45%), `2- Rentabilidad Media` (>=25%), o `3- Baja Rentabilidad (Traffic Builder)`.

## 9. Catálogo de Métricas y KPIs Centrales (Measure Table)
La totalidad de las lógicas financieras se unificaron en una carpeta flotante (`_Medidas`), ocultando al usuario los campos numéricos brutos:

**Rendimiento Comercial Base:**
* **`Ventas_totales`:** Base monetaria cruda (`= SUM(ventas_order_line_procesado[monto_linea])`).
* **`Cantidad_Transacciones`:** Flujo operativo evaluado por volumen de tickets (`= COUNTROWS(ventas_order_procesado)`).
* **`Ticket_Promedio`:** Valor recabado por orden general (`= DIVIDE([Ventas_totales], [Cantidad_Transacciones], 0)`).

**Rentabilidad & Profiling:**
* **`Ganancia_Total`:** Iteración (`SUMX`) del volumen facturado purgado de sus costos de inventario (`RELATED(costo_unitario)`), revelando la recaudación líquida de la compañía.
* **`Margen_%_Real`:** Proporción combinada ("Blended") entre Ganancia Total y Ventas Totales.
* **`Ranking_Producto_en_Categoria`:** Posicionamiento ponderado (`RANKX`) de cada ítem limitándolo exclusivamente a sus "pares" de categoría igual (`ALLEXCEPT`).

**Time Intelligence & Self-Service UI:**
* **`Ventas_LY` & `Variacion_YoY %`:** Contexto del año pasado con formato uniendo caracteres ASCII geométricos (▲/▼) para una rápida lectura en variaciones. Soporte ciego de color para las vistas (*Format YoY %* = "Green" / "Red").
* **`Metrica_Dinámica` & `Filtro Ventana Dinámico`:** Motores de los SLICERS UI que permiten cambiar por botón si el gráfico muestra "Ventas", "Ganancias", o acorta temporalmente el eje viendo solos los "Últimos 90 o 180 días" bloqueando la sobresaturación analítica.

**Caso de Uso Particular: Visual Medidor (Gauge) de `Margen Real %`:**
* *Propósito:* Proporcionar contexto de cuán cerca o lejos estamos de la rentabilidad deseada en un solo vistazo.
* *Lógica:* Se estableció como *objetivo predefinido* un umbral del **20%**, estimado como "Punto de Equilibrio Optimizado" requerido para sufragar costos operativos fijos y reinversión paralela. Agujas posadas en franja verde (>16%) expresan la latencia a cruzar la menta (El valor del 20% se estima a partir de una pequeña investigacion de mercado, idealmente se requiere consultar a un experto en finanzas o dueño del negocio para obtener un valor mas preciso).
* *Diseño Cromático:* El uso del verde neón sobre fondo oscuro minimiza la carga cognitiva y enfatiza el carácter positivo del dato actual (16.4%). La aguja en verde indica una zona de "Salud Financiera", mientras que el espacio restante del arco permite visualizar el potencial de crecimiento hasta la meta del 20%.
* *Decisión Técnica:* Se utilizó la función DIVIDE en DAX para garantizar el manejo de errores ante posibles valores nulos en ventas, evitando divisiones por cero que comprometan la integridad del reporte.

---
## 10. User Experience: Panel de Campos Limpio
Para hacer que el informe sea "Business-Ready", los campos complejos de Base de Datos relacional quedan fuera de la óptica del consumidor ocultándose (`Hide from report view`):

1. **Ocultar Todas las Foreign y Primary Keys (FK/PK):** (p.ej. `Ventas_Order[id_cliente]`). Los usuarios nunca deben usarlas para filtrar reportes estadísticos ni de transacciones, ya que deben apelar directamente a la meta-información del `Nombre de Cliente`.
2. **Ocultar Fechas de Fact Tables:** Todo el filtrado transaccional se redirige operativamente a la tabla maestra `Calendario`.
3. **Ocultar Columnas Numéricas Simples (Tras la inclusión de medidas):** Muestre únicamente `Total Ingresos` en lugar de arriesgar agrupamientos erróneos de negocio derivativos de `monto_total_con_iva`.
4. **Ocultar columnas de caracteristicas de productos:** (p.ej. `Productos[cod_interno], Productos[peso_kg], Productos[precio_lista], Productos[stock_actual]`). Son columnas que no se usan, es una mala practica eliminar información que en un futuro puede ser utilizada.

## 11. Insights del Negocio y Hallazgos Analíticos

Al someter esta data pre-limpiada ante el modelo analítico formulado en la capa de Power BI, salieron a flote importantes tendencias que posibilitan la toma de decisión basada fuertemente en datos:

### Tendencia Anual y Estacionalidad
* **Crecimiento e Histórico:** El año 2024 finalizó como tope de ventas, rebotando en un **17% de mejora** positiva con respecto a 2023 (año que a su vez cargaba con el arrastre de una aguda caída del 9.2% originada pos-2022). 
* **Costes vs Interacciones:** Sorprendentemente, el año 2023 albergó **4 transacciones volumétricas más** que 2024; sin embargo, en 2024 el *Ticket Promedio* se disparó hasta **$148.000**. Esto demanda investigación de urgencia: *¿La base está perdiendo sus clientes pero los pocos que compran traccionan más por inflación/precios, o mutamos de "Venta barata por volumen" hacia una "Microventa Boutique Premium"?*
* **Patrón de Estacionalidad:** Febrero, Junio y Agosto dictaminen los techos en facturación y se coronan como los tres meses de mayor recaudación interanual. Resulta inversamente crítico identificar que sistemáticamente —durante el mes de Abril de cada año censado (2022, 2023, 2024)— se registra **una depresión drástica de los ingresos**. Obligatorio estudiar mermas por factor de fin temporadas.

### Portafolio, Clientela y Logística
* **Liderazgo de Segmentos:** El estrato de venta *"Básico"* arrasa siendo el bloque de mayor generación de caja global y volumen transaccional; paradójicamente su rentabilidad neta (Margen porcentual de ganancia) es la **tercera peor del grupo**. Inversamente, el segmento *"Premium"*, en segunda plaza financiera, asume el reinado consolidando el **Mejor Margen Real** de contribución al negocio.
* **Fidelización y Canales:** El **ID Cliente "063"** se ubica como el máximo tomador serial del catálogo. Urge un programa "VIP/Rewards" para asegurar su anclaje o convertirlo en embajador de marca (*Key Account*). Por formato tecnológico, reinan casi por monopolio la tracción Online y Presencial (acumulan juntas el **77% total** de operaciones).

### Producto y Ganancias Críticas
* **Contradicción al Volumen:** La *"Mochila 25L"* aplasta siendo el ente más despachado visualmente; no obstante, su métrica financiera duele: posee el **tercer margen más bajo** (1.5% de ganancia de retribución). 
* **Joyas Ocultas:** El estandarte principal de recaudación lo carga la *"Campera Impermeable"* (Producto top en ganancia neta absoluta), ranqueado segundamente en masividad. A la par, las *"Galletitas Integrales"*, si bien flotan silenciosas en el décimo lugar de volumen de canastos, detentan un potente ratio líquido del **13.5%** de contribución.
* **Zonas de Pérdidas Ocultas y Outliers Inferiores:** El elemento más relegado en tracción general empírica es la *"Tablet 10 Pulgadas"* paralizada en inventario. Se levanta la alarma para la *"Remera Deportiva"* que actualmente registra un **Margen de Ganancia Negativo** de cara al reporte. ¿Estrategia corporativa comercial preestablecida ("Loss Leader" o "Producto Gancho") o gravísimos errores de Costos de Fabricación mal imputados al ERP?

### Operatividad Diaria (Cajas y Metodología)
* **Comportamiento por Turno:** La campana indica que lo más lucrativo es la *Tarde*, escoltada por la *Noche* y finalmente hundido el horario de la *Mañana*. 
* Paradójicamente, el **Ticket Recaudador Máximo Promedio emerge de la Noche**, anclado directamente a que el pico de gasto nocturno converge mediante **Tarjeta de Crédito**, el medio pospago habilitador de mayores gastos.
* **Metodología y Geolocalización:** La liquidez y circulación prefieren abrumadoramente el *Efectivo*, luego rondan Tarjeta de Crédito, Transferencia Bancaria y Cheque. Territorialmente *Buenos Aires* capta el máximo núcleo de flujo monetario en tanto *Rosario* representa el territorio logístico de peor rentabilidad de mercado comparada.

## 12. Diseño UI/UX y Paleta de Colores

**Paleta de Colores Exclusiva:**

| Elemento | Nombre / Tono | Código Hexadecimal |
|:---|:---|:---|
| **Fondo Principal** | Deep Space Navy (Azul noche profundo) | `#14161F` |
| **Contenedores/Cards** | Slate Blue Dark (Azul pizarra) | `#222533` |
| **Color de Éxito/Activo** | Electric Lime (Verde neón) | `#39FF14` / `#2ECC71` |
| **Segmento Medio** | Emerald Green (Verde esmeralda) | `#27AE60` |
| **Segmento Bajo** | Forest Shadow (Verde bosque oscuro) | `#1E5631` |
| **Texto de Datos** | Pure White (Blanco puro) | `#FFFFFF` |
| **Labels y Ejes** | Cool Gray (Gris azulado suave) | `#A0AEC0` |

### Justificación del Diseño Visual

1. **Jerarquía Visual y Ratio de Contraste**
   En un entorno oscuro, el ojo humano se dirige naturalmente hacia el punto con mayor luminancia. Al usar el verde más brillante para las Ventas Totales y la Línea de Evolución, estás forzando al usuario a ver primero lo más importante. La escala de verdes en el Treemap (Ventas por segmento) permite entender el volumen sin necesidad de leer etiquetas inmediatamente.

2. **Ergonomía Visual**
   El uso del azul oscuro (`#14161F`) en lugar de negro puro evita el efecto de "sangrado" o "halación" del texto blanco. Esto permite que el usuario pase más tiempo analizando el dashboard sin sufrir fatiga ocular, algo crítico en roles de toma de decisiones.

3. **Psicología del Color en el Sector Fintech/Datos**
   * **Azul Profundo:** Transmite autoridad, estabilidad y seguridad (ideal para una consultora de datos).
   * **Verde:** Asociado universalmente con el crecimiento, la rentabilidad y el "go" (adelante).
   * **El punto rojo de inflexión:** Al ser el único color cálido en todo el reporte, el "alerta" de Octubre-24 es imposible de ignorar. Es un uso magistral de la disonancia cromática para marcar un outlier.

4. **Consistencia de Marca (Branding)**
   La alineación con los colores de Sudata no es solo estética, es estratégica. Refuerza la pertenencia del producto a la consultora, creando una experiencia de marca unificada que genera confianza en el cliente final.

> **Nota:** Se eligió un diseño de **Minimalismo Cognitivo**, el cual consiste en no saturar al usuario con millones de colores, lo que permite una toma de decisiones más rápida.

---

## 13. Tipografías 

* **Valores:** `Segoe UI Semibold` (Tamaño 9)
* **Encabezados de tablas:** `Segoe UI Semibold` (Tamaño 10)
* **Ejes X e Y:** `Segoe UI Semibold` (Tamaño 10)
* **Títulos:** `Inter Semibold` (Tamaño 22 de FIGMA)

---

## 14. Guía para el uso de filtros (Línea Temporal)

**Mini-guía: ¿Cómo entender este control?**

> 📸 *Imagina que tu gráfico es una cámara de fotos. Estos dos controles manejan el zoom y la resolución.*

**A. La Ventana (El Zoom)**
Define _"¿Cuánto tiempo hacia atrás quiero ver?"_.
*   Si eliges **`1M`**, la cámara se acerca y solo te muestra los últimos 30 días.
*   Si eliges **`1A`**, la cámara se aleja y te muestra todo el año completo.
> **Dato clave:** Este control filtra los datos para que no veas una línea eterna e ininteligible.

**B. La Apertura (La Resolución)**
Define _"¿Cuántos puntos quiero en mi línea?"_.
*   **Diario:** Verás un punto por cada día (ideal para detectar caídas rápidas).
*   **Mensual:** Verás un punto que resume todo el mes (ideal para ver la tendencia a largo plazo sin "ruido").