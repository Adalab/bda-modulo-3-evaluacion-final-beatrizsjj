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

Además, para algunos análisis fue necesario agregar previamente la información mensual a nivel cliente. Por ejemplo, para estudiar si existían diferencias en el número de vuelos reservados según el nivel educativo, se calculó primero el total de vuelos reservados por cada cliente. Posteriormente, esta información se cruzó con la variable `education`, permitiendo comparar clientes únicos y no registros mensuales repetidos.

A partir de esta tabla agregada se calcularon estadísticas descriptivas por nivel educativo, como el número de clientes, la media, la mediana, la desviación estándar, el mínimo y el máximo de vuelos reservados. También se utilizaron gráficos de barras y boxplots para comparar tanto las medidas centrales como la dispersión de los vuelos reservados en cada grupo educativo.