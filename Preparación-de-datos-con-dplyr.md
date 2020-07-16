Preparación de datos con dplyr
================
Carlos Figueroa 
Junio 2020

### Introducción

*dplyr* es un paquete de R que permite realizar la preparación necesaria
de los datos para su posterior análisis estadístico. Contiene una serie
de funciones bastante sencillas a la lectura que gramaticalmente tienen
bastantes similitudes al lenguaje SQL, proveyendo una serie de verbos
que ayudan a preparar los datos para su análisis.

### Instalación

Para instalar y cargar la librería al ambiente de trabajo, solo es
necesario utilizar las siguientes funciones:

``` r
install.packages('dplyr')
library(dplyr)
```

Primero, para leer y cargar los datos de un archivo con formato .csv
hacemos uso del paquete `readr`.

``` r
iris <- readr::read_csv('data/iris.csv')
```

Esto almacená en nuestro ambiente una variable llamada “iris” que tendrá
una estructura de data frame que es un arreglo que emula el
comportamiento de una matriz de datos en el que las variables se
representan por columnas y las observaciones por filas. Podemos echar un
vistazo a los datos al ejecutar en la consola el nombre del data frame.

``` r
iris
```

    ## # A tibble: 150 x 5
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ##           <dbl>       <dbl>        <dbl>       <dbl> <chr>  
    ##  1          5.1         3.5          1.4         0.2 setosa 
    ##  2          4.9         3            1.4         0.2 setosa 
    ##  3          4.7         3.2          1.3         0.2 setosa 
    ##  4          4.6         3.1          1.5         0.2 setosa 
    ##  5          5           3.6          1.4         0.2 setosa 
    ##  6          5.4         3.9          1.7         0.4 setosa 
    ##  7          4.6         3.4          1.4         0.3 setosa 
    ##  8          5           3.4          1.5         0.2 setosa 
    ##  9          4.4         2.9          1.4         0.2 setosa 
    ## 10          4.9         3.1          1.5         0.1 setosa 
    ## # … with 140 more rows

### Verbos principales

Los verbos principales de `dplyr` permiten manejar las estructuras de
datos con una sintaxis fácilmente comprensible que busca asemejarse a la
descripción del procesamiento de los datos antes de convertirse en
macrodatos. Los verbos o funciones más importantes son los siguientes:

#### Función `select()`:

Permite seleccionar o dejar seleccionar columnas específicas de acuerdo
a sus nombres en el orden en el que sean expresados los argumentos de la
función inmediatamente siguientes al data frame objetivo que es
expresado como primer argumento. Podría establecerse una equivalencia en
SQL con la sentencia `SELECT`.

``` r
library(dplyr)
select(iris, Species, Sepal.Length)
```

    ## # A tibble: 150 x 2
    ##    Species Sepal.Length
    ##    <chr>          <dbl>
    ##  1 setosa           5.1
    ##  2 setosa           4.9
    ##  3 setosa           4.7
    ##  4 setosa           4.6
    ##  5 setosa           5  
    ##  6 setosa           5.4
    ##  7 setosa           4.6
    ##  8 setosa           5  
    ##  9 setosa           4.4
    ## 10 setosa           4.9
    ## # … with 140 more rows

También, es posible seleccionar todas las columnas excepto las que sean
especificadas agregando un `-` en las variables que se desean excluir.
Por ejemplo:

``` r
library(dplyr)
select(iris, -Species, -Sepal.Length)
```

    ## # A tibble: 150 x 3
    ##    Sepal.Width Petal.Length Petal.Width
    ##          <dbl>        <dbl>       <dbl>
    ##  1         3.5          1.4         0.2
    ##  2         3            1.4         0.2
    ##  3         3.2          1.3         0.2
    ##  4         3.1          1.5         0.2
    ##  5         3.6          1.4         0.2
    ##  6         3.9          1.7         0.4
    ##  7         3.4          1.4         0.3
    ##  8         3.4          1.5         0.2
    ##  9         2.9          1.4         0.2
    ## 10         3.1          1.5         0.1
    ## # … with 140 more rows

#### Función `filter()`:

Permite mostrar o dejar de mostrar registros o filas en el dataframe que
cumplan con una expresión lógica, en donde se mostrarán los valores que
cumplan con esa condición. Puede establecerse una equivalencia en SQL
con la sentencia `WHERE`. El segundo argumento será constituido por la
expresión lógica que deben cumplir los registros para ser mostrados,
para la cual se utilizarán los operadores lógicos para realizar
diferentes combinaciones de posibles valores. Los operadores lógicos
son:

  - Disyunción inclusiva (ó): Representado con el caracter `|`.
  - Conjunción (y): Representado por el caracter `&`.

