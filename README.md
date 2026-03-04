# Análisis y Predicción de Churn en Telecomunicaciones

## 1. Introducción
Este proyecto tiene como objetivo desarrollar un modelo predictivo para identificar clientes propensos a la "fuga" o "churn" en una empresa de telecomunicaciones. La predicción temprana del churn permite a la empresa implementar estrategias de retención proactivas, minimizando así la pérdida de ingresos y optimizando los recursos de marketing.

## 2. Estructura del Proyecto
*   **`notebook.ipynb`**: Cuaderno principal de Google Colab que contiene todo el código para el preprocesamiento de datos, análisis exploratorio (EDA), modelado, evaluación y resultados.
*   **`datos_tratados.csv`**: Archivo CSV que contiene el conjunto de datos de clientes preprocesado, utilizado como base para el análisis.
*   **Visualizaciones**: Los gráficos y visualizaciones generadas durante el Análisis Exploratorio de Datos (EDA) y la evaluación de modelos están integrados directamente en el cuaderno.

## 3. Preparación de Datos
El conjunto de datos inicial requirió un preprocesamiento exhaustivo para ser apto para el modelado:
*   **Carga de Datos**: El análisis comenzó cargando el archivo `datos_tratados.csv`.
*   **Codificación de Variables Binarias**:
    *   La columna `gender` fue recodificada, asignando `0` a 'Male' y `1` a 'Female'.
    *   En `online_backup`, los valores numéricos '1' y '0' se mapearon a 'Si' y 'No' respectivamente, además de manejar 'No internet service'.
    *   En `internet_service`, el valor '0' fue reemplazado por 'No' para mayor claridad.
*   **One-Hot Encoding**: Se aplicó One-Hot Encoding a las variables categóricas nominales (`internet_service`, `online_backup`, `contract`, `payment_method`) utilizando `pd.get_dummies()` para convertirlas en un formato numérico adecuado para los algoritmos de Machine Learning.
*   **Eliminación de Columnas Irrelevantes**: Columnas como `customer_ID`, `gender`, `phone_service`, y `multiple_lines` fueron eliminadas debido a su baja correlación con la variable objetivo o por ser identificadores únicos sin valor predictivo directo.
*   **Manejo de Valores Nulos**: Se identificaron y eliminaron las filas con valores nulos en la columna `charges_total`, asegurando la integridad de los datos.
*   **Detección de Desbalance de Clases**: Se observó un desbalance significativo en la variable objetivo `churn` (aproximadamente 73% no-churn, 27% churn).

## 4. Metodología de Modelado
Para abordar el problema de clasificación y el desbalance de clases, se implementó la siguiente metodología:
*   **División de Datos**: El conjunto de datos preprocesado se dividió en características (`X`) y la variable objetivo (`y`, `churn`).
*   **Algoritmos de Clasificación Evaluados**:
    *   `DummyClassifier` (Modelo base para comparación).
    *   `RandomForestClassifier`.
    *   `KNeighborsClassifier`.
    *   `DecisionTreeClassifier`.
*   **Validación Cruzada Estratificada**: Se utilizó `StratifiedKFold` con `n_splits=5` y `random_state=5`. Esta técnica garantiza que la proporción de las clases (churn vs. no-churn) se mantenga consistente en cada fold de entrenamiento y prueba, proporcionando una evaluación más robusta del rendimiento del modelo.
*   **Balanceo de Clases y Pipelines**: Para manejar el desbalance y evitar la fuga de datos (data leakage), se construyeron `imbpipeline`s para cada modelo. Estos pipelines incorporaron `SMOTE` (Synthetic Minority Over-sampling Technique) para sobremuestrear la clase minoritaria (`churn`) exclusivamente en los conjuntos de entrenamiento de cada fold antes del entrenamiento del clasificador.
*   **Reproducibilidad**: Se fijó `random_state=5` en las divisiones de datos, SMOTE y los modelos que lo permiten, para asegurar la reproducibilidad de los resultados.

## 5. Métricas de Evaluación
La métrica principal para evaluar el rendimiento de los modelos fue la **Sensibilidad (Recall)**.
*   **Justificación**: En el contexto de la predicción de churn, es más crítico identificar correctamente a los clientes que realmente van a abandonar el servicio (minimizar Falsos Negativos) que identificar incorrectamente a un cliente que no lo hará (Falsos Positivos). Un alto Recall asegura que la empresa detecte la mayor cantidad posible de clientes en riesgo, permitiendo una intervención oportuna.

## 6. Resultados y Conclusiones
Se comparó el rendimiento de los modelos utilizando el Recall promedio obtenido a través de la validación cruzada estratificada:
*   **Árbol de Decisión**: 0.5212
*   **K-Nearest Neighbors**: 0.6608
*   **Random Forest**: 0.6758

Basado en estos resultados, el modelo **RandomForestClassifier** demostró el mejor rendimiento en términos de Recall.

*   **Recomendación**: Se recomienda la implementación del modelo Random Forest. Su capacidad superior para identificar a los clientes propensos al churn lo convierte en la mejor opción para respaldar las estrategias de retención, minimizando la pérdida de ingresos y maximizando el impacto de las acciones de marketing y atención al cliente.

## 7. Instrucciones de Uso
Para ejecutar el análisis y los modelos en este cuaderno:
1.  Asegúrate de tener un entorno de Google Colab o Python con las siguientes bibliotecas instaladas:
    *   `pandas`
    *   `scikit-learn`
    *   `matplotlib`
    *   `seaborn`
    *   `imblearn`
2.  Carga el archivo `datos_tratados.csv` en el entorno de Colab (o en la ruta especificada si es local).
3.  Ejecuta las celdas del cuaderno secuencialmente para replicar el preprocesamiento de datos, el modelado y la evaluación.
