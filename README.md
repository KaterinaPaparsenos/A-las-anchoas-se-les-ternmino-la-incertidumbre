# A las anchoas se les ternminó la incertidumbre.

Autoras: Irene Extremera Serrano & Caterina O. Paparsenos Fernández

***

Este repositorio contiene información más detallada respecto al poster que hemos presentado en las V Jornadas Científicas de Estudiantes de la SEB realizadas en enero 2021.

En el poster hablamos sobre que son los modelos de distribución de especies (SDM) y cual es su finalidad. También, mostramos algunos modelos realizados por nosotras, tanto con métodos frecuentistas como con bayesianos, con el fin de determinar la actual destribución espacial de la anchoa europea (_Engraulis encrasicolus_) en el Atlántico norte y Mediterráneo. Hacemos una comparativa entre estos modelos para ver cual realiza mejores predicciones y, finalmente, realizamos predicciones para observar el impacto que tendrá el cambio climático sobre la distribución de dicha especie.

Esta pagina junto con el poster son una pequeña parte de nuestros trabajos fin de master. Hemos empezado hace poco, por lo que nos queda mucho por desarrollar y mejorar. Cualquier sugerencia o idea respecto al tema será bienvenida.

***


# ¿PORQUE ESTUDIAR LA DISTRIBUCIÓN DE LAS ANCHOAS? :

Los modelos de distribución de especies (SDM) son el principal método de modelización en ecología cuantitativa. Se utilizan principalmente para estudiar la distribución geográfica de las especies de nuestro planeta, así como para evaluar el impacto que tendrá el cambio climático en sus distribuciones.

La dificultad de los SDMs es encontrar métodos que consigan vincular de manera eficiente la información que se tiene de las especies con las variables ambientales para poder  predecir dónde es (o cuanto de) probable (es) que estén presentes en lugares o periodos de tiempo no muestreados. Normalmente se utilizan modelos complejos donde se encuentran problemas como:
- exceso de ceros
- no linealidad de la variables explixatorias
- varianza no constante
- correlación espacio-temporal 

La capacidad de adaptación a los cambios ambientales varía mucho entre los diferentes organismos. Las distribuciones de las especies más sensibles se verán muy afectadas por el continuo calentamiento del planeta, mientras que muchas llegarán a extinguirse.

La anchoa europea (Engraulis encrasicolus) es una de las especies marinas más sensibles a las fluctuaciones del medio ambiente. Es una especie pelágica que se puede encontrar en todo el Atlántico nororiental, Mediterráneo y Mar Negro. Es una especie extremadamente sensible a los cambios ambientales y presenta gran variabilidad ambiental a escala regional. Se trata de una especie de alto interés ecológico y comercial ya que no solo sirve para el abastecimiento humano sino como carnada en otras pesquerías. 

En nuestro póster, nosotras evaluamos cómo afectará el cambio climático en la distribución de la anchoa europea en el Atlántico norte y el Mediterráneo. Primero calculamos distintos modelos de distribución de especies (SDMs) con diferentes enfoques estadísticos, frecuentista y bayesiano, para determinar su actual posición geográfica. Luego proyectamos la posible futura distribución de esta especie para el futuro escenario RCP 4.5 en el cual se propone que la temperatura global aumentará aproximadamente 1.4 oC y las emisiones de los gases de efecto invernadero empezarán a disminuir a partir del 2040.

# ¿DE DONDE SACAMOS NUESTROS DATOS? 