Luego, es posible utilizar los operadores relacionales para evaluar si
las variables pertenecen a determinados intervalos o si toman
determinados valores puntuales. Es posible utilizar los siguientes
operadores realaciones.

  - Igual que / Diferente de: Representado por `==` ó `!=`
  - Mayor que / menor que: Representados por `>` ó `<`.
  - Mayor o igual que / Menor o igual que: Representado por `>=` ó `<=`.

Por ejemplo, si quisiéramos seleccionar solo las variables “Species” del
tipo “vesicolor” y las flores cuyas “Petal.Lenght” sean mayor a 4.9,
tendríamos entonces:

``` r
filter(iris, Species=='versicolor' & Petal.Length > 4.9)
```

    ## # A tibble: 2 x 5
    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species   
    ##          <dbl>       <dbl>        <dbl>       <dbl> <chr>     
    ## 1          6.7         3            5           1.7 versicolor
    ## 2          6           2.7          5.1         1.6 versicolor

#### Función `group_by()`:

Permite agrupar todas las observaciones del data frame de acuerdo a las
variables categóricas que se especifique. Esta función por lo general es
utilizada en la práctica de manera conjunta con una función de
agregación como lo son, por ejemplo `sum()`, `sd()`, `mean()`, entre
otras.

#### Función `summarise()`:

Como su nombre lo indica, sumariza múltiples valores en medidas de
resumen como lo son las estadísticas descriptivas. Puede ser utilizada
por sí sola o seguda de la función `group_by()` para calcular
estadísticas según las variables categóricas que se señalen.

Por ejemplo, si quisiéramos conocer cuál es el promedio del ancho del
pétalo de las rosas y cuántas diferentes especies hay:

``` r
summarise(iris, 
          mean_petal_width=mean(Petal.Width), count_distinct_species=n_distinct(Species)
          )
```

    ## # A tibble: 1 x 2
    ##   mean_petal_width count_distinct_species
    ##              <dbl>                  <int>
    ## 1             1.20                      3

Otro ejemplo está en si se quisiera conocer cuál es el promedio de la
longitud del pétalo para cada uno de los tipos de especies:

``` r
summarise(group_by(iris, Species), mean_petal_length=mean(Petal.Length))
```

    ## # A tibble: 3 x 2
    ##   Species    mean_petal_length
    ##   <chr>                  <dbl>
    ## 1 setosa                  1.46
    ## 2 versicolor              4.26
    ## 3 virginica               5.55

#### Función `arrange()`

Utilizada para ordenar registros de acuerdo a valores de una o varias
variables. Los valores son ordenados de acuerdo en el orden en el que
son definidos los argumentos de la función `arrange`, y por defecto, son
ordenados de manera ascendente.

Si utilizáramos el ejemplo anterior para conocer cuál es la especie cuya
longitud del pétalo es menor, podríamos utilizar.

``` r
arrange(
  summarise(group_by(iris, Species), 
            mean_petal_length=mean(Petal.Length)
            ), 
  mean_petal_length
  )
```

    ## # A tibble: 3 x 2
    ##   Species    mean_petal_length
    ##   <chr>                  <dbl>
    ## 1 setosa                  1.46
    ## 2 versicolor              4.26
    ## 3 virginica               5.55

Por otro lado, también es posible utilizar la función `dplyr::top_n()`
cuyo argumento mostrará las primeras filas de un data frame.

#### Función`mutate()`:

Añade una nueva variable o columna al data frame basada en aplicar
funciones a las variables existentes sin que esto represente la
agregación de los registros.

Por ejemplo, si quisiéramos crear una variable llamada `greater_smaller`
que tome el valor “Greater than 4.6” cuando la variable Sepal.Lenght sea
mayor que 4.6 y que tome el valor “Other case” en cualquier otro caso,
podríamos hacer uso de:

``` r
mutate(
  iris, greater_smaller=case_when(
    Sepal.Length > 4.6 ~ 'Greater than 4.6',
    TRUE ~ 'Other case')
)
```

    ## # A tibble: 150 x 6
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species greater_smaller 
    ##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <chr>           
    ##  1          5.1         3.5          1.4         0.2 setosa  Greater than 4.6
    ##  2          4.9         3            1.4         0.2 setosa  Greater than 4.6
    ##  3          4.7         3.2          1.3         0.2 setosa  Greater than 4.6
    ##  4          4.6         3.1          1.5         0.2 setosa  Other case      
    ##  5          5           3.6          1.4         0.2 setosa  Greater than 4.6
    ##  6          5.4         3.9          1.7         0.4 setosa  Greater than 4.6
    ##  7          4.6         3.4          1.4         0.3 setosa  Other case      
    ##  8          5           3.4          1.5         0.2 setosa  Greater than 4.6
    ##  9          4.4         2.9          1.4         0.2 setosa  Other case      
    ## 10          4.9         3.1          1.5         0.1 setosa  Greater than 4.6
    ## # … with 140 more rows

