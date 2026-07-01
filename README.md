## Datos utilizados

El análisis parte de dos conjuntos de datos:

- `Customer Flight Activity`: contiene información mensual sobre la actividad de los clientes, como vuelos reservados, vuelos con acompañantes, distancia recorrida, puntos acumulados y puntos redimidos.
- `Customer Loyalty History`: recoge información general de los clientes del programa de fidelización, como género, nivel educativo, estado civil, salario, provincia, tipo de tarjeta y fechas de alta o cancelación.



## Estructura de los archivos

Este repositorio está organizado de la siguiente forma:

- En la carpeta 'files' se encuentran:
    - los csv originales ('Customer Flight Activity' y 'Customer Loyalty History'), materia prima del análisis,
    - El csv 'df_final_limpio', que es el csv con los datos limpios, transfromados y unificados de los csv originales.
    - El csv 'df_clientes', con los datos limpios y transformados solo de los clientes, para facilitar el análisis.

- Y la carpeta 'Analisis' está estructurada en 3 documentos:
    1. El documento 01_EDA_limpieza_transformacion.ipynb, donde se ha realizado todo el proceso de exploración, limpieza y transformación.
    2. El documento 02_analisis, donde se ha desarrollado todo el código y las visualizaciones para la realización del análisis
    3. El documento 03_informe_resultados, con los gráficos y las explicaciones más relevantes y ordenadas, desarrollando las conclusiones del análisis




### Limpieza y preparación de los datos

Durante la fase de limpieza se trabajó con dos tablas principales: una tabla de actividad de los clientes (`df_activity`) y una tabla con información general del programa de fidelización (`df_loyalty`).

En primer lugar, en la tabla `df_activity` se unificaron las columnas correspondientes al año y al mes para crear una nueva columna de fecha. Dado que la información disponible solo recogía año y mes, se asignó el primer día de cada mes como referencia técnica para construir una fecha válida en formato `datetime`.

Posteriormente, se eliminó la columna `month`, al considerar que la nueva columna de fecha ya recogía esa información de forma más completa. Sin embargo, se mantuvo la columna `year`, ya que puede resultar útil para realizar agrupaciones, filtros o comparaciones anuales de manera más directa durante el análisis.

También se detectaron 228.705 filas en la tabla de actividad en las que todas las variables relacionadas con vuelos, distancia y puntos tenían valor 0. Estos registros no se eliminaron, ya que no se interpretan como valores nulos, sino como meses en los que no hubo actividad registrada por parte del cliente. Mantenerlos permite analizar la inactividad dentro del programa de fidelización y evita perder información relevante sobre el comportamiento mensual de los usuarios.

En la tabla `df_loyalty`, las columnas `cancellation_year` y `cancellation_month` se transformaron a formato entero, ya que inicialmente estaban en formato decimal. Posteriormente, se utilizaron para construir una columna de fecha de cancelación. Una vez creada esta nueva variable, se eliminaron las columnas auxiliares de mes utilizadas para construir las fechas, como `enrollment_month` y `cancellation_month`, con el objetivo de evitar redundancias en el conjunto de datos.

Antes de unir ambas tablas, se comprobó que no existieran duplicados en la tabla `df_loyalty` para el identificador de cliente (`loyalty_number`). Esta comprobación era importante porque la tabla de actividad contiene varias filas por cliente, una por cada mes registrado, mientras que la tabla de fidelización contiene información única de cada cliente. Una vez verificado esto, se realizó la unión de ambas tablas mediante el identificador `loyalty_number`, conservando todos los registros de la tabla de actividad.

En cuanto a la gestión de valores nulos, se identificaron principalmente en la fecha de cancelación y en la variable `salary`. Los nulos de la fecha de cancelación se mantuvieron, ya que se interpretan como clientes que no han cancelado su pertenencia al programa de fidelización. Por tanto, en este caso el valor nulo no representa necesariamente ausencia de información, sino una condición relevante del cliente.

La variable `salary` presentaba valores nulos y algunos valores negativos, que no resultan coherentes con el significado de la variable. Por este motivo, los salarios negativos se transformaron previamente en valores nulos para tratarlos como datos ausentes.

Posteriormente, se aplicó `IterativeImputer`, siguiendo la metodología trabajada en clase, para imputar los valores ausentes de la variable `salary`. No obstante, dado que la imputación se realizó únicamente sobre esta variable, el algoritmo no disponía de otras variables predictoras con las que estimar el salario de forma más individualizada. Como consecuencia, se observa una concentración de los valores imputados en torno a la zona central de la distribución.

Esta limitación se tiene en cuenta en la interpretación posterior, ya que el algoritmo no dispone de otras variables numéricas relevantes para estimar el salario con mayor variabilidad individual.

### Preparación de tablas para el análisis

Una vez realizada la unión de las tablas, se creó una tabla específica a nivel cliente (`df_clientes`) para analizar aquellas variables que describen características individuales de los usuarios, como `gender`, `education`, `marital_status`, `loyalty_card`, `enrollment_type`, `province` o `salary`.

Esta decisión se tomó porque la tabla final contiene información a nivel cliente-mes, es decir, un mismo cliente puede aparecer varias veces, una por cada mes registrado. Por tanto, analizar variables propias del cliente directamente sobre la tabla mensual podría duplicar registros y distorsionar la distribución de estas variables.

De este modo, las variables categóricas se analizaron desde `df_clientes`, evitando que los clientes con más meses registrados tuvieran más peso en el análisis. Para cada variable categórica se calcularon frecuencias absolutas y porcentajes, y se representaron mediante gráficos de barras para observar la distribución de clientes según género, nivel educativo, estado civil, tipo de tarjeta de fidelidad, tipo de inscripción y provincia.

### Análisis realizado

El análisis exploratorio se estructuró en varios bloques:

- análisis descriptivo de variables numéricas relacionadas con vuelos, distancia, puntos, salario y CLV;
- análisis de variables categóricas a nivel cliente;
- análisis de la distribución mensual de vuelos reservados para identificar posibles patrones estacionales;
- análisis de la relación entre distancia recorrida y puntos acumulados;
- análisis de la relación entre salario, CLV y vuelos reservados;
- comparación del número de vuelos reservados según el nivel educativo de los clientes.


### Conclusiones principales

El análisis muestra que la actividad mensual de los clientes está muy concentrada en valores bajos o nulos, lo que refleja la existencia de muchos meses sin vuelos registrados. Al mismo tiempo, una minoría de registros presenta niveles de actividad mucho más altos, lo que genera distribuciones asimétricas y una elevada dispersión en variables como vuelos reservados, distancia y puntos acumulados.

También se observa una relación positiva muy fuerte entre la distancia recorrida y los puntos acumulados, con una correlación cercana a 0,99. Esto indica que ambas variables evolucionan prácticamente en la misma dirección.

En cuanto al perfil de los clientes, la base está equilibrada por género, pero concentrada en determinados niveles educativos, tipos de tarjeta, modalidades de inscripción y provincias.

Respecto a las variables que podrían explicar una mayor reserva de vuelos, no se observa una relación clara entre salario y número de vuelos reservados. En cambio, la distribución mensual de reservas sí muestra un patrón estacional, con mayor actividad en los meses de verano y en diciembre.


### Limitaciones

La principal limitación del análisis está relacionada con la imputación de la variable `salary`. Al aplicar `IterativeImputer` únicamente sobre esta variable, los valores imputados se concentran en torno a un valor central, lo que puede reducir parcialmente la variabilidad real de los salarios.

Además, el análisis es descriptivo y exploratorio, por lo que las relaciones observadas no deben interpretarse como relaciones causales.