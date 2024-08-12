# Laboratoria - Proyecto 3 

# RIESGO RELATIVO: Institución financiera "Súper Caja" :bank:

El riesgo relativo es una medida estadística utilizada para evaluar la asociación entre la exposición a un factor de riesgo específico y la aparición de un resultado particular, como una enfermedad o un evento adverso. Se calcula como la proporción de la incidencia de dicho resultado en el grupo expuesto al factor de riesgo en comparación con la incidencia en el grupo no expuesto, es decir, proporciona una indicación de cuánto más probable es que ocurra un resultado en el grupo expuesto en comparación con el grupo no expuesto.

Un riesgo relativo igual a 1 sugiere que no hay diferencia en la incidencia entre los dos grupos, mientras que un riesgo relativo mayor que 1 indica un mayor riesgo en el grupo expuesto, y un riesgo relativo menor que 1 indica un menor riesgo en el grupo expuesto.


# Temas :bulb:

- [Introducción](#Introducción)
- [Objetivo](#Objetivo)
- [Procesamiento y análisis](#Procesamiento-y-análisis)
- [Herramientas](#Herramientas)
- [Lenguajes](#Lenguajes)
- [Ficha técnica](/Ficha_técnica/README.md)
- [Jupyter Notebook](/Jupyter_Notebook/README.md)
- [Dashboard](/Dashboard/README.md)
- [Presentación](/Presentación/README.md)


## Introducción :gear:

La disminución de las tasas de interés en el mercado ha desencadenado un aumento notable en la demanda de solicitudes de crédito. Los clientes ven una oportunidad favorable para financiar compras importantes o consolidar deudas existentes, lo que ha llevado a una afluencia de solicitudes de préstamo en el banco.

El equipo de análisis de crédito del banco está enfrentando una carga de trabajo abrumadora debido al análisis manual requerido para cada solicitud de préstamo de clientes individuales. Esta metodología manual ha resultado en un proceso ineficiente y demorado, lo que ha afectado negativamente la eficacia y la rapidez con la que se procesan las solicitudes de préstamo. La situación se vuelve más crítica debido a la creciente preocupación por la tasa de incumplimiento (default), un problema que está afectando cada vez más a la industria financiera, aumentando la presión sobre los bancos para identificar y mitigar los riesgos asociados con el crédito.

## Objetivo :dart:

El objetivo del análisis es armar un score crediticio a partir de un análisis de datos y la evaluación del riesgo relativo que pueda clasificar a los solicitantes en diferentes categorías de riesgo basadas en su probabilidad de incumplimiento. Esta clasificación permitirá al banco tomar decisiones informadas sobre a quién otorgar el crédito, reduciendo así el riesgo de préstamos no reembolsables. 

## Procesamiento y análisis :bar_chart:

__1. Conectar/importar datos a otras herramientas__
* Se creó el proyecto3-riesgo-relativo-lab y el conjunto de datos Dataset en BigQuery.
* Tablas importadas: user_info, loans_outstanding, loans_details y default.

__2. Identificar y manejar valores nulos__

* Se identifican valores nulos a través de comandos SQL COUNT, WHERE y IS NULL.
* __user_info__: 7199 valores nulos en la columna last_month_salary y number_dependents.
* Unión de la base de datos user_info y default usando LEFT JOIN: se encontró que de 7199 valores nulos, 7069 (19.64%) clientes son buenos pagadores y 130 (0.36%) son malos pagadores. Los datos nulos (7199) representan el 20% del total (36,000). 
* Con los comandos AVG, WHERE y GROUP BY, se calculó el promedio a la variable last_month_salary para cada categoría de cliente (buen pagador/mal pagador), sin considerar datos outliers, es decir, salarios mayores a 400,000.
* Con los comandos IFNULL, CASE, WHEN, THEN, ELSE, se IMPUTARON los valores nulos de la variable last_month_salary colocando el promedio por categoría.
* Con los comandos WITH, RANK se calculó la moda para la variable number_dependents para cada categoría de cliente (buen pagador/mal pagador).
* Con los comandos IFNULL, CASE, WHEN, THEN, ELSE, se IMPUTARON los valores nulos de la variable number_dependents colocando la moda por categoría.
* __loans_outstanding__: 0 valores nulos.
* __loans_details__: 0 valores nulos.
* __default__: 0 valores nulos.

__3. Identificar y manejar valores duplicados__
* Se identifican duplicados a través de comandos SQL COUNT, GROUP BY, HAVING.
* __user_info:__ no hay valores duplicados.
* __loans_outstanding:__ no hay valores duplicados.
* __loans_details:__ no hay valores duplicados.
* __default:__ no hay valores duplicados.

__4. Identificar y manejar datos fuera del alcance del análisis__
* Se manejan variables que no son útiles para el análisis a través de comandos SQL SELECT EXCEPT.
* Se excluye la variable sex de la tabla user_info.
* Con el comando CORR y STDDEV, se calcula la correlación y la desviación estándar entre las variables more_90_days_overdue y number_times_delayed_payment_loan_30_59_days, y more_90_days_overdue y number_times_delayed_payment_loan_60_89_days. Se identifican las variables con alta correlación.
* more_90_days_overdue y number_times_delayed_payment_loan_60_89_days tienen la correlación más alta con 0.9921.
* number_times_delayed_payment_loan_60_89_days tiene la desviación estándar más baja 4.1055.
* Para el análisis y limpieza se utiliza la variable more_90_days_overdue.

__5. Identificar y manejar datos inconsistentes en variables categóricas__
* Con los comandos DISTINCT, COUNT, CASE, WHEN, THEN, ELSE, LOWER se estandarizaron los datos de la variable loan_type.

__6. Identificar y manejar datos inconsistentes en variables numéricas__
* Con los comandos WITH, APPROX_QUANTILES, CASE, WHEN, ELSE, WHERE, se identifican los datos outliers de las tablas user_info y de loans_detail. Se utilizó la metodología de rango intercuartil.
* Se realizaron box plots e histogramas interactivos en google colab usando python para visualizar mejor los resultados encontrados, adicional se hicieron nuevas consultas para encontrar los valores más extremos un top 30 y top 70.

__7. Crear nuevas variables__
* Con los comandos DISTINCT, SUM, CASE, WHEN, GROUP BY, se hizo una tabla agrupada por usuario, con una fila para cada cliente, mostrando el tipo de préstamo y la cantidad total.

__8. Unir tablas__
* Con el comando INNER JOIN se unieron las vistas user_default_limpia, loans_out_totales, loans_detail_limpia, creando una tabla consolidada con las variables limpias join_user_out_detail_limpia.

__9. Agrupar datos según variables categóricas__
* Se conectaron los datos a looker studio desde BigQuery.
* Se creó un campo calculado en looker studio para crear una clasificación de datos por rango de edad, utilizando los comandos CASE, WHEN, THEN y de está manera hacer un análisis exploratorio.
* Se creó un grupo categoría de pago para buen pagador y mal pagador de acuerdo a las flags 0 y 1.

__10. Visualizar las variables categóricas__
* Se realizaron gráficos de barras para la visualización de variables y exploración de datos en looker studio.

__11. Aplicar medidas de tendencia central y aplicar medidas de dispersión__
* Se crearon tablas en looker studio con las medidas de tendencia central (mediana, promedio) para comparar los datos por rango de edad y por categoría de pago.
* Se crearon tablas en looker studio con las medidas de dispersión (rango, desviación estándar) para comparar los datos por rango de edad y por categoría de pago.

__12. Visualizar distribución__
* Se crearon box plot para visualizar la distribución de las variables por rango de edad y categoría de pago en looker studio.
* Se realizaron box plot e histogramas para las variables en google colab usando python.

__13. Aplicar correlación entre las variables numéricas__
* Se creó una matriz de correlación de todas las variables en google colab utilizando Python.
* Con el comando CORR se calculó la correlación entre variables en BigQuery.

__14. Calcular cuartiles, deciles o percentiles__
* Con los comandos WITH, NTILE, COUNT, GROUP BY, MIN, MAX, JOIN, se calcularon los cuartiles de cada variable, se contabilizó el número de usuarios por cuartil, el total de malos pagadores y se calculó el rango de cada cuartil. 

__15. Calcular riesgo relativo__
* Con los comando WITH, NTILE, COUNT, MIN, MAX, CASE, WHEN, LEFT JOIN, se calculó el riesgo relativo en BigQuery para las variables, obteniendo una tabla con los cuartiles, total de usuarios, total de malos y buenos pagadores, riesgo relativo, y el rango de los cuartiles.

Este proceso es fundamental para asegurar la calidad y precisión del análisis subsiguiente. 

Para ver más detalles sobre el hito 1, hito 2 e hito 3, gráficos, resultados, tablas, decisiones, y conclusiones consultar la ficha técnica y las consultas de BigQuery.

## Herramientas :hammer_and_wrench:

* Google BigQuery
* Google Looker Studio
* Google Docs
* Google Slide
* Jupyter Notebook

## Lenguajes :pencil2:

* SQL
* Python
