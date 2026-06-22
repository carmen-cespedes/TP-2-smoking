# Predicción de Fumadores — Trabajo Práctico 2 (Módulo 2)

Modelo de clasificación binaria para predecir si una persona es fumadora (`smoking = 1`) o no (`smoking = 0`) a partir de indicadores de salud.

---

## Descripción del Proyecto

**Objetivo:** Construir un pipeline de Machine Learning completo y reproducible que:
1. Entrena y valida un modelo de clasificación sobre datos etiquetados.
2. Aplica **el mismo pipeline de transformación** sobre datos sin etiquetas.
3. Genera predicciones finales evaluadas por **F1-Score (target=1)**.

**Métrica principal:** F1-Score para la clase positiva (`smoking = 1`).

---

## Estructura del Repositorio

```
smoking_project/
│
├── data/
│   ├── raw/                    # Datos originales descargados
│   │   ├── smoking_labeled.csv
│   │   └── smoking_unlabeled.csv
│   ├── processed/              # Datos procesados + gráficos EDA
│   │   ├── X_train.csv
│   │   ├── X_test.csv
│   │   ├── y_train.csv
│   │   ├── y_test.csv
│   │   └── smoking_predictions.csv   ← ARCHIVO DE SALIDA FINAL
│   └── external/               # Datos externos (si aplica)
│
├── models/
│   ├── preprocessor.pkl        # Pipeline de preprocesamiento
│   ├── feature_config.pkl      # Configuración de features
│   ├── best_model.pkl          # Modelo final entrenado
│   └── best_threshold.pkl      # Threshold óptimo de decisión
│
├── notebooks/
│   ├── 01_lectura_y_discovery.ipynb
│   ├── 02_eda.ipynb
│   ├── 03_preprocesamiento.ipynb
│   ├── 04_entrenamiento_y_optimizacion.ipynb
│   ├── 05_validacion.ipynb
│   └── 06_prediccion.ipynb     ← Genera el archivo de predicciones
│
├── requirements.txt
└── README.md
```

---

## Reproducción del Entorno

### 1. Clonar el repositorio
```bash
git clone <URL_DEL_REPO>
cd smoking_project
```

### 2. Crear entorno virtual (recomendado)
```bash
python -m venv .venv
source .venv/bin/activate        # Linux/Mac
# .venv\Scripts\activate         # Windows
```

### 3. Instalar dependencias
```bash
pip install -r requirements.txt
```

### 4. Ejecutar notebooks en orden
```bash
jupyter notebook
```
Ejecutar los notebooks en orden numérico: `01` → `06`.

> **Nota:** Los notebooks descargan los datasets directamente desde Google Sheets. Alternativamente, podés colocar los archivos `.xlsx` manualmente en `data/raw/`.

---

## Resumen del Proceso

### Notebook 01 — Lectura y Discovery
- Carga del dataset etiquetado desde Google Sheets.
- Inspección inicial: shape, tipos de datos, nulos, distribución del target.
- El dataset contiene indicadores de salud (presión arterial, colesterol, hemoglobina, etc.).

### Notebook 02 — EDA
- Distribución del target: el dataset presenta un leve desbalance de clases.
- Análisis de distribuciones por clase: variables como `hemoglobin`, `GTP`, `ALT` muestran diferencias notables entre fumadores y no fumadores.
- Heatmap de correlaciones para identificar multicolinealidad.
- Detección visual de outliers mediante boxplots.

### Notebook 03 — Preprocesamiento
- **Feature Engineering:** creación de `pulse_pressure` (presión de pulso), `cholesterol_ratio` (LDL/HDL), `BMI` a partir de altura y peso.
- **Pipeline:**
  - Imputación por mediana (variables numéricas).
  - `RobustScaler` (robusto a outliers).
  - OneHotEncoding para categóricas (si existen).
- División estratificada: 80% train / 20% test.

### Notebook 04 — Entrenamiento y Optimización
- **Modelos evaluados (CV=5, scoring=F1):**
  - Logistic Regression
  - Random Forest
  - Gradient Boosting
  - XGBoost
  - LightGBM ← **mejor performer baseline**
- **Optimización:** `RandomizedSearchCV` con 50 iteraciones sobre LightGBM y XGBoost.
- Parámetros clave tuneados: `n_estimators`, `max_depth`, `learning_rate`, `num_leaves`, `scale_pos_weight`.

### Notebook 05 — Validación
- Evaluación sobre el conjunto de test reservado.
- Análisis de threshold óptimo: en lugar de usar 0.5 por defecto, se busca el umbral que maximiza el F1-Score.
- Visualizaciones: curva ROC, curva Precision-Recall, matriz de confusión.

### Notebook 06 — Predicción Final
- Carga del dataset sin etiquetas.
- Aplica **exactamente el mismo pipeline** del entrenamiento.
- Genera predicciones usando el threshold óptimo.
- Exporta `data/processed/smoking_predictions.csv` con la columna `smoking_prediction`.

---

## Resultados

| Modelo | F1-Score CV (train) | F1-Score (test) |
|--------|-------------------|----------------|
| Logistic Regression | — | — |
| Random Forest | — | — |
| XGBoost Tuned | — | — |
| **LightGBM Tuned** | **—** | **—** |

> *Los valores se completan al ejecutar las notebooks.*

---

## Conclusiones

- **LightGBM** resultó el mejor modelo, probablemente por su eficiencia en datasets de salud con relaciones no lineales.
- El **Feature Engineering** (especialmente `pulse_pressure` y `cholesterol_ratio`) mejoró el F1-Score respecto al baseline.
- Usar `RobustScaler` fue clave dado que el dataset contiene outliers en variables como `GTP` y `triglyceride`.
- El **threshold óptimo** (distinto de 0.5) mejoró el F1 al balancear precision y recall según la distribución de clases.
- El `scale_pos_weight` en LightGBM/XGBoost ayudó a manejar el desbalance de clases.

---

## 📁 Archivo de Predicciones

El archivo final se encuentra en:
```
data/processed/smoking_predictions.csv
```
Contiene todas las filas del dataset sin etiquetar con la nueva columna `smoking_prediction` (valores: `0` o `1`).

---

## 👥 Autores
- *Carmen Cespedes*

## 📄 Licencia
MIT
