# A las anchoas se les ternminó la incertidumbre.

Autoras: Irene Extremera Serrano & Caterina O. Paparsenos Fernández

***

En el poster hablamos sobre que son los modelos de distribución de especies (SDMs) y cual es su finalidad. También, mostramos algunos modelos realizados por nosotras, tanto con métodos frecuentistas como con bayesianos, con el fin de determinar la actual destribución espacial de la anchoa europea (_Engraulis encrasicolus_) en el Atlántico norte y Mediterráneo. Hacemos una comparativa entre estos modelos para ver cual realiza mejores predicciones y, finalmente, realizamos predicciones para observar el impacto que tendrá el cambio climático sobre la distribución de dicha especie.

Esta pagina junto con el poster son una pequeña parte de nuestros trabajos fin de master. Hemos empezado hace poco, por lo que nos queda mucho por desarrollar y mejorar. Cualquier sugerencia o idea respecto al tema será bienvenida.

En caso de dudas, sugerencias u otra cosa, pueden escribirnos a kateopap@gmail.com o a irenethinker17@gmail.com.

***


# ¿PORQUE ESTUDIAR LA DISTRIBUCIÓN DE LAS ANCHOAS? :

Los modelos de distribución de especies (SDM) son el principal método de modelización en ecología cuantitativa. Se utilizan principalmente para estudiar la distribución geográfica de las especies de nuestro planeta, así como para evaluar el impacto que tendrá el cambio climático en sus distribuciones.

La dificultad de los SDMs es encontrar métodos que consigan vincular de manera eficiente la información que se tiene de las especies con las variables ambientales para poder predecir dónde es (o cuanto de) probable (es) que estén presentes en lugares o periodos de tiempo no muestreados. Normalmente se utilizan modelos complejos donde se encuentran problemas como:
- Exceso de ceros
- No linealidad de la variables explicativas
- Varianza no constante
- Correlación espacio-temporal 

La capacidad de adaptación a los cambios ambientales varía mucho entre los diferentes organismos. Las distribuciones de las especies más sensibles se verán muy afectadas por el continuo calentamiento del planeta, mientras que muchas llegarán a extinguirse.

La anchoa europea (Engraulis encrasicolus) es una de las especies marinas más sensibles a las fluctuaciones del medio ambiente. Es una especie pelágica que se puede encontrar en todo el Atlántico nororiental, Mediterráneo, Mar Negro y en la costa oeste de África. Es una especie extremadamente sensible a los cambios ambientales y presenta gran variabilidad ambiental a escala regional. Se trata de una especie de alto interés ecológico y comercial ya que no solo sirve para el abastecimiento humano sino como carnada en otras pesquerías. 

En nuestro póster, nosotras evaluamos cómo afectará el cambio climático en la distribución de la anchoa europea en el Atlántico norte y el Mediterráneo. Primero calculamos distintos modelos de distribución de especies (SDMs) con diferentes enfoques estadísticos, frecuentista y bayesiano, para determinar su actual posición geográfica. Luego proyectamos la posible futura distribución de esta especie para el escenario RCP 4.5 en el cual se propone que la temperatura global aumentará aproximadamente 1.4 oC y las emisiones de los gases de efecto invernadero empezarán a disminuir a partir del 2040.

# ¿DE DONDE SACAMOS NUESTROS DATOS? 

