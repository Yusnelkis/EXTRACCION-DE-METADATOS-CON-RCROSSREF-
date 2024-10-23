# CARGA DE DATOS Y EXTRACCIÓN DE METADATOS USANDO RCROSSREF

## Paso 1: Instalación y carga del paquete `rcrossref`

Primero, asegurémonos de que el paquete `rcrossref` esté instalado y
cargado en tu entorno de trabajo.

    # Verificamos si el paquete está instalado; si no lo está, se instala
    if(!require(rcrossref)) {
      install.packages("rcrossref")
    }

    ## Cargando paquete requerido: rcrossref

    # Cargar el paquete
    library(rcrossref)

## Paso 2: Búsqueda de un artículo mediante DOI

En este paso, utilizamos el DOI (Identificador de Objeto Digital) para
buscar los metadatos de un artículo. El DOI es un identificador único
que nos permite acceder a la información completa del artículo.

    # DOI del artículo
    doi <- "10.1038/s41586-020-2649-2"

    # Obtener los metadatos del artículo mediante el DOI
    article_metadata <- cr_works(dois = doi)

    # Mostrar los metadatos completos
    print(article_metadata$data)

    ## # A tibble: 1 × 33
    ##   alternative.id container.title created    deposited  published.print
    ##   <chr>          <chr>           <chr>      <chr>      <chr>          
    ## 1 2649           Nature          2020-09-17 2024-08-14 2020-09-17     
    ## # ℹ 28 more variables: published.online <chr>, doi <chr>, indexed <chr>,
    ## #   issn <chr>, issue <chr>, issued <chr>, member <chr>, page <chr>,
    ## #   prefix <chr>, publisher <chr>, score <chr>, source <chr>,
    ## #   reference.count <chr>, references.count <chr>,
    ## #   is.referenced.by.count <chr>, title <chr>, type <chr>,
    ## #   update.policy <chr>, url <chr>, volume <chr>, abstract <chr>,
    ## #   language <chr>, short.container.title <chr>, assertion <list>, …

## Paso 3: Extracción de información clave

Una vez obtenidos los metadatos, podemos extraer información clave como
el título del artículo, los autores y la fecha de publicación.

### Extracción del título del artículo

    # Extraer título
    titulo <- article_metadata$data$title
    print(paste("Título:", titulo))

    ## [1] "Título: Array programming with NumPy"

### Extracción de la lista de autores

    # Extraer lista de autores
    autores <- article_metadata$data$author
    autores_nombres <- sapply(autores, function(x) paste(x$given, x$family))
    print("Autores:")

    ## [1] "Autores:"

    print(autores_nombres)

    ##       [,1]                     
    ##  [1,] "Charles R. Harris"      
    ##  [2,] "K. Jarrod Millman"      
    ##  [3,] "Stéfan J. van der Walt" 
    ##  [4,] "Ralf Gommers"           
    ##  [5,] "Pauli Virtanen"         
    ##  [6,] "David Cournapeau"       
    ##  [7,] "Eric Wieser"            
    ##  [8,] "Julian Taylor"          
    ##  [9,] "Sebastian Berg"         
    ## [10,] "Nathaniel J. Smith"     
    ## [11,] "Robert Kern"            
    ## [12,] "Matti Picus"            
    ## [13,] "Stephan Hoyer"          
    ## [14,] "Marten H. van Kerkwijk" 
    ## [15,] "Matthew Brett"          
    ## [16,] "Allan Haldane"          
    ## [17,] "Jaime Fernández del Río"
    ## [18,] "Mark Wiebe"             
    ## [19,] "Pearu Peterson"         
    ## [20,] "Pierre Gérard-Marchant" 
    ## [21,] "Kevin Sheppard"         
    ## [22,] "Tyler Reddy"            
    ## [23,] "Warren Weckesser"       
    ## [24,] "Hameer Abbasi"          
    ## [25,] "Christoph Gohlke"       
    ## [26,] "Travis E. Oliphant"

### Extracción de la fecha de publicación

    # Extraer fecha de publicación
    fecha_publicacion <- article_metadata$data$published

    ## Warning: Unknown or uninitialised column: `published`.

    print(paste("Fecha de publicación:", fecha_publicacion))

    ## [1] "Fecha de publicación: "

## Paso 4: Búsqueda de artículos por autor

Ahora vamos a buscar artículos publicados por un autor específico. En
este caso, tomaremos como ejemplo a “John P. A. Ioannidis”.

    # Autor de ejemplo
    autor_nombre <- "John P. A. Ioannidis"

    # Búsqueda de artículos por el autor
    resultados_autor <- cr_works(query = autor_nombre)

    # Mostrar el número de resultados encontrados
    print(paste("Número de artículos encontrados para", autor_nombre, ":", resultados_autor$meta$total_results))

    ## [1] "Número de artículos encontrados para John P. A. Ioannidis : 3009473"

    # Mostrar títulos de los primeros 5 artículos
    titulos_articulos <- resultados_autor$data$title[1:5]
    print("Títulos de los primeros 5 artículos:")

    ## [1] "Títulos de los primeros 5 artículos:"

    print(titulos_articulos)

    ## [1] "Bias"                                                                                     
    ## [2] "Anticonvulsants for alcohol withdrawal"                                                   
    ## [3] "Transparency in infectious disease research: a meta-research survey of specialty journals"
    ## [4] "Authors’ reply to Pérol and colleagues"                                                   
    ## [5] "John Ioannidis: Uncompromising gentle maniac"

## Paso 5: Guardar los resultados en un DataFrame

Para trabajar con los resultados de forma más cómoda, los convertimos en
un `data.frame`.

    # Convertir los resultados en un data.frame
    articulos_dF <- as.data.frame(resultados_autor$data)
    View(articulos_dF)

## Paso 6: Limpieza de las columnas que contienen listas

Algunas de las columnas en el `data.frame` pueden contener listas o
estructuras complejas, lo que puede dificultar su análisis. Por eso,
vamos a convertir estas listas en texto plano.

### Convertir listas o estructuras complejas a texto

    # Función para convertir listas a texto o NA si está vacío
    convertir_a_texto <- function(columna) {
      sapply(columna, function(x) {
        if (is.list(x)) {
          # Si es una lista, la convertimos a un texto concatenado
          paste(unlist(x), collapse = "; ")
        } else if (length(x) == 0 || is.null(x)) {
          # Si está vacío o es nulo, devolvemos NA
          NA
        } else {
          # Si no es lista, devolver el valor tal cual
          x
        }
      })
    }

    # Aplicar la función a todas las columnas del data.frame
    articulos_dB_limpio <- as.data.frame(lapply(articulos_dF, convertir_a_texto))
    View(articulos_dB_limpio)

## Paso 7: Exportar el DataFrame a un archivo CSV

Finalmente, exportamos el `data.frame` limpio a un archivo CSV para
poder usar los datos posteriormente.

    # Exportar el DataFrame a un archivo CSV
    write.csv(articulos_dB_limpio, "articulos_ioannidis.csv", row.names = FALSE)

------------------------------------------------------------------------

Con esto habrás completado el proceso de extracción y limpieza de
metadatos usando `rcrossref`. Puedes modificar el código según sea
necesario para otros DOIs o autores.
