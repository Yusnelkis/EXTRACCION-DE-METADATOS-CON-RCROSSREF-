# Extracción de Metadatos usando `rcrossref`

Este proyecto utiliza el paquete `rcrossref` en R para buscar y extraer metadatos de artículos científicos mediante su DOI, así como realizar análisis de publicaciones de autores específicos.

## Objetivos del Proyecto

1. Extraer metadatos clave (título, autores, fecha de publicación) de artículos científicos usando su DOI.
2. Buscar artículos de un autor específico y extraer información sobre sus publicaciones.
3. Limpiar y estructurar los datos obtenidos para facilitar su análisis y exportación.

## Estructura del Proyecto

- `/src`: Contiene el código R en formato `.Rmd` para la extracción de metadatos y búsqueda de artículos.
- `/data`: Carpeta donde se almacenarán los resultados de las búsquedas y las exportaciones en formato CSV.
- `README.md`: Este archivo que explica cómo ejecutar el proyecto.

## Metodología

El proceso sigue estos pasos:

1. **Carga del Paquete**: Instala y carga el paquete `rcrossref` si no está instalado previamente.
2. **Búsqueda por DOI**: Se utiliza un DOI específico para obtener los metadatos completos de un artículo.
3. **Extracción de Información Clave**: Extrae el título, los autores y la fecha de publicación.
4. **Búsqueda de Publicaciones por Autor**: Búsqueda de artículos asociados a un autor específico y extracción de los primeros resultados.
5. **Limpieza de Datos**: Conversión de estructuras de listas a texto para facilitar su manejo.
6. **Exportación de Resultados**: Los resultados se exportan a un archivo CSV para un análisis posterior.

## Requisitos

Para ejecutar este proyecto, es necesario tener instalado:

- R
- RStudio
- Paquetes: `rcrossref`, `dplyr`, `readr`

Para instalar los paquetes requeridos, ejecuta:

```r
install.packages(c("rcrossref", "dplyr", "readr"))