Por un lado, los datos de presencias de anchoas en Atlántico Norte y Mediterraneo los hemos obtenido de la página [AQUAMAPS]( https://www.aquamaps.org/receive.php?type_of_map=regular)

Habría que clickar en “Point map” que debería llevar a una opción de descarga de datos en diversos formatos (nosotras hemos utilizado .csv). Una vez que introducimos el archivo en Rstudio, nos quedamos únicamente con las presencias de Atlántico Norte y Mediteraneo (no contamos con el mar Egeo ni el mar Muerto).
La última vez que tratamos de acceder la página no cargaba por lo que no hemos podido adjuntar imágenes para explicar de forma mas ilustrativa cómo hacer la descarga.

Las pseudoausencias se generaron con la siguiente semilla “141592” y se adjuntaron a la base de datos final.   

Por otro lado, las variables ambientales utilizadas para la creación de los modelos fueron: batimetría (bathy), temperatura media superficial (tempmean), oxígeno disuelto (odismean), salinidad superficial media (salinity), producción primaria media (ppmean) y clorofila media (chlomean). Esta preselección se basó en lo que se sabe sobre la ecología de la anchoa. Todas se obtuvieron, usando Rstudio, desde las bases de datos online Bio-Oracle a excepción de batimetría que la cogimos de MARSPEC.  Estas variables son el resultado de hacer una media de diez años.

La base de datos final incluía dos columnas correspondientes a las coordenadas de las presencias y pseudoausencias, otra compuesta por ceros y unos que indicaba si eran presencias o pseudoausencias (variable respuesta), y el resto eran seis columnas que correspondían al valor de las covariables asociadas a las distintas coordenadas.

Y finalmente, las variables utilizadas para realizar predicciones futuras fueron obtenidas de (METER AQUÍ LO DE CATERINA)

Con respecto a las covariables recortamos los ráster para trabajar solo en el cuadrante de -25º a 40º en longitud y de 30º a 65º de latitud.

# SELECCIÓN DE VARIABLES

En cuanto a la selección de covariables primero miramos la correlación entre ellas.
![Diagrama de correlaciones](/Imágenes/correlación.jpg)

Se aprecia sobre todo que hay una alta correlación entre ppmean con tempmean y salinity. 
Con el fin de ver cómo es la forma en la cual se relacionan las covariables entre si para valorar la posibilidad de en un futuro realizar o no un modelo aditivo generalizado (gam).
![Gpairs](.../Imágenes/Gpairs.jpg)

A la vista de que la relación entre las covariables no es para nada lineal, decidimos decantarnos por realizar modelos lineales generalizados (glms, tanto en frecuentista como en INLA) debido a que la varaible respuesta sigue una distribución binomial  y, más adelante probar ajustando con un gam.
Posteriormente para poder decantarnos finalmente con qué variables quedarnos para comenzar a ajustar los modelos miramos el GVIF. Primero introducimos las seis variables juntas y a continuación fuimos, una a una, quitando las que tenían un GVIF mas alto. El resultado final fue el siguiente:

![Tabla comparacion de variables ambientales](/Im%C3%A1genes/Tabla.png)

# ¿QUE MODELOS HEMOS UTILIZADO? 

El objetivo del TFM de una de nosotras consiste en la realización de distintos modelos estadísticos de distribución espacial con el fin de realizar una comparación entre ellos en cuanto a calidad predictiva. Durante este periodo de tiempo solo ha dado tiempo a realizar cuatro modelos que son los que hemos realizado en el póster presentado y los cuales explicamos a continuación.

(Si saben algo más acerca de los modelos que aquí describimos agradeceriamos que nos lo comentasen durante la presentación del póster o en caso contrario enviasen un mail a irenethinker17@gmail.com)

#### MODELO 1: BIOCLIM

Es un modelo de distribución de especies que solo usa datos de presencia (PO). El modelo crea una caja que incluye los valores mínimos y máximos de los ejes X e Y. Para calcular la probabilidad de ocurrencia de una especie en ese espacio, se comparan los valores de las variables ambientales de las localizaciones conocidas con el percentil 50 de la distribución de valores de las localizaciones que se conocen. Cuanto mas cerca esté el valor del percentil 50, mayor es la probabilidad de ocurrencia en esa localización.

Las ventajas de este modelo son:
-	Simple e intuitivo
-	Solo necesita presencias
-	Es útil enseñando modelos de distribución de especies

Los inconvenientes que tiene son:
-	No tiene en cuenta la interacción de los predictores
-	No puede usar variables categóricas
-	No da intervalos de confianza
-	Susceptible a la sobrepredicción debido a los outliers

Para la realización de este modelo se utilizó el paquete dismo. De todos los modelos fue el más rápido y sencillo de realizar.

#### MODELO 2: MAXENT

Para la realización de este modelo se utilizó también el paquete dismo. Al igual que el anterior, fue sencillo de realizar aunque tardó un poco mas en ejecutarse. Sin embargo, es ligeramente mas complejo que BIOCLIM y difícil de interpretar.

Es un modelo que usa métodos de máxima entropía para calcular la distribución de probabilidad de la variable respuesta. La forma que tiene de funcionar es partiendo de la hipótesis de máxima entropía (de ahí lo de MAXENT XD). Lo  primero que hace el modelo es darle una distribución de probabilidad a todos los sucesos de 0.5.

A continuación realiza una serie de restricciones para perfilar esas probabilidades. El algoritmo extrae la función de densidad de todo el territorio en el que trabajamos. Lo mismo hace con los valores de la variable ambiental en los puntos de presencia. Posteriormente de ambas funciones de densidad calcula mediante unas features (se explica que son mas adelante) la función de densidad para la ocurrencia de la especie.
El algoritmo compara los datos de las variables ambientales de los puntos de presencia con todos los ambientes disponibles de la región de estudio (la que incluye las presencias).  Además se generan otros puntos (background points) que pueden incluir zonas en las que sé que se sabe que va a haber una ocurrencia y zonas en las que no (zona mayor que incluye la región de estudio). Estos puntos no son lo mismo que pseudoausencias. Los valores de las variables ambientales en BACKGROUND tienen por lo general un rango mayor y eso hace que la hipótesis de partida sobre la que trabaja MAXENT cambie. Por lo que el algoritmo va a cambiar en función de la extensión de terreno que elijamos. 

Como hemos mencionado antes, las features son transformaciones y combinaciones de las variables. Hay hasta 6 combinaciones: lineal, cuadrática, producto, categorica, hinge y threshold. MAXENT lo hace por defecto pero no dice cuáles usa y cómo lo hace. Las usa para tener mas variables con las que ajustar el modelo. De todas las features que hay, las que tienen un efecto mas grande es hinge y threshold, que pueden hacer que la función que explica la relación entre la variable respuesta y la covariable pueda caer en picado en algún momento (random forest hace algo similar).

Para evitar el sobreajuste el modelo realiza las siguientes acciones:

-	 Relaja las restricciones de las medias y varianzas de las variables ambientales teniendo en consideración los intervalos de confianza alrededor de estas restricciones. Esto previene que el modelo se ajuste demasiado a los datos.
-	 Penaliza la complejidad de las características que no generan una mejoría en el modelo (lambdas). 
Usando un buen valor de lambda las predicciones pueden asemejarse a las de un GLM.

Para terminar, una vez calculadas estas funciones de densidad MAXENT calcula el ratio entre estas dos funciones de densidad obteniendo la función de densidad óptima para una especie en cada punto del area de estudio. El output que devuelve es una transformación logística para facilitar la interpretación de los resultados en cuanto a la probabilidad del evento.

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

En resumen, MAXENT en Rstudio, al menos con el paquete que hemos utilizado es una caja muy negra. Aun no tenemos del todo claro como trabaja el modelo por dentro pero estamos en ello. Sin embargo, es bastante rápido y sencillo en comparación con otros modelos y la calidad predictiva es bastante alta.
De cara al futuro estamos valorando el utilizar otro paquete o un tipo de software para poder tener un control sobre todo lo comentado en inconvenientes.

#### MODELO 3: MODELO LINEAL GENERALIZADO (GLM)


#### MODELO 4: MODELO GEOESPACIAL BAYESIANO:

Nuestros datos de presencia y pseudoausencia son datos geoestadísticos (observaciones de todo un espacio continuo) que provienen de un espacio abierto, por lo que, se ven influenciados de los datos vecinos. Esto significa que existe una autocorrelación espacial entre nuestros datos que no se ha incluido en los modelos previos. Al realizar un modelo geoestadístico se asume que las observaciones están correlacionadas y que las observaciones más cercanas son mucho más similares respecto a las que están más lejos.

Para la estimación de este modelo hemos utilizado el software R-INLA ya que nos permite utilizar la estructura de los Modelos Jerárquicos Bayesianos y realizar simulaciones más rápidas que con el MCMC. 

Por lo tanto, el modelo que hemos utilizado para estimar la distribución de la anchoa europea es el que se muestra en el diagrama a continuación: (diagrama de INLA)

![Diagrama del modelo relizado (estilo Kruschke)](https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/blob/main/Im%C3%A1genes/INLA_krusche_diagram.png)

Como se trata de un modelo Jerárquico Bayesiano, tiene varios niveles:

**Nivel 1**: _Verosimilitud_

Se trata de la función de verosimilitud condicionalmente independiente en la cual nuestra variable respuesta ![y_i](https://latex.codecogs.com/gif.latex?y_i), dado nuestros datos, sigue una distribución ![Ber(\pi](https://latex.codecogs.com/gif.latex?%7E%20Ber%28%5Cpi%29)
(1 es la presencia y 0 la pseudoausencia). Como función de enlace hemos utilizado la función _logit_. Por lo tanto, la probabilidad de ocurrencia se calculará de la siguiente manera:
![logit(\pi)= \alpha+ \beta_i X_i+ W_i](https://latex.codecogs.com/gif.latex?logit%28%5Cpi%29%3D%20%5Calpha&plus;%20%5Cbeta_i%20X_i&plus;%20W_i),

donde ![\alpha= intercept](https://latex.codecogs.com/gif.latex?%5Calpha%3D%20intercept) y
![\beta_i X_i](https://latex.codecogs.com/gif.latex?%5Cbeta_i%20X_i%3D ) y ![W_i](https://latex.codecogs.com/gif.latex?W_i) son el predictor fijo linear y el efecto aleatorio espacial para cada observación i, respectivamente. 

**Nivel 2**: _Campo Latente Gausiano_

*	Efectos fijos: 

Consideramos que el predictor lineal sigue una distribución normal de parámetros ![mi](https://latex.codecogs.com/gif.latex?%5Cmu) y ![\sigma^2](https://latex.codecogs.com/gif.latex?%5Csigma%5E2). Como distribución a priori para el predictor lineal, hemos utilizado el que viene por defecto en INLA que es una distribución Gausiana N(0,100)

* Efecto aleatorio:

El efecto aleatorio especial _W_ es una función isotrópica de covarianza Matérn y se asume que sigue una distribución Gaussiana multivariante donde su matriz de covarianza depende de las distancias entre las observaciones y los hyperparametros varianza (![\sigma^2](https://latex.codecogs.com/gif.latex?%5Csigma%5E2)) y rango (![fi](https://latex.codecogs.com/gif.latex?%5Cphi)) (es la distancia a partir de la cual dos observaciones dejan de estar correlacionadas): 
![W_i](https://latex.codecogs.com/gif.latex?W_i) ~ ![N(0, \sigma^2 H(\phi)](https://latex.codecogs.com/gif.latex?N%280%2C%20%5Csigma%5E2%20H%28%5Cphi%29)

A nivel computacional, esta matriz es muy difícil de calcular, pero al utilizar el método INLA se utiliza el **SPDE** (Stochastic Partial Differential Equation approach) para poder llegar a calcular esa matriz de covarianza de manera indirecta. EL SPDE lo que hace es reparametrizar esta matriz con otros dos parámetros ![$\kappa$](https://latex.codecogs.com/gif.latex?%5Ckappa) y ![$\tau$](https://latex.codecogs.com/gif.latex?%5Ctau), tal que así: 
![W_i](https://latex.codecogs.com/gif.latex?W_i) ~ ![N(0, Q($\kappa$,$\tau$)$](https://latex.codecogs.com/gif.latex?N%280%2C%20Q%28%5Ckappa%2C%5Ctau%29%29);
donde ![$\kappa$](https://latex.codecogs.com/gif.latex?%5Ckappa) y ![$\tau$](https://latex.codecogs.com/gif.latex?%5Ctau) determinan el rango y la varianza del efecto espacial respectivamente.


**Nivel 3**: _Hyperparametros_

Son los hyperparametros del efecto espacial que se calculan de la siguiente manera:
$2log\kappa~ N(\mu_\kappa, p_kappa)$ y $log\tau~ N(\mu_\tau, p_\tau)$

Como distribuciones a priori para estos hyperparametros, hemos utilizado los valores que tiene INLA por defecto, que son: rango $\phi= 20%$ de nuestro área de estudio y la varianza $\sigma^2= 1$.

El SPDE a efectos prácticos se realiza con el uso del mesh (Delaunay triangulation). EN nuestro modelo, el mesh utilizado ha sido el siguiente.

![Nuestro mesh](https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/blob/main/Im%C3%A1genes/mesh_plot.PNG)

A continuación se muestran los valores de los parametros obtenidos para nuestro modelos.

![Modelo estimado en INLA](https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/blob/main/Im%C3%A1genes/summary_modelo_inla.PNG)


# NUETROS RESULTADOS

Para evaluar la calidad de las predicciones realizadas creamos una función que realizase la media de 10 validaciones cruzadas con cada uno de los modelos frecuentistas y así poder compararlos entre sí de forma mas sencilla. Los criterios de valoración que vamos a usar son: AUC, Kappa, COR, TSS, sensitividad y especificidad. 

![alt text](https://github.com/KaterinaPaparsenos/A_las_anchoas_se_les_termino_la_incertidumbre/commit/155ef0ce8ca4d61f707bcb415f9227482132b84b)

La siguiente parte fue realizar el mapa predictivo de los correspondientes modelos realizados: (CATERINA METE IMÁGENES)

# ¿QUE HEMOS OBTENIDO DE TODO ESTO? 

Después de todo el trabajo realizado concluimos lo siguiente:

- La identificación de áreas de presencia a la hora de planificar áreas marinas. En concreto la delimitación de las zonas de pesca para el consumo y para la conservación de la especie.

- Entre los modelos frecuentistas, MAXENT, siendo el de los más sencillos realiza ligeramente mejores predicciones que GLM. La calidad de las de las predicciones con GLM son casi iguales pero, siendo algo mas complejo, este tiene mejor interpretabilidad.

- La aplicación de Modelos Jerárquicos Bayesianos utilizando el software R-INLA permite hacer inferencia mas efeciente, intuitiva y sencilla a nivel computacional. Sin embargo, su capacidad predictiva para este área es dificil computacionalmente, por lo que sería idóneo realizar de nuevo la modelización de cada área por separado.

# PERSPECTIVA DE FUTURO

Los siguientes pasos a seguir serán:

- La realización de otros modelos como bosted regresion tree, random forest y GAM.
- Probar a realizar el modelo de MAXENT con otro paquete o software.
- Volver a realizar el modelo de INLA para cada área por separado o centrándonos solo en Atlántico Norte. Dependiendo del tiempo del que dispongamos.


