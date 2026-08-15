# CBS06M---Examen-Parcial---Tema-21
Tema 21 del Examen Parcial: Código fuente y dataset.

# Optimización mediante pruning y compresión ligera para la ejecución eficiente de modelos de IA en dispositivos de borde para entornos IoT con recursos limitados, 2026

## Tema
Tema 21 – Examen Parcial

## Modelo utilizado
LightGBM (`LGBMClassifier`, v4.6.0 en el entorno de ejecución)

## Estrategias evaluadas
- **Baseline**: LightGBM con las 42 features disponibles, sin optimización.
- **Pruning**: selección de las 20 features de mayor importancia (según `feature_importances_` del modelo baseline) y re-entrenamiento del modelo solo con ese subconjunto. No es un pruning estructural del árbol (poda de nodos/hojas), sino reducción de dimensionalidad de entrada.
- **Compresión ligera**: serialización del modelo baseline con `joblib.dump(..., compress=9)`. No hay cuantización de pesos ni modificación de la arquitectura del modelo; solo cambia el formato/tamaño del archivo guardado.
- **Pruning + Compresión ligera**: combinación de ambas técnicas (modelo podado, serializado con compresión nivel 9).

## Métricas evaluadas
- Accuracy
- Precision (weighted)
- Recall (weighted)
- F1-Score (weighted)
- Latencia de inferencia (ms, promedio sobre 1000 muestras, 5 repeticiones tras warm-up)
- Uso de RAM del proceso en Google Colab (MB, vía `psutil`)
- Tamaño del modelo serializado (KB)

## Dataset utilizado
Edge-IIoTset (variante `ML-EdgeIIoT-dataset.csv`, 157,800 muestras, 15 clases de `Attack_type`).

El dataset **no se descarga automáticamente**: el notebook está diseñado para ejecutarse en Google Colab y requiere subir manualmente el archivo `ML-EdgeIIoT-dataset.csv` mediante el widget de carga de archivos incluido en el notebook.

Fuente original: https://www.kaggle.com/datasets/sibasispradhan/edge-iiotset-dataset

## Notas de reproducibilidad
- `random_state = 42` en el split train/test (80/20, estratificado) y en los hiperparámetros de LightGBM.
- Antes del entrenamiento se eliminan `Attack_label` (para evitar data leakage) y todas las columnas no numéricas restantes (p. ej. `frame.time`, `ip.src_host`, `http.request.method`, etc.).
- El notebook fue ejecutado en Google Colab; los tiempos de latencia y uso de RAM reportados dependen del hardware/entorno de Colab en el momento de la ejecución y pueden variar entre corridas.
