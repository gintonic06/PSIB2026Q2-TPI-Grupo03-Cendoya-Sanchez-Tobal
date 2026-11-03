# PSIB2026Q2-TPI-Grupo03-Cendoya Sanchez Tobal

Trabajo Práctico de Imágenes (TPI) de **16.63 Procesamiento de Señales e Imágenes Biomédicas** (ITBA, 2.º cuatrimestre 2026).

**Segmentación y extracción de características morfológicas, de color y de textura en imágenes dermatoscópicas para el análisis de lesiones cutáneas.**

Integrantes: Cendoya, Sanchez, Tobal.

## Descripción

Se desarrolla un algoritmo de procesamiento de imágenes dermatoscópicas que:

1. Caracteriza el conjunto de imágenes (formato, resolución, canales e histogramas).
2. Preprocesa las imágenes: compara filtros de suavizado y mejoras de contraste, y elimina pelos mediante black-hat e inpainting.
3. Segmenta automáticamente la lesión con cinco métodos sobre el canal S (umbral fijo, Otsu, Canny, crecimiento de regiones y Chan–Vese) y refina las máscaras con operaciones morfológicas.
4. Evalúa las segmentaciones con Dice e IoU respecto de las máscaras de los especialistas.
5. Extrae características morfológicas (circularidad, solidez, asimetría, etc.), de color (RGB, HSV y CIELAB) y de textura (GLCM).
6. Compara estadísticamente las características entre melanoma, nevus y queratosis seborreica, y analiza cómo influye la segmentación sobre las mediciones.

El trabajo no pretende reemplazar la evaluación diagnóstica de un profesional.

## Dataset

[ISIC 2017: Skin Lesion Analysis Towards Melanoma Detection](https://challenge.isic-archive.com/data/#2017), conjunto de entrenamiento (2000 imágenes):

- `ISIC-2017_Training_Data`: imágenes dermatoscópicas JPEG.
- `ISIC-2017_Training_Part1_GroundTruth`: máscaras de segmentación de referencia (PNG).
- `ISIC-2017_Training_Part3_GroundTruth.csv`: diagnóstico de cada imagen (melanoma, queratosis seborreica; si ambas columnas valen 0, nevus).

El dataset ya está incluido en la carpeta `Data` del repositorio. Si se descarga desde el sitio de ISIC, debe respetarse esta estructura:

```
Data/
├── ISIC-2017_Training_Data/                 (ISIC_xxxxxxx.jpg)
├── ISIC-2017_Training_Part1_GroundTruth/    (ISIC_xxxxxxx_segmentation.png)
└── ISIC-2017_Training_Part3_GroundTruth.csv
```

## Estructura del repositorio

| Archivo | Contenido |
|---|---|
| `Pipeline_V2.ipynb` | Notebook principal con todo el procesamiento, los resultados y su análisis |
| `Pipeline.ipynb` | Versión inicial del pipeline (exploración preliminar) |
| `resultados/caracteristicas_lesiones.csv` | Características extraídas de las 150 lesiones, con máscara de referencia y automática (se genera al ejecutar el notebook) |
| `requirements.txt` | Dependencias de Python |

## Instalación

Requiere Python 3.10 o superior.

```bash
git clone https://github.com/gintonic06/PSIB2026Q2-TPI-Grupo03-Cendoya-Sanchez-Tobal.git
cd PSIB2026Q2-TPI-Grupo03-Cendoya-Sanchez-Tobal
python -m venv venv
source venv/bin/activate        # En Windows: venv\Scripts\activate
pip install -r requirements.txt
```

El repositorio pesa varios GB porque incluye el dataset, por lo que la clonación puede demorar.

## Ejecución

```bash
jupyter notebook Pipeline_V2.ipynb
```

Ejecutar todas las celdas en orden (*Run All*). El notebook debe estar en la misma carpeta que `Data`.

La ejecución completa demora entre 40 y 60 minutos, según el equipo. Las etapas más lentas son la caracterización de las 2000 imágenes, la elección de parámetros en el conjunto de ajuste y la segmentación con Chan–Vese y crecimiento de regiones de las 150 imágenes.

## Muestras utilizadas

- **Muestra de evaluación:** 150 imágenes (50 por diagnóstico), elegidas al azar con semilla fija (`random_state=42`).
- **Conjunto de ajuste:** 30 imágenes (10 por diagnóstico), distintas de la muestra de evaluación, que se utilizan solo para elegir parámetros (`random_state=0`).

## Resultados principales

- La mejor entrada para la segmentación es el canal S (saturación) de HSV. La ecualización global y CLAHE empeoran los resultados.
- Chan–Vese y Otsu sobre el canal S, con eliminación de pelos y refinamiento morfológico, alcanzan un Dice mediano de 0,85 y 0,84 respecto de las máscaras de los especialistas.
- La heterogeneidad de color, la textura (GLCM) y la asimetría difieren significativamente entre melanoma y lesiones benignas. Las características de color y textura se mantienen con la segmentación automática, mientras que las de forma se degradan.