Por un lado, los datos de presencias de anchoas en Atlántico Norte y Mediterraneo los hemos obtenido de la página [AQUAMAPS]( https://www.aquamaps.org/receive.php?type_of_map=regular)

Habría que clickar en “Point map” que debería llevar a una opción de descarga de datos en diversos formatos (nosotras hemos utilizado .csv)
La última vez que tratamos de acceder la página no cargaba por lo que no hemos podido adjuntar imágenes para explicar de forma mas ilustrativa cómo hacer la descarga. En caso de no poder acceder a los datos pueden escribirnos a kateopap@gmail.com o a irenethinker17@gmail.com.

Las pseudoausencias se generaron con la siguiente semilla “141592” y se adjuntaron a la base de datos final.   

Por otro lado, las variables ambientales utilizadas para la creación de los modelos fueron: batimetría (bathy), temperatura media superficial (tempmean), oxígeno disuelto (odismean), salinidad superficial media (salinity), producción primaria media (ppmean) y clorofila media (chlomean). Este preselección se basó en lo que se sabe sobre la ecología de la anchoa. Todas se obtuvieron desde Bio-Oracle a excepción de batimetría que la cogimos de MARSPEC. Fueron descargadas directamente desde Rstudio. Estas variables son el resultado de hacer una media de diez años para estos valores.

La base de datos final incluía dos columnas correspondientes a las coordenadas de las presencias y pseudoausencias, otra compuesta por ceros y uno que indicaba si eran presencias o pseudoausencias, y el resto eran seis columnas que correspondían al valor de las covariables asociadas a las distintas coordenadas.

Y finalmente, las variables utilizadas para realizar predicciones futuras fueron obtenidas de (METER AQUÍ LO DE CATERINA)

Una vez que tuvimos todas las variables, nos quedamos únicamente con las presencias de la zona de Estudio excluyendo Mar Muerto y Mar Egeo. Con respecto a las covariables recortamos los ráster para trabajar solo en el cuadrante de -25º a 40º en longitud y de 30º a 65º de latitud.

# SELECCIÓN DE VARIABLES

En cuanto a la selección de covariables primero miramos la correlación entre ellas.
(/Imágenes/correlación.jpg)

Se aprecia sobre todo que hay una alta correlación entre ppmean con tempmean y salinity. 
Con el fin de ver cómo es la forma en la cual se relacionan las variables entre si para valorar la posibilidad de en un futuro realizar o no un modelo aditivo generalizado (gam).
(https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/blob/main/Im%C3%A1genes/Gpairs.jpg)

A la vista de que la relación entre las covariables no es para nada lineal decidimos decantarnos por realizar modelos lineales generalizados (glm, tanto en frecuentista como en INLA) y, más adelante probar ajustando con un gam.
Posteriormente para poder decantarnos finalmente con qué variables quedarnos para comenzar a ajustar los modelos miramos el GVIF. Primero introducimos  las seis variables juntas y a continuación fuimos, una a una, quitando las que tenían un GVIF mas alto. El resultado final fue el siguiente:
(https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/blob/main/Im%C3%A1genes/Tabla.png)

# ¿QUE MODELOS HEMOS UTILIZADO? 

El objetivo del TFM de una de nosotras consiste en la realización de distintos modelos estadísticos de distribución espacial con el fin de realizar una comparación entre ellos en cuanto a calidad predictiva. Durante este periodo de tiempo solo ha dado tiempo a realizar cuatro modelos que son los que hemos realizado en el póster presentado y los cuales explicamos a continuación.

#### MODELO 1: BIOCLIM

Es un modelo de distribución de especies que solo usa datos de presencia (PO). El modelo crea una caja que incluye los valores mínimos y máximos de los ejes X e Y. Para calcular la probabilidad de ocurrencia de una especie en ese espacio, se compara los valores de las variables ambientales de las localizaciones conocidas con el percentil 50 de la distribución de valores de las localizaciones que se conocen. Cuanto mas cerca esté el valor del percentil 50, mayor es la probabilidad de ocurrencia en esa localización.

Las ventajas de este modelo es que:
-	Simple e intuitivo
-	Solo necesita presencias
-	Da un ranking de variables ambientales
-	Es útil enseñando modelos de distribución de especies

Los inconvenientes que tiene son:
-	No tiene en cuenta la interacción de los predictores
-	No puede usar variables categóricas
-	No da intervalos de confianza
-	Susceptible a la sobrepredicción debido a los outliers

Para la realización de este modelo se utilizó el paquete dismo. De todos los modelos fue el más rápido y sencillo de realizar.

#### MODELO 2: MAXENT

Para la realización de este modelo se utilizó el paquete dismo. También fue sencillo de realizar, aunque tardó un poco mas en ejecutarse. Sin embargo, es ligeramente mas complejo que BIOCLIM y difícil de interpretar.

Es un modelo que usa métodos de máxima entropía para entender la distribución de probabilidad de la variable respuesta. La forma que tiene de buscar la distribución de probabilidad es teniendo en cuenta los límites de las variables ambientales de las localizaciones conocidas,  posteriormente busca la distribución lo más dispersa que se acerque a la uniforme a excepción de los puntos de presencia.
La forma que tiene de funcionar es partiendo de la hipótesis de máxima entropía (de ahí lo de MAXENT XD). Lo  primero que hace el modelo es darle una distribución de probabilidad a todos los sucesos de 0.5.

A continuación realiza una serie de restricciones para perfilar esas probabilidades. El algoritmo extrae la función de densidad de todo el territorio en el que trabajamos. Lo mismo hace con los valores de la variable ambiental en los puntos de presencia. Posteriormente de ambas funciones de densidad calcula mediante unas features (se explica que son mas adelante) la función de densidad para la ocurrencia de la especie.
El algoritmo compara los datos de las variables ambientales de los puntos de presencia con todos los ambientes disponibles de la región de estudio (la que incluye las presencias).  Además se generan otros puntos (background points) que pueden incluir zonas en las que sé que se sabe que va a haber una ocurrencia y zonas en las que no (zona mayor que incluye la región de estudio). Estos puntos no son lo mismo que pseudoausencias. Los valores de las variables ambientales en BACKGROUND tienen por lo general un rango mayor y eso hace que la hipótesis de partida sobre la que trabaja MAXENT cambie. Por lo que el algoritmo va a cambiar en función de la extensión de terreno que elijamos. 

Como hemos mencionado antes, las features son transformaciones y combinaciones de las variables. Hay hasta 6 combinaciones: lineal, cuadrática, producto, categorica, hinge y threshold. MAXENT lo hace por defecto pero no me dice cuáles usa y cómo lo hace. Las usa para tener mas variables con las que ajustar el modelo. De todas las features que hay, las que tienen un efecto mas grande es hinge y threshold, que pueden hacer que la función que explica la relación entre la variable respuesta y la covariable pueda caer en picado en algún momento (random forest hace algo similar).

Para evitar el sobreajuste el modelo realiza las siguientes acciones:

-	 Relaja las restricciones de las medias y varianzas de las variables ambientales teniendo en consideración los intervalos de confianza alrededor de estas restricciones. Esto previene que el modelo se ajuste demasiado a los datos.
-	 Penaliza la complejidad de las características que no generan una mejoría en el modelo (lambdas). 
Usando un buen valor de lambda las predicciones pueden asemejarse a las de un GLM.

Para finalizar, una vez calculadas estas funciones de densidad MAXENT calcula el ratio entre estas dos funciones de densidad obteniendo la función de densidad óptima para una especie en cada punto del area de estudio. El output que devuelve es una transformación logística para facilitar la interpretación de los resultados en cuanto a la probabilidad del evento.

Las ventajas del uso de MAXENT:
- Solo usa datos de presencias y puede usar pocos puntos de presencia
- Permite usar variables categóricas y continuas
- Incluye interacciones entre variables predictoras
- Incluye un protocolo que impide el sobreajuste
- Por lo general las predicciones son buenas

En cuanto a los inconvenientes que hemos encontrado usando la función del paquete dismo de Rstudio:
- No se puede saber qué features ha utilizado.
- No se puede cambiar el valor de lambda.
- No se puede cambiar la probabilidad inicial de 0.5.
- Tampoco se puede modificar las áreas de estudio y el área total que coge para realizar el ajuste.
E
n resumen, MAXENT en Rstudio, al menos con el paquete que hemos utilizado es una caja muy negra. Sin embargo, es bastante rápido y sencillo en comparación con otros modelos y la calidad predictiva es bastante alta.
De cara al futuro estamos valorando el utilizar otro paquete o un tipo de software para poder tener un control sobre todo lo comentado en inconvenientes.

#### MODELO 3: MODELO LINEAL GENERALIZADO (GLM)


#### MODELO 4: GEOESPACIAL BAYESIANO


# NUETROS RESULTADOS:

# ¿QUE HEMOS OBTENIDO DE TODO ESTO? 



