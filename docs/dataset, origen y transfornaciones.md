Este documento describe el origen, la estructura y referencias del conjunto de datos utilizado en el proyecto **Predicción prenatal de riesgo de bajo peso al nacer en Argentina**.

## 1. Origen del dataset

El proyecto utiliza el archivo CSV `nacidos-vivos-registrados-en-la-republica-argentina-entre-los-anos-2005-2022.csv`, que contiene datos de nacidos vivos registrados en Argentina para el período 2005–2022.

La fuente institucional es la **Dirección de Estadística e Información en Salud (DEIS), dependiente del Ministerio de Salud de la República Argentina**. La DEIS publica archivos y documentación sobre nacidos vivos.

- **Fuente institucional:** [DEIS — Ministerio de Salud. Dirección de Estadística e Información en Salud](https://datos.salud.gob.ar/dataset/d1350588-d8bb-4892-b21c-48738311e218/archivo/5a68ea36-03fe-4b38-b590-d7cf2a13b821)
- **Catálogo de datos abiertos:** [Nacidos vivos registrados en la República Argentina entre los años 2005-2022](https://datos.gob.ar/dataset/nacidos-vivos-registrados-por-jurisdiccion-de-residencia-de-la-madre-republica-argentina/resource/a55038dc-30f1-5327-af78-8ecd18f54cff)
- **Archivo utilizado:** `nacidos-vivos-registrados-en-la-republica-argentina-entre-los-anos-2005-2022.csv`
- **Formato:** CSV
- **Período:** 2005–2022.
- **Fecha de adquisición:** 16 de septiembre de 2026.


## 2. Cantidad de instancias y características

La inspección inicial del archivo original registró:

- **Filas:** 546.963.
- **Columnas:** 12.

Las filas corresponden a registros agregados, no necesariamente a nacimientos individuales. La columna `nacimientos_cantidad` indica cuántos nacimientos representa cada combinación de características.

Los tipos indicados en la tabla son los esperados según la lectura inicial y deben contrastarse con la salida definitiva de `df.info()` o `df.dtypes`.

| Columna | Tipo esperado en pandas | Descripción |
|---|---|---|
| `anio` | Entero (`int64`) | Año del registro. |
| `jurisdiccion_de_residencia_id` | Entero (`int64`) | Identificador numérico de la jurisdicción de residencia de la madre. |
| `jurisdicion_residencia_nombre` | Texto (`object`) | Nombre de la jurisdicción de residencia de la madre. |
| `edad_madre_grupo` | Texto (`object`) | Grupo de edad de la madre. |
| `instruccion_madre` | Texto (`object`) | Categoría del nivel de instrucción de la madre. |
| `tipo_de_parto_id` | Entero (`int64`) | Identificador de tipo de parto |
| `tipo_de_parto_nombre` | Texto (`object`) | Nombre de la categoría de tipo de parto/nacimiento. |
| `semana_gestacion` | Texto (`object`) | Categoría o intervalo de semanas de gestación. |
| `intervalo_peso_al_nacer` | Texto (`object`) | Categoría del intervalo de peso al nacer. |
| `sexo_id` | Entero (`int64`) | Identificador numérico de la categoría de sexo. |
| `Sexo` | Texto (`object`) | Categoría textual del sexo del recién nacido. |
| `nacimientos_cantidad` | Entero (`int64`) | Cantidad de nacimientos representados por cada fila agregada. |

## 3. Unidad de observación y registros agregados

El dataset es una tabla agregada. Cada fila representa una combinación de características compartidas, como año, jurisdicción, grupo de edad materna, nivel de instrucción, tipo de nacimiento, semanas de gestación, intervalo de peso y sexo.

Es necesario distinguir entre:

- **Cantidad de filas:** número de combinaciones o registros agregados del archivo.
- **`nacimientos_cantidad`:** frecuencia de nacimientos representados por cada registro.

En el modelado, `nacimientos_cantidad` se conserva separada de los predictores y se utiliza como ponderador (`sample_weight`) para que los registros contribuyan al entrenamiento y a las métricas según la cantidad de nacimientos representados. Esto requiere verificar que los pesos se transmitan correctamente al ajuste de cada modelo y a la evaluación.

## 4. Variables de interés y alcance de la predicción

Entre las variables consideradas para el modelado se encuentran la jurisdicción de residencia, el grupo de edad materna, el nivel de instrucción, el tipo de parto/nacimiento, las semanas de gestación y el sexo.

La propuesta sitúa la estimación del riesgo en una **instancia de evaluación prenatal tardía o de admisión al parto**, en la que la edad gestacional ya se encuentra registrada.

El modelo no debe describirse como una predicción disponible durante cualquier etapa del embarazo. El alcance queda limitado a la instancia definida y a las variables efectivamente disponibles en ella.

## 5. Preparación y transformaciones realizadas

Las decisiones documentadas en el análisis inicial son las siguientes:

1. **Carga del archivo:** lectura del CSV mediante pandas, manteniendo el archivo original sin modificaciones.
2. **Jurisdicción sin información:** se identificaron valores nulos en `jurisdicion_residencia_nombre` y se unificaron bajo la categoría `Sin Información`.
3. **Categorías de sexo:** las categorías `Indeterminado` y `Desconocido` se agrupan bajo `Desconocido`.
4. **Construcción de `bajo_peso`:**
   - `1`: bajo peso al nacer, inferior a 2500 gramos.
   - `0`: sin bajo peso, igual o superior a 2500 gramos.
   - `9.Sin especificar`: no permite determinar la clase y se excluye del conjunto de trabajo utilizado para modelar.
5. **Prevención de fuga de información:** `intervalo_peso_al_nacer` se utiliza para construir la variable objetivo y, por lo tanto, se excluye de los predictores.
6. **Predictores considerados:** jurisdicción de residencia, grupo de edad materna, nivel de instrucción materna, tipo de parto/nacimiento, semana de gestación y sexo.
7. **Ponderación:** `nacimientos_cantidad` se conserva fuera de los predictores y se utiliza como peso de muestra durante el entrenamiento y la evaluación.

En el análisis inicial se identificaron **26.432 filas** con la categoría `9.Sin especificar`, correspondientes a **145.857 nacimientos representados**. Estas filas se excluyeron de la copia de trabajo utilizada para construir la variable objetivo.

## 6. Consideraciones y limitaciones

- El dataset contiene registros agregados, por lo que los resultados deben interpretarse teniendo en cuenta el uso de ponderadores y no como si cada fila fuera un nacimiento individual.
- Los registros con `9.Sin especificar` en el intervalo de peso se excluyen porque no permiten definir la variable objetivo.
- `intervalo_peso_al_nacer` no debe incluirse entre los predictores porque se utiliza para construir `bajo_peso`.
- La variable `semana_gestacion` se conserva para el alcance de evaluación prenatal tardía/admisión al parto acordado para el proyecto.

## 7. Referencias

- Fuente del Valor Objetivo: [Metas mundiales de nutrición 2025: documento normativo sobre bajo peso al nacer](https://www.who.int/es/publications/i/item/WHO-NMH-NHD-14.5).  
