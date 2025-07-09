-----
# **Modelo Predictivo de Asentamientos Informales en la Región Metropolitana**

## 🗺️ Resumen del Proyecto

Este proyecto consiste en el desarrollo de un **modelo de Machine Learning** para detectar y predecir la probabilidad de formación de asentamientos informales (campamentos) a nivel de distrito censal en la Región Metropolitana de Santiago.

Para lograrlo, se integraron múltiples capas de información geoespacial y socioeconómica, combinando datos censales, catastrales, de infraestructura y métricas espaciales derivadas de imágenes satelitales. El producto final es un modelo predictivo robusto y un mapa de riesgo interactivo que sirve como herramienta para la planificación urbana y la política pública.

## 📊 Fuentes de Datos

La construcción del modelo se basó en la integración de las siguientes fuentes de datos públicas y privadas:

  * **Datos de Campamentos TECHO-Chile:** Información catastral y de ubicación de campamentos.
      * **Link:** [https://cl.techo.org/catastro/](https://cl.techo.org/catastro/)
  * **Datos del Censo (INE):** Capas geoespaciales con la división administrativa y censal (distritos) de Chile.
      * **Link:** [https://www.ine.gob.cl/herramientas/portal-de-mapas/geodatos-abiertos](https://www.ine.gob.cl/herramientas/portal-de-mapas/geodatos-abiertos)
  * **Encuesta CASEN en Pandemia (Pobreza Comunal):** Estimaciones de la tasa de pobreza por ingresos y multidimensional a nivel comunal.
      * **Link:** [http://observatorio.ministeriodesarrollosocial.gob.cl/pobreza-comunal-pandemia](https://www.google.com/search?q=http://observatorio.ministeriodesarrollosocial.gob.cl/pobreza-comunal-pandemia)
  * **Datos Crudos de Viviendas y Geometrías (Uso Privado):** Set de datos con la geolocalización de viviendas, utilizado para la delimitación inicial de los polígonos de campamentos.
      * **Acceso (restringido):** [https://drive.google.com/file/d/12VoavAOfokVyiy3Wo6WheDrL8hpqv3cj/view?usp=sharing](https://drive.google.com/file/d/12VoavAOfokVyiy3Wo6WheDrL8hpqv3cj/view?usp=sharing)
  * **Imágenes Satelitales (Google Earth Engine):** Imágenes de las misiones Copernicus/Sentinel-2 y NOAA/VIIRS para el análisis temporal.

## ⚙️ Metodología

El proyecto se estructuró en tres fases principales:

### 1\. Preprocesamiento y Construcción de Variables

Se realizó un intensivo proceso de limpieza, validación y unión de los datos de origen. Las decisiones y procedimientos clave fueron:

  * **Generación de Polígonos de Campamentos:** Se aplicaron algoritmos de clustering (**DBSCAN**) para identificar los núcleos de los asentamientos y se generaron polígonos ajustados a su forma, limpiando datos atípicos.
  * **Características Satelitales:** Usando **Google Earth Engine**, se calcularon índices como **NDBI** (construcción), **NDVI** (vegetación) y **Luces Nocturnas** para los años 2018, 2020 y 2024. Se generaron variables de **cambio temporal** (ej. `NDBI_cambio_18_24`) que resultaron ser altamente predictivas.
  * **Creación del Dataset Maestro:** Se consolidaron todas las fuentes en un único GeoDataFrame a nivel de distrito (451 en total), donde cada uno fue enriquecido con características como:
      * Número de campamentos (`n_campamentos`).
      * Distancia a la red vial principal (`distancia_vial_m`).
      * Tasas de pobreza comunal.
      * Métricas de cambio satelital del propio distrito y de los campamentos que contiene.

### 2\. Modelamiento y Clasificación

Se utilizó un modelo **HistGradientBoostingClassifier**, implementado a través de un pipeline que incluyó:

  * **Preprocesamiento:** Imputación de valores faltantes y escalado de características.
  * **Manejo de Desbalance:** Aplicación de **RandomOverSampler** para dar igual peso a la clase minoritaria (distritos con campamentos).
  * **Optimización de Hiperparámetros:** Uso de `GridSearchCV` para encontrar la mejor combinación de parámetros del modelo.

## ✅ Resultados

  * **Dataset Final:** Se generó un archivo `dataset_final_para_modelo.geojson` con **451 registros (distritos)** y **38 columnas (características)**.
  * **Modelo Predictivo:** Se obtuvo un modelo entrenado capaz de identificar distritos en riesgo con una alta precisión, optimizado para alcanzar un **Recall superior al 60%** en la detección de la clase de interés.
  * **Visualización Interactiva:** Se desarrolló un **mapa multicapa en formato HTML** que permite explorar visualmente la probabilidad de riesgo predicha, la clasificación final y las principales variables utilizadas como filtros.