Observemos que en este último ejemplo en particular, comenzamos a tener
ciertos problemas para leer las funciones que están siendo ejecutadas
debido las funciones anidadas. Las funciones anidadas son especialmente
frecuentes en softwares como Microsoft Excel, en las que se pueden
utilizar funciones como argumentod de otras funciones. Esto representa
un verdadero problema de legibilidad en el código, dificultando su
mantenimiento futuro. `dplyr` provee de una herramienta que elimina las
funciones anidadas, mejorando considerablemente la lectura del código.

#### Pipes (tuberías):

El operador de tubería “pipe” `%>%` un operador del paquete `magrittr` y
es cargado automáticamente al ejecutar `library(dplyr)` en el ambiente
de trabajo. Permite conectar múltiples funciones y hacerlas funcionar de
manera conjunta. Es particularmente útil para evitar la anidación de
funciones.

Su funcionamiento consiste en pasar como primer argumento de la función
a la derecha del operador, el colocado a la izquierda del mismo. Es
decir, de manera matemática:

1.  `x %>% f(y)` es equivalente a `f(x, y)`
2.  `x %>% f(y) %>% g(z)` es equivalente a `g(f(x, y), z)`

Entonces, en caso del ejemplo anterior en el que se utilizó la función
`mutate()`, el código puede escribirse de una mejor forma:

``` r
iris %>% 
  mutate(
    greater_smaller=case_when(
    Sepal.Length > 4.6 ~ 'Greater than 4.6',
    TRUE ~ 'Other case')
  )
```

    ## # A tibble: 150 x 6
    ##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species greater_smaller 
    ##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <chr>           
    ##  1          5.1         3.5          1.4         0.2 setosa  Greater than 4.6
    ##  2          4.9         3            1.4         0.2 setosa  Greater than 4.6
    ##  3          4.7         3.2          1.3         0.2 setosa  Greater than 4.6
    ##  4          4.6         3.1          1.5         0.2 setosa  Other case      
    ##  5          5           3.6          1.4         0.2 setosa  Greater than 4.6
    ##  6          5.4         3.9          1.7         0.4 setosa  Greater than 4.6
    ##  7          4.6         3.4          1.4         0.3 setosa  Other case      
    ##  8          5           3.4          1.5         0.2 setosa  Greater than 4.6
    ##  9          4.4         2.9          1.4         0.2 setosa  Other case      
    ## 10          4.9         3.1          1.5         0.1 setosa  Greater than 4.6
    ## # … with 140 more rows

Otro ejemplo estaría en el caso de que se quisieran conocer para cada
tipo de especie de flores el total de observaciones, la media de la
anchura del pétalo, así como la desviación estándar de la longitud del
pétalo:

``` r
iris %>% 
  select(Species, Petal.Width, Petal.Length) %>% 
  group_by(Species) %>% 
  summarise(
    total=n(), 
    mean_petal_width=mean(Petal.Width), 
    sd_petal_length=sd(Petal.Length)
    ) %>% 
  arrange(-mean_petal_width) # Utilizamos el - para ordenar de forma descendente
```

    ## # A tibble: 3 x 4
    ##   Species    total mean_petal_width sd_petal_length
    ##   <chr>      <int>            <dbl>           <dbl>
    ## 1 virginica     50            2.03            0.552
    ## 2 versicolor    50            1.33            0.470
    ## 3 setosa        50            0.246           0.174

### Conclusión

`dplyr` no solo ofrece funciones bastante útiles sino que también prevee
operadores que mejoran considerablemente la lectura del código,
haciéndolo más entendible y parecido al la manera en la que es manejada
la sintaxis de SQL. El uso de una combinación de las funciones de
`dplyr` con el operador `%>%` se convierte en sí misma una sintaxis
propia que permite al usuario programar mientras mentalmente describe
con la sintaxis el procesamiento realizado con los datos. Esto supone
amplias ventajas sobre otros lenguajes en los que, por ejemplo, hubiese
sido necesario utilizar bucles u otras herramientas más complejas para
lograr estos objetivos.
