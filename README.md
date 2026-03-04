## Informe Final Detallado

### 1. Resumen Ejecutivo
El presente informe detalla el desarrollo y evaluación de modelos de Machine Learning diseñados para predecir la fuga de clientes en el sector de las telecomunicaciones. El objetivo principal es identificar proactivamente a los usuarios con alta probabilidad de cancelar su suscripción, permitiendo al negocio implementar estrategias de retención focalizadas y, de esta manera, minimizar la pérdida de ingresos y optimizar los esfuerzos de marketing.

### 2. Datos y Preprocesamiento
El análisis partió de un conjunto de datos (`datos_tratados.csv`) que requirió un preprocesamiento exhaustivo para preparar las variables para el modelado:

*   **Carga inicial de datos**: Se cargó el archivo `datos_tratados.csv` en un DataFrame de pandas.
*   **Codificación de variables binarias**: La columna 'gender' fue recodificada numéricamente para 'Male' como 0 y 'Female' como 1.
*   **Normalización de categorías**: 
    *   En la variable 'internet_service', el valor '0' se reemplazó por 'No' para una mejor interpretación.
    *   En 'online_backup', los valores '1' y '0' se mapearon a 'Si' y 'No' respectivamente.
*   **Manejo de variables categóricas**: Las variables 'internet_service', 'online_backup', 'contract' y 'payment_method' fueron transformadas utilizando One-Hot Encoding (`pd.get_dummies()`) para convertirlas en un formato numérico adecuado para los modelos de Machine Learning.
*   **Eliminación de columnas irrelevantes**: Se eliminaron columnas que no aportaban valor al modelo o tenían baja correlación con la variable objetivo 'churn', como 'customer_ID', 'gender', 'phone_service', y 'multiple_lines'.
*   **Gestión de valores nulos**: Se identificaron y eliminaron las filas con valores nulos, específicamente en la columna `charges_total`, asegurando la integridad de los datos para el entrenamiento.
*   **Desbalance de clases**: Se observó un desbalance significativo en la variable objetivo 'churn' (aproximadamente 73% no-churn y 27% churn), lo cual es crucial abordar para evitar que el modelo se incline a predecir la clase mayoritaria.

### 3. Metodología y Modelado
Para abordar el problema de clasificación y el desbalance de clases, se implementó una metodología robusta que incluyó los siguientes pasos:

*   **División de datos**: Se dividió el conjunto de datos en características (`X`) y la variable objetivo (`y`, 'churn').
*   **Algoritmos de Clasificación**: Se evaluaron los siguientes modelos:
    *   **DummyClassifier**: Como modelo base para establecer un punto de referencia.
    *   **RandomForestClassifier**: Un potente algoritmo de ensemble conocido por su buen rendimiento.
    *   **KNeighborsClassifier**: Un modelo basado en la distancia entre vecinos.
    *   **DecisionTreeClassifier**: Un modelo simple y fácil de interpretar.
*   **Validación Cruzada**: Se utilizó `StratifiedKFold` con `n_splits=5` y `random_state=5`. Esta técnica asegura que la proporción de las clases (churn vs. no-churn) se mantenga constante en cada fold de entrenamiento y prueba, proporcionando una evaluación más confiable del rendimiento del modelo.
*   **Pipelines con balanceo de clases**: Para manejar el desbalance de clases y prevenir el data leakage, se construyeron pipelines (`imbpipeline`) para cada modelo. Estos pipelines integraron el sobremuestreo `SMOTE` (Synthetic Minority Over-sampling Technique) para balancear la clase minoritaria (`churn`) únicamente en el conjunto de entrenamiento de cada fold antes de entrenar el clasificador. Esto garantiza que el balanceo no influya en la evaluación del modelo con datos no vistos.
*   **Control de aleatoriedad**: Se fijó una semilla (`random_state=5`) tanto en el particionamiento de datos como en SMOTE y los modelos que lo permiten, para asegurar la reproducibilidad de los resultados.

### 4. Métricas de Evaluación
La métrica principal seleccionada para evaluar el rendimiento de los modelos fue la **Sensibilidad (Recall)**.

*   **Justificación**: En el contexto de la retención de clientes, el costo de no detectar a un cliente que tiene alta probabilidad de abandonar el servicio (un Falso Negativo) es considerablemente mayor que el costo de contactar por error a un cliente que no planeaba irse (un Falso Positivo). Maximizar el Recall asegura que el modelo capture la mayor cantidad posible de clientes en riesgo real de fuga, lo que permite a la empresa intervenir a tiempo y aplicar estrategias de retención de manera efectiva.

### 5. Conclusiones

*   La integración de SMOTE en el pipeline de validación cruzada demostró ser una estrategia robusta para manejar el desbalance de clases, un desafío común en los conjuntos de datos de churn en telecomunicaciones.
*   Se comparó el rendimiento de los modelos utilizando el Recall promedio obtenido a través de la validación cruzada estratificada:
    *   **Árbol de Decisión**: 0.5212
    *   **K-Nearest Neighbors**: 0.6608
    *   **Random Forest**: 0.6758

* Basado en la comparativa de Recall, el modelo con la puntuación media más alta, RandomForest, es el recomendado para implementar en la detección de posibles clientes que vayan a dejar la compañia, ya que minimizará la pérdida de ingresos al alertar sobre la mayor cantidad posible de fugas.

*   **Recomendación**: Se recomienda implementar el modelo Random Forest para la detección de posibles clientes que vayan a dejar la compañía. Su rendimiento superior en Recall minimiza la pérdida de ingresos al alertar sobre la mayor cantidad posible de fugas, permitiendo así una intervención proactiva y efectiva en las estrategias de retención de clientes.
