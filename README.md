# Clasificación del riesgo de diabetes

Proyecto de análisis de datos y aprendizaje automático orientado a comparar estrategias de tratamiento de datos faltantes y modelos de clasificación sobre el **Pima Indians Diabetes Dataset**.

El trabajo prioriza la trazabilidad metodológica: auditoría de calidad, prevención de fuga de información, validación cruzada estratificada, calibración de probabilidades, selección de umbrales e incertidumbre de las métricas.

> Este es un ejercicio educativo y de portafolio. El modelo no está validado para uso clínico y no debe utilizarse como herramienta diagnóstica.

## Problema analítico

La base no contiene valores `NaN` explícitos, pero presenta ceros clínicamente no plausibles en glucosa, presión arterial, grosor de piel, insulina e índice de masa corporal. Estos ceros se trataron como datos faltantes encubiertos.

La pregunta principal fue:

> ¿Es posible estimar la probabilidad de un resultado positivo de diabetes sin eliminar innecesariamente una parte considerable de los registros?

## Metodología

1. Auditoría de estructura, duplicados, balance de clases y ceros no plausibles.
2. Análisis de porcentajes, patrones y asociaciones de ausencia.
3. Exploración de distribuciones y correlaciones de Spearman.
4. División estratificada: 80 % entrenamiento y 20 % prueba.
5. Comparación de tres estrategias:
   - casos completos;
   - imputación por mediana;
   - mediana con indicadores de ausencia.
6. Comparación de Dummy, regresión logística, Random Forest y XGBoost.
7. Ajuste de hiperparámetros mediante validación cruzada estratificada.
8. Calibración sigmoide y selección de umbrales con predicciones fuera de pliegue.
9. Evaluación única sobre prueba, bootstrap estratificado e interpretación de coeficientes.

Todo el preprocesamiento se incorporó en pipelines para evitar que la imputación o el escalado aprendieran información de los pliegues de validación o del conjunto de prueba.

## Resultados principales

La regresión logística regularizada fue seleccionada por su equilibrio entre discriminación, sensibilidad, estabilidad e interpretabilidad. En validación cruzada obtuvo un ROC-AUC medio aproximado de `0,844`.

En el conjunto de prueba reservado, el modelo calibrado obtuvo:

| Métrica | Resultado |
|---|---:|
| ROC-AUC | 0,812 |
| PR-AUC | 0,671 |
| Brier score | 0,175 |
| Log loss | 0,515 |

El efecto del umbral fue:

| Escenario | Umbral | Sensibilidad | Especificidad | Falsos negativos | Falsos positivos |
|---|---:|---:|---:|---:|---:|
| Equilibrio principal | 0,45 | 0,556 | 0,790 | 24 | 21 |
| Orientado a sensibilidad | 0,27 | 0,852 | 0,670 | 8 | 33 |

Reducir el umbral recuperó positivos que el escenario principal habría omitido, a cambio de aumentar los falsos positivos. La elección del umbral depende del costo operativo de cada tipo de error.

## Estructura

```text
diabetes-risk-classification/
├── README.md
├── README_EN.md
├── data/
│   └── diabetes.csv
├── notebooks/
│   ├── 01_diabetes_eda_calidad_datos.ipynb
│   ├── 02_diabetes_modelado_comparacion_estrategias.ipynb
│   └── 03_diabetes_evaluacion_interpretacion.ipynb
├── images/
│   ├── missing_values.png
│   ├── values_distribution.png
│   ├── values_correlation.png
│   ├── model_comparison.png
│   └── confusion_matrices.png
├── requirements.txt
├── .gitignore
└── LICENSE
```

## Ejecución local

Requiere Python 3.10 o posterior.

```bash
git clone https://github.com/dualmapa/diabetes-risk-classification.git
cd diabetes-risk-classification
python -m venv .venv
```

Activación en Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Activación en macOS o Linux:

```bash
source .venv/bin/activate
```

Instalación y apertura:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter lab
```

Los notebooks deben ejecutarse desde la raíz del repositorio y en orden numérico. La ruta utilizada por ellos es `data/diabetes.csv`.

## Tecnologías

- Python
- pandas y NumPy
- Matplotlib y seaborn
- SciPy
- scikit-learn
- XGBoost
- Jupyter

## Fuente de datos

Versión utilizada: [Diabetes Dataset en Kaggle](https://www.kaggle.com/datasets/mathchi/diabetes-data-set), publicada con licencia **CC0: Public Domain**.

La base corresponde a una población histórica y específica. Por ello, los resultados no deben generalizarse automáticamente a otras poblaciones o contextos clínicos.

## Limitaciones

- Tamaño reducido: 768 registros y 154 observaciones de prueba.
- Alta proporción de datos faltantes encubiertos en insulina y grosor de piel.
- El mecanismo de ausencia no puede identificarse de manera definitiva con los datos disponibles.
- No existe validación externa, temporal ni por institución.
- No se realizó análisis de equidad por subgrupos.
- Los coeficientes describen asociaciones predictivas, no causalidad.

## Autor

**Alexander Marín**  
Analista de datos

## Licencias

El código y la documentación de este repositorio se distribuyen bajo licencia MIT. El archivo de datos conserva la licencia CC0 indicada por su fuente.

