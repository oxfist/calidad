Evaluar calidad de estimaciones
================

## Generar diseño complejo

Antes de comenzar a usar el paquete `calidad` es necesario declarar el
diseño muestral de la encuesta que se está evaluando, para lo cual
utilizamos el paquete `survey`. En este ejemplo se usa un dataset
reducido de la Encuesta de Presupuestos Familiares, que viene incluido
en el paquete `calidad`.

Es importante declarar cuál es la UPM, el estrato y el factor de
expansión. Toda esta información debería estar contenida en las bases
de datos de las encuestas de hogares. Para un correcto funcionamiento
del paquete, es necesario que las variables de diseño tengan los
siguientes nombres:

  - varunit: Unidad primaria de muestro (UPM)

  - varstrat: estratos

<!-- end list -->

``` r
library(survey)
library(calidad)
dc <- svydesign(ids = ~varunit, strata = ~varstrat, data = epf_personas, weights = ~fe)
dc
```

    ## Stratified 1 - level Cluster Sampling design (with replacement)
    ## With (304) clusters.
    ## svydesign(ids = ~varunit, strata = ~varstrat, data = epf_personas, 
    ##     weights = ~fe)

## Generar insumos para la evaluación

Para evaluar la calidad de una estimación, la [metodología del
INE](https://www.ine.cl/docs/default-source/documentos-de-trabajo/20200318-lineamientos-medidas-de-precisi%C3%B3n.pdf?sfvrsn=f1ab2dbe_4)
establece criterios diferenciados para estimaciones de nivel y
estimaciones de proporción. En el caso estimaciones de nivel se requiere
contar con el tamaño muestral, los grados de libertad y el coeficiente
de variación. Por su parte, la estimaciones de proporción requieren el
tamaño muestral, los grados de libertad y el error estándar.
Adicionalmente, es posible obtener estimaciones de totales
poblacionales, cuyos criterios de calidad son análogos a los de nivel.

El paquete incluye funciones diferenciadas para crear los insumos para
cada uno de los tipo de estimación. El uso de cada uno de ellos es el
siguiente:

``` r
insumos_prop <- crear_insumos_prop(ocupado, zona+sexo, dc)
insumos_media <-  crear_insumos(gastot_hd, zona+sexo, dc)
insumos_total <-  crear_insumos_tot(ocupado, zona+sexo, dc)
```

EL primer argumento corresponde a la variable objetivo. El segundo
argumento corresponde a los dominios de estimación que se quieren
evaluar. En este caso estamos considerando zona y sexo, que deben ir
separados por un signo “+”. El tercer argumento de la función
corresponde al diseño complejo.

Cabe mencionar que el uso por defecto es no desagregar, en cuyo caso las
funciones deben ser utilizadas del siguiente modo:

``` r
insumos_prop <- crear_insumos_prop(ocupado, disenio = dc)
insumos_media <-  crear_insumos(gastot_hd, disenio = dc)
insumos_total <-  crear_insumos_tot(ocupado, disenio = dc)
```

## Generar evaluación

Una vez que se han generado los insumos, podemos hacer la evaluación.
Nuevamente, usamos funciones diferentes para cada uno de los 2 tipos de
estimación.

``` r
evaluacion_prop <- evaluacion_calidad_prop(insumos_prop)
evaluacion_media <- evaluacion_calidad(insumos_media)
evaluacion_media_tot <- evaluacion_calidad(insumos_total)
```

La salida de estas últimas funciones es un `dataframe` que, además de
contener la información ya generada, incluye una columna que indica se
la estimación es poco fiable, fiable o no fiable.
