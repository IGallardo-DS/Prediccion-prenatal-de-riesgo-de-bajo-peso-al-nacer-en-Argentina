# Predicción prenatal de riesgo de bajo peso al nacer en Argentina

## 1. Descripción del proyecto

Este proyecto propone desarrollar y evaluar modelos de aprendizaje automático supervisado para estimar el riesgo de bajo peso al nacer en Argentina, utilizando registros de nacimientos correspondientes al período 2005–2022.

Se plantea un problema de clasificación binaria. Se considera **bajo peso al nacer** un peso inferior a 2500 gramos y **sin bajo peso** un peso igual o superior a 2500 gramos.

La propuesta contempla una instancia de evaluación prenatal tardía o de admisión al parto, en la que la edad gestacional ya se encuentre registrada. Por este motivo, el alcance de la predicción se limita a esa instancia y a las variables que estén disponibles en ese momento.

## 2. Formulación de objetivos

### Objetivo general

Desarrollar y evaluar modelos de aprendizaje automático capaces de estimar el riesgo de bajo peso al nacer en una instancia de evaluación prenatal tardía o de admisión al parto, utilizando las variables disponibles en los registros de nacimientos de Argentina.

### Objetivos específicos

- Examinar la estructura y las características del conjunto de datos.
- Realizar un análisis exploratorio para conocer la distribución de las variables y de las clases.
- Preparar los datos para utilizarlos en modelos de clasificación.
- Construir la variable objetivo `bajo_peso` a partir del umbral de 2500 gramos.
- Entrenar y comparar modelos de clasificación binaria vistos en clase.
- Evaluar el desempeño de los modelos mediante métricas que permitan analizar la detección de casos de bajo peso y el equilibrio entre sensibilidad, precisión y falsos positivos.
- Interpretar y comunicar los resultados teniendo en cuenta las limitaciones del dataset, sin atribuir causalidad a las asociaciones observadas.

## 3. Contexto del problema y relevancia

El bajo peso al nacer es una característica relevante para el seguimiento de los nacimientos. Contar con una estimación del riesgo en una instancia tardía de evaluación prenatal o al momento de la admisión al parto podría ayudar a identificar registros con características asociadas al bajo peso y a estudiar patrones presentes en los datos.

El proyecto analizará variables disponibles en el dataset, como la jurisdicción de residencia, el grupo de edad materna, el nivel de instrucción, las semanas de gestación y el sexo. La utilidad de cada variable dependerá de su definición y de que se encuentre disponible en el momento previsto para la estimación.

## 4. Definición del tipo de problema

El proyecto corresponde a un problema de **aprendizaje supervisado de clasificación binaria**. La variable objetivo distingue entre:

- **Bajo peso al nacer:** peso inferior a 2500 gramos.
- **Sin bajo peso:** peso igual o superior a 2500 gramos.

La variable `bajo_peso` se construirá a partir de la categoría de peso al nacer. La variable original utilizada para definir la clase no se incorporará entre los predictores, para evitar fuga de información.

## 5. Modelos de aprendizaje automático propuestos

Se propone comparar modelos de clasificación estudiados en clase:

- **Regresión logística:** modelo de referencia para clasificación binaria que estima la probabilidad de pertenecer a una clase.
- **Árbol de decisión (CART):** utiliza reglas sucesivas para separar los registros y permite inspeccionar las decisiones de clasificación.
- **Random Forest:** combina múltiples árboles de decisión para obtener una clasificación conjunta.

La selección final se justificará a partir de las métricas de evaluación y del objetivo de detectar casos de bajo peso manteniendo un equilibrio razonable con la precisión y los falsos positivos.

## 6. Alcance y limitaciones

La predicción propuesta se sitúa en una instancia de evaluación prenatal tardía o de admisión al parto, cuando la edad gestacional ya se encuentra registrada. No se plantea como una predicción aplicable a cualquier momento del embarazo.

El dataset utilizado contiene registros agregados y no necesariamente una fila por cada nacimiento individual. Por eso, los resultados deberán interpretarse considerando la unidad de observación y el uso de `nacimientos_cantidad` como ponderador. El desempeño obtenido sobre estos datos no constituye por sí solo una validación clínica de predicciones individuales.

Además, se verificará en el diccionario de datos qué representa exactamente la variable `semana_gestacion` y su correspondencia con la instancia de predicción definida.

## 7. Organización del repositorio

- `data/raw/`: datos originales, sin modificaciones.
- `data/interim/`: datos intermedios durante la preparación.
- `data/processed/`: datos preparados para el análisis y el modelado.
- `data/external/`: datos procedentes de fuentes externas.
- `notebooks/`: notebooks de exploración, preprocesamiento y experimentación.
- `models/`: modelos entrenados, si corresponde almacenarlos en el repositorio.
- `reports/figures/`: gráficos exportados.
- `docs/`: documentación complementaria de las entregas.
- `src/bajo_peso/`: código reutilizable del proyecto.

## 8. Reproducibilidad y uso de datos

Las bibliotecas y sus versiones se registrarán en `requirements.txt`. Cuando corresponda, se documentarán las semillas aleatorias y los criterios utilizados para separar los datos en conjuntos de entrenamiento y prueba.

Antes de publicar archivos de datos en GitHub, se verificará que las condiciones de uso de la fuente permitan redistribuirlos. No se incluirá información sensible ni archivos que no deban hacerse públicos.

La descripción detallada del origen, la estructura del dataset y las transformaciones realizadas se encuentra en [`docs/entrega_2_dataset_preprocesamiento.md`](docs/entrega_2_dataset_preprocesamiento.md).
