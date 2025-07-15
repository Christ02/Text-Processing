# Laboratorio 7: Procesamiento de Texto

Este repositorio contiene el código y análisis del Laboratorio 7 sobre Procesamiento de Texto, realizado por Christian Barrios el 19 de octubre de 2024.

## Descripción

El laboratorio utiliza técnicas de procesamiento de texto para analizar reseñas de productos de salud y cuidado personal. Se exploran patrones en los textos, frecuencias de palabras y se generan visualizaciones como wordclouds.

## Datasets utilizados

1. `Health_and_Personal_Care_metadata.csv` - Metadatos de productos
2. `Health_and_Personal_Care.csv` - Reseñas de productos

## Librerías requeridas

- `dplyr`
- `tm` (Text Mining)
- `wordcloud`
- `ggplot2`

## Análisis realizados

1. **Conteo de productos**: Identificación de productos cuyas reseñas contienen las palabras "love", "recommend" y "enjoy".
2. **Top 5 tiendas**: Análisis de las tiendas con más productos que tienen estas reseñas.
3. **Wordcloud general**: Visualización de palabras frecuentes en todas las reseñas relevantes.
4. **Wordcloud por tienda**: Visualización específica para las 5 principales tiendas.
5. **Frecuencia de palabras**: Identificación y gráfico de las 25 palabras más frecuentes.

## Resultados clave

- Se identificaron **25,424 productos** con reseñas que contienen las palabras clave.
- Las tiendas con más productos relevantes son (en orden): sin nombre específico (" "), HAARBB, Eyekepper, Generic y Glade.
- Las palabras más frecuentes incluyen "love", "product", "recommend", "great" y "use".

## Visualizaciones

El proyecto incluye:
1. Wordcloud de todas las reseñas relevantes
2. Wordcloud específico para las 5 principales tiendas
3. Gráfico de barras con las 25 palabras más frecuentes

## Cómo ejecutar

1. Asegurarse de tener instaladas las librerías requeridas
2. Descargar los datasets en el mismo directorio que el script
3. Ejecutar el código R completo

## Notas

- El código incluye transformaciones de texto como conversión a minúsculas, eliminación de puntuación y stopwords.
- Las visualizaciones se generan automáticamente al ejecutar el script.
