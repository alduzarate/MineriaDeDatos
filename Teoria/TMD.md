## Preprocesamiento
Preparación de datos

### Entendiendo los datos
#### Relevancia
Los datos sirven para resolver el problema? Son relevantes?
Qué período de tiempo cubren los datos? Quién es el experto en esos datos?

#### Calidad
-Nro de registros => menos datos, menos confiables los resultados (en gral mientras mas datos mejor, solo habia que recortar por la complejidad de los algoritmos)
-Nro de variables (en gral, por cada variable 10 o más registros). Si hay demasiadas, se puede usar selección o extracción de variables.
-Nro de clases (si es muy desbalanceado se puede intentar corregir)

### Limpieza de datos

#### Adquisición
-Guardarlos en DBs, tener cuidado al parsearlos
- Verificar la lectura correcta de los datos 

### Meta-datos
Tipo de variable, Uso de la variable

### Formato
-Valores faltantes (ignorar registros, ignorar variables, dar valor particular, imputation(malo pq implicaria que la variable es deducible a partir de otras))
-Formato de fechas
-Conversión nominales a numéricos (el mecanismo varía segun el tipo de dato) y Discretización (usando diferentes tipos de histogramas) de datos numéricos (solo los arboles de decisión puede usar nominales, el resto usa numéricos)
-Limpieza de errores y outliers (no hago nada o fuerzo cota inf/sup y los asigno a esos valores)

### Pre-selección de variables. "Falsos predictores"
-No importa el rango de la variable, sino que tenga muchos valores distintos, qué tanto cambia.
-Falsos predictores: si construyo un AD y hay una variable al principio del árbol que divide todo -> sospechoso (ver si la borro o consultar si es importante)

### Clases desbalanceadas
(para evitar que se aprenda luego solo la clase mayoritaria)
-Sobresamplear la minoritaria
-Subsamplear la mayoritaria (descartando casos obvios)

**Paper: undersampling**:
Genero puntos pero antes me aseguro que la zona sea segura (eliminando los que no tenga vecinos de su clase)
Si la clase es desbalanceada es mejor no hacer nada artificialmente
Hay que ser escépticos al leer papers de ML, 1/100 está mal

## Visualización

### Buenos gráficos - El lie factor
-Si el box plot no alcanza, hacer mas pequeños haciendo "zoom"
-Lie factor: escalas en el eje Y no acordes a la magnitud de los datos, dan un efecto de cambio notorio que en realidad no existe.

### Representación de datos en 1,2,3D
1D:
-Problema de los histogramas: aglomeramiento (causado por outliers por ejemplo). Solución: bins de igual altura o eliminando el outlier de alguna manera.
-Boxplot: se puede modificar la distancia desde la cual se empieza a considerar un pto outlier

2D:
-Problema de scatter plot: saturación. Si hay 10 millones de puntos no se entiende nada.
-Contornos: gráficos para plots con alta densidad

Primero se hace un análisis univariado para ver cosas raras simples, y luego se pasa al bivariado etc (o mientras más variables mejor) para ver si hay cosas raras en los datos.

3D:
-Proyecciones de la supercie en el plano (problema: siempre depende de donde se ponga la proyeccion, de acuerdo a eso se puede ocultar información)
-Heat-map: 2D +  1 color que da información de la 3er dimensión

### Representación de datos en 4D+
-Parallel coordinates: el orden de los ejes de las variables es importante, límite de ~20 dimensiones
-Chernoff-Faces: caritas
-Star plots: cada variable va en una dirección angular equiespaciada diferente.
-Scatterplots
-Stick figures

Primero se hace un análisis univariado para ver cosas raras simples: el problema es que no muestra correlaciones.
Luego se pasa al bivariado etc (o mientras más variables mejor) para ver si hay cosas raras en los datos. En 2D se ven facilmente las correlaciones, pero no se ven efectos multivariados. 

## Proyecciones
Se representan los datos en un espacio de menor dimensión para:
- Visualizar mejor los datos
- Para moderlarlos mejor
Lo que caracteriza datos en muchas dimensiones es la distancia entre los puntos => una buena proyección es la que conserva estas relaciones de similaridades o distancias, es decir, una proyección lineal tal que los datos conserven lo más posible su estructura.

La proyección se calcula de manera de minimizar las distancias de los puntos (*r*estos) al espacio proyectado. Esto se puede ir calculando iterativamente, es decir:
- La primer componente principal es la **dirección** en la cual los datos tienen máxima varianza (ya que minimizar los restos es equivalente a maximizar las proyecciones, y como la media es 0, hay que maximizar la varianza).
- La segunda comp. ppal es la dirección ortogonal a la anterior en la cual hay máxima varianza (es la 1er comp. del subespacio que queda)
- y así hasta llegar a las p variables.

La solución de cuál es la dirección (vector unitario) que maximiza las proyecciones son los autovectores de la matriz de covarianza => como el vector es unitario, la varianza de z es el autovalor.

¿Cómo se calculan las direcciones principales? Se calculan los *p* autovectores de la matriz de covarianza con sus correspondientes autovalores, se las ordena según el valor de su autovalor, con lo cual es autovalor máximo da la dirección de varianza máxima (que da los datos lo más separados posible)

Desventaja: dimensiones altas hace que el cálculo se vuelva complejo (p³).

¿Cuántas dimensiones necesito conservar? La cantidad puede estar determinada por la suma relativa de los autovalores, ya que quizás con sumar solo 2 ya tengo algo muy parecido a los z (ya que los otros pueden ser muy pequeños y no aportar significativamente) => Varianza explicada: dejar las direcciones que conservan "casi toda" la varianza.

Formas de hacer la PCA:
- En correlación
  - Datos sin normalizare - distintas varianzas para cada variable original
  - Se usa cuando las relaciones entre las variables tienen sentido físico-real (y por ello no se las puede escalar)
  - Puede suceder que el 1er autovector tenga que ver con la escala de los datos => hay que conservar más autovalores para describir los datos pq el primero explica casi todo del tamaño y después las direcciones interesantes aportan mucho menos al tamaño de los datos.
- En covarianza:
  - Datos normalizados previamente (varianza 1 en todas las variables)
  - Cuando las variables no tienen relación
  - No suele haber un autovector muy grande
  - Se usa en la mayoría de los casos reales, suelen quedarse con los autovectores normalizados que suman el 95% de la varianza.

Cosas que se pueden hacer usando la PCA:
- Detección de outliers
- Interpretación: ver qué variables explican más los datos

Dato de color: MDS se basa solo en distancias entre puntos. No se necesita que los puntos esté en un espacio vectorial, solo las distancias. (PCA es un caso particular de MDS)

### Proyecciones no lineales
Se usan superficies no-lineales (curvas en vez de rectas por ej)

Paper **Isomap**: Pca no va más allá, busca la mejor manera de retirar la superficie. Isomap va más allá y aunque sea retorcida puede encontrar menos dimensiones

--------

Paper **Kernel PCA for novelty detection**: Una vez que estiró los datos en el espacio de infinita dimensión, la pca extrae las componentes principales de la distribución de datos; midiendo distancias con el kernel busco lo que está lejos y eso es lo novedoso
Ojo con la elección del sigma, cambia todo.

## Selección de variables

### Por qué y para qué

Caso: tener mil variables haría que la medición sea costosa; haciendo que la PCA no sea una opción viable. Mejor sería limitar esas mil a 20 y después voy y mido eso nomás, y en última se hace PCA sobre ese subconjunto de variables. Además de que modelar el problema origial directamente sería subóptimo (tanto en calidad como en interpretabilidad)

Ventajas: 
- Se mejora la performance de los métodos de aprendizaje (y reducir el problema de la dimensionalidad)
- En muchos casos muchas variables no son informativas del problema (ruido o redundancias) y al eliminarlas reducimos el riesgo de sobreajuste.
- **Se descubren** cuáles son las variables más importantes en un problema y cuáles están correlacionadas, coreguladas o son dependientes y cuáles no.
  
### Métodos

- Univariados: consideran una variable a la vez (pero no pueden resolver algunos problemas, como el del xor)
- Multivariados: consideran subconjuntos de variables al mismo tiempo.

¿Por qué no se prueban todas las opciones que hay de combinaciones de subconjuntos de variables? => explosión combinatoria: costoso computacionalmente y además suele llevar a una mala solución (al probar entre tantos pares, la probabilidad de que aparezca alguna correlación al azar es muy alta) ==> se usan soluciones sub-optimas sobre eurísticas.

### Filtros (heurística)
- Ordenan las variables con criterios de importancia independientes del predictor (no resuelven el problema de modelado directamente) y se retienen las más importantes (criterio de corte)
- Suelen ser univariados
- Los criterios de importancia son los que más separan las clases en diferencia de medias en relación a las varianzas.
  - Ejemplos: anova (aunque es mejor usar un test estádistico más gral como kruskal wallis que no asuma que las variables son gaussianas para evitar falsos resultados de separación, aunque lea menos), ganancia de información, etc
- Suelen tener problemas con variables "conjuntas"
- Son muy rápidos

### Wrappers (heurística)
- Usan el predictor final (modelo) para evaluar la utilidad de las variables (probando de a subconjuntos el error al usar ese subconjunto de variables, el tema que esto es muy costoso como vimos antes)
- Suelen ser multivariados
- Dar mejores selecciones
- Son muy pesados
- Suelen hacer overfitting

Alternativas a la búsqueda con explosión combinatoria:

#### Búsquedas Greedy
##### < Forward selection
![[forward-selection.png]]

-Evalúo en cada paso el que tiene mejor error y en el paso siguiente considero solo el subconjunto de variables que tiene las del mejor subconjunto actual.
-Llega hasta el final y después vuelve al mejor, ya que el error puede ir fluctuando al agregar/sacar variables.


##### > Backward elimination

![[backward-search.png]]
-Comienzo con todas las variables, y pruebo de sacar una y me quedo con el que mejor performó. Luego, pruebo los subconjuntos de variables que **están cotenidas** en el mejor subconjunto actual. Así hasta llegar al nivel univariado.
-Evalúa más pares de variables que funcionan juntos, sin jugarsela por una variable como hace forward al principio.
-Es menos óptima cuando se quiere encontrar conjunto chiquito y óptimo.

También se puede hacer un híbrido entre ambas, Búsquedas pseudo random (probando al azar) y simulated annealing, genetic algorithm.

Una buena alternativa para cuando se tienen cientos de miles de variables, sería algo como BS pero eficiente. Si la función de error es suave, dar el paso en la dirección de maximo descenso en la derivada (facil de calcular, aunque sea una aprox, llamadas "medidas internas de importancia") del modelo (respecto de cada una de las variables) debería ser lo mismo que el máximo descenso del error. ==> paso a construir solo un modelo por paso y el algoritmo pasa de cuadrático a lineal.


### RFE
- Ajusta un modelo a los datos
- Iteracion:
  - Rankea las variables usando una medida interna de importancia (+imp, +empeora el modelo al ser eliminada)
  - Eliminar la variable con el ranking mas bajo (la que menos me modifica el modelo)

¿Cómo consigo las medidas internas de importancia que aproximan el error? Se estima, no se mide directamente
- RFE con SVM: el vector que define el plano generado por la SVM, la componente que más colabore en la suma vectorial será la más importante. 
- Random forest
  - Shuffling OOB
  - Variación del GINI index
- LDA o PDA o regr logística: weights
- PLS: scores

Problemas con el RFE:
-Variables importantes pero correlacionadas (comparten la importancia) ==> el ranking de variables tiende a ser inestable



### Evaluando las selecciones
Idea base:
- Aplicar el wrapper, filtro o RFE al prob
- Al ir eliminando variables, controlo el error (en ppio se usó cross-validation, que estuvo mal hecho). Minimizo.
- ¿Por qué estuvo mal hecho usar cross-validation? Pq se está estimando el error de test sobre los mismos datos que usó para elegir las varibles ==> fuente de bias.
  - Elegir variables es como modelar, es como elegir un clasificador; estoy haciendo algo que está basado en los datos (aunque no sea construir el clasificador en sí, es algo que guía la búsqueda e impacta en el clasificador). Se necesitan usar datos completamente independientes del proceso de elección de las variables para poder estimar bien el error.

Cómo se evalúa la selección: partiendo los datos en 3 partes:
- Training: para entrenar los clasificadores. Para cada subconjunto de variables entrenar un modelo en el training set y elegir las variables.
- Validación: evaluar dónde está el mínimo de la variable. Elegir el subconjunto de variables que muestra la mejor performance en el validation set. (Acá si se puede usar cross-validation para tener una mejor estima)
(como tercer paso' se hace una selección final con train+validación)
- Test: para estimar el error final verdadero que después se muestra (pero las variables se eligieron ya con el de validacion, para que sea confiable)


Generalization_error <= Validation_error + $ϵ(c/n_2)$

![[error_en_busqueda_variables.png]]

El error de estimación mientras más evaluo (búsqueda exhaustiva), más baja. Si pruebo muchas cosas, termina encontrando algo bueno (sobreajuste, por más que se dejen conjuntos de test). => Acotar la complejidad de la búsqueda(C, curva roja) mejora el resultado (curva verde)! (búsqueda suboptima usando una heurística) 

## Clustering
Objetivos:
- Encontrar grupos naturales en un conjunto de datos del que no se conocen clases. OBS: Clustering NO es clasificación! Clustering es aprendizaje no-supervisado; se conocen los datos, no los gruposen que están organizados. El objetivo es encontrar la organización.
- Encontrar jerarquías de similaridad en los datos.
- Encontrar prototipos representativos de un conjunto grande de ejemplos.


### Clustering divisivo
- Clustering como una partición del espacio, y que sea más significativa en un nro fijo de partes. 

#### K-means
- Objetivo: encontrar una partición de los datos en k grupos, tal que la distancia media dentro de los puntos de cada grupo sea mínima. => 
  - Grupos apretados, clusters compactos => esto es equivalente a pensar que todos los puntos del cluster, están cerca del centro del cluster. Es decir, la distancia media de todos los puntos al centro del cluster sea lo más chica posible.
  - Al minimizar la distancia total dentro de los grupos estamos maximizando la distancia entre los grupos.
- Para minimizar la distancia media de todos los puntos al centro del cluster, la función a minimizar depende de las asignaciones que se le hacen a cada punto para asignarle cluster y donde se puso el centro de cada cluster.
- La función a minimizar desciende siempre. (al ser una cuadratica simple, la alternada siempre desciende)
- Minimización alternada: voy cambiando si fijo los centros y varío la asignación de clases o varío los centros y fijo la asignación de cluster de los puntos. =>
- Garantía de convergencia: Al minimizar en cada paso, voy a encontrar un mínimo LOCAL de la función en tiempo finito. 

Algoritmo minimización alternada:
- Empezar con k centros al azar
- Itero: 
  - Asignar a cada punto al centro más cercano
  - Asignar cada centro como la media de sus puntos
Criterio de parada: hasta que dejen de cambiar los puntos

Problemas:
    - el punto inicial de búsqueda el azar lleva a un mínimo local. Solución: se hacen muchas búsquedas y se queda con el mínimo de todas las búsquedas (como haciamos en ML con las NN)
    - Depende fuertemente de los outliers al usar la media. Se podría usar la mediana para que no le afecte tanto.
    - Solo vale en espacios vectoriales

#### K-medoids
- Representa cada cluster por su medoid (pto del cluster situado más centralmente)
- Soluciona outliers y vale para espacios arbitrarios
- Mucho más caro computacionalmente. (pasa de ser lineal a cuadrático) => mejor usarlo para problemas chicos

### Clustering jerárquico
- El objetivo es construir una anidación de particiones, de la que se puede extraer luego una cantidad dada de partes.

- En cada nivel, los datos agrupados son más similares entre sí que si se los compara con los de los otros grupos.

- La cantidad de clusters está dada según el nivel donde se corte el árbol del dendrograma. Los subaŕboles conectados *a ese nivel* forman los clusters. 

#### Estrategias para armas los clusters
##### < Top-Down (divisivo)
- Comienza con todos los puntos en un solo cluster, y en cada paso divide todos los clusters en dos segun el criterio que haya.
- Termina con todos los puntos separados. Es bueno para pocos clusters. Es medio contraintuitivo, porque para este método de clustering, necesitas a la vez otro método de clustering para construir los 2 cluster(por ej k-means)

##### > Bottom-up (aglomerativo)
- Comienza con todos los puntos separados (clusters individuales). A cada paso busca los dos clusters más similares (de acuerdo al criterio que haya) y los une.
- Termina con todos los puntos en un solo cluster.
- No se necesita otro método de clustering; solo la distancia entre los clusters para ir uniendo.

#### Criterios de similitud

##### Single Linkage
- Medida de distancia: distancia mínima entre pares de puntos, uno en cada cluster. 
- Busca "alta conectividad", no grupos compactos; ya que no hace falta que esté todo cerca a la vez, con que haya un par de puntos que estén muy cerca ya alcanza.
- Produce el Minumum spanning tree de los datos (Alg. de Prim)

Desventajas
- Muy dependiente de outliers (si hay outliers, seguramente ellos sean un cluster)
- Muy costoso: O(n²)
- No puede corregir errores previos si hace una mala asignación.

##### Complete Linkage
- Medida de distancia: distancia máxima entre pares de puntos, uno en cada cluster.
- Agrupa "conjuntos completamente vecinos", ya que al tomar como distancia la maxima, busca que todos los puntos del cluster estén cerca, no solo un par.
- Busca grupos compactos, estilo k-means.

Desventajas:
- Dependiente de outliers (pero diferente, ahora estos definen los clusters de alguna manera).
- A diferencia de k-means (que el azar del comienzo podría llevar a un mínimo local) es determinista, da una sola jerarquía para todos los datos.
- También es ineficiente, O(n²) ni tampoco puede corregir errores previos (caracteristica gral de los métodos iterativos que se vieron) al también depender de la solución anterior.
-No forma clusters con formas arbitrarias como SL, busca grupos ajustados como k-means. (Pero como le cuesta más, en la practica en gral se usa k-means y ya)

##### Average linkage
- Medida de distancia: media de la distancia entre pares de puntos, uno en cada cluster.
- Intermedia entre los 2 métodos anteriores
- Busca grupos conectados y compactos (pero no tanto como en k-means o CL)

##### Criterio de Ward
- Busca los cluster que al juntarlos dan el menor aumento en la suma de distancia cuadrada entre sus componentes
- Equivalente a k-means; las solucionen *tienden* a dar lo mismo, pero da todo el árbol, no es exactamente la misma solución que con k-means porque esta solución es iterada, no puedo garantizar que llegar a 3 clusters, partiendo 2, eso sea exactamente la misma solución que buscando directamente 3 que sean óptimos. (Si yo quisiera obtener 3 clusters directamente, se usa k-means con K=3 y ya).

Observaciones generales:
- En el 95% de los casos se usa K-means o S/C/AL al ser heurísticas simples, sin gran teoría detrás, fáciles de implementar. Son base de métodos más elaborados para los casos excepcionales pertenecientes a ese 5%.
- No existe "el mejor" método de clustering, todavía no existe un método que resuelva todos los problemas; hay que buscar cuál es la solución que mejor se adapta, evaluar diferentes tipos de soluciones y ver cuál es la que me ofrece mayor información.
- Todos parten de imponer una estructura para lo que busca el método en cuestión (devuelve la mejor solución para el tipo de solución que se está buscando, no significa que los datos estén efectivamente muy apretados o no al buscar soluciones apretadas).
- Si aciertan, entonces el resultado es bueno.

#### Otros métodos de clustering (para el 5%)
##### SOM
- Básicamente un NN que hace clustering; se basa en el mapeo que hace la corteza visual del cerebro.
- Trata de ajustar el mapa (con forma muy simple, de no más de 2 dimensiones) a los datos (pueden ser n-dimensionales), deformandose lo menos posible.
- Los nodos del mapa son centros de clusters que compiten entre sí para estar cerca de los datos.
- Algoritmo:
  - Tomo un punto al azar
  - Busco el nodo más cercano
  - Muevo el nodo (y sus vecinos directos en la grilla) un paso dado en la dirección del punto
  - itero

##### Model-based methods
- Propone un modelo funcional para los datos (gaussianas por ej)
- Ajusta los parámetros del modelo a los datos (usando Expectation-Maximization en gral)
- Se regula la complejidad del modelo propuesto de acuerdo a la cantidad de datos

##### Métodos de ensamble
- Propone juntar información de un montón de soluciones de clustering diversas.
  - Junta evidencia: si un par de ptos están siempre juntos, dejarlos así y viceversa.
  - Usa una matriz de evidencias como nueva matriz de distancias y clusterizarla.

##### Métodos espectrales
- Pensar el problema como el de encontrar componentes disjuntas en un grafo.
- Calcular una proyección PCA de esa matriz en bajas dimensiones
- Buscar clusters en ese espacio
- Muy efectivo


### Técnicas de clustering - Cómo encontrar la cantidad de clusters?

#### Método del salto
- Un criterio general para evaluar la calidad de la solución en la suma de las distancias dentro del cluster W_k (es lo que se minimiza en la mayoría de los métodos de clustering) -> no sirve demasiado porque siempre que se divida en más clusters, la suma de las distancias decrece; con lo cual no sería medida de si la solución es buena o no. => lo que diferencia de una solución mala a una buena es que la suma de las distancias decrece notoriamente (no solo un poco)
- W_k decrece siempre. No sirve buscar el mínimo.
- Pero decrece más cuando separa 2 clusters verdaderos (eliminando distancias largas ) que cuando parte en 2 un cluster "natural". 

Problemas:
- Suele no ser una medida confiable usada directamente. No hay un criterio cuantificado para decir "acá está el corte"
- No detecta la falta de clusters en un problema donde no hay clusters (iris o gaussianas).

##### Gap statistic
- Idea: comparar la curva anterior con la curva que da una distribución uniforme (la DU representaria que "todo sea igual", el objetivo es reconocer alguna diferencia en la distribución de puntos y que, justamente, no sea uniforme, dando lugar a la aparicion de clusters)
- Cuantifica el salto: busca la 1er diferencia significativa entre las dos curvas (primer gap)
- Detecta cuando no hay clusters

Formas de generar la referencia:
a) Tomar una DU que ocupe el mismo hiper-rectangulo que la original de los datos (desventaja: suele ser mucho más grande que el volumen real que representan los datos, mejora:)
b) Hacer lo mismo pero sobre una PCA de los datos.

##### Estabilidad
- Los resultados tienen que ser reproducibles; los "clusters naturales" de un problema se tienen que encontrar siempre => si cambio un pto en un problema y la solución desaparece, entonces no son "clusters naturales" sino un resultado ficticio creado por el algoritmo.

Algoritmo base:
- Variar la cantidad de clusters (de 1 a n)
- Construir muchas soluciones replicadas (usando de base los datos un poquito cambiados y un mismo método de clusterizacion para todas las réplicas)
- Evaluar la estabilidad de las soluciones 
- Seleccionar el k con más estabilidad.

\* Cómo generar réplicas?
Si cambio mucho, puedo destruir la estructura natural. Si cambio poco, puede parecer estable algo que no es.
- Subsample: tomo una muestra al azar de los datos originales (en gral del 70% al 90% de los datos)
- Agregar ruido: agregar ruido blanco (normal) a los datos originales (menos de 10% de la señal). No es posible en conjuntos discretos porque al moverlos ya una unidad se cambian demasiado los datos.
- Proyecciones: en datos de alta dimensionalidad, tomar proyecciones sobre un sub-espacio elegido al azar.

Cómo se evaluan los resultados? 
Mismos datos:
- Siempre se evalúan pares de soluciones
- Cuando los conjuntos de datos son iguales:
  - cuento cuantos pares de ptos que están en un mismo cluster en la 1er sol también lo están en la 2da
  - normalizo la cuenta

Distintos datos:
- Restringir: evaluar la intersección
  - tiene sentido si la intersección es grande
  - se pierde información (si la muestra original ya era chiquita al hacer la int. se hace aún más)
  - la más usada (siempre y cuando la muestra sea grande)
- Extender: agregar los puntos faltantes a cada solución (y a cada cluster, usando el criterio del clustering). Uso los puntos que quedaron afuera no para generar los clusters, sino para evaluar la solución
  - por ej, en k-means, asignar al centro cercano
  - en SL asigno al primer vecino espacial
  - menos común, pero sirve si el conj. de datos es más chiquito

Seleccionar el k
- Dada la muestra de valores de similaridad entre las soluciones para todos los puntos, para distintos k:
  - tomar la media salvo que justo la distribución sea una gaussiana no es una buena opción porque no me caracteriza la distribución
  - Hay que mirar la distribución acumulada: busco las que estén más tirando para el 1 (siendo que a mayor similaridad mejor) y de las curvas que están ahí, me quedo con la que representa la mayor cantidad de clusters.
    - si no hay ninguna para la derecha, también *sirve para ver que no hay solución* porque ni siquiera la de 2 clusters da buenos resultados.

Contraejemplos

- Uno asume que las soluciones naturales tienen que ser estables
- Lo contrario no está garantizado. Hay soluciones estables que son "artificiales". Es decir, las soluciones artificiales no tiene por qué ser inestables.(ejemplo: clusters triangulados). Se suele superar seleccionando (entre todas las estables) la que tiene mayor k. Pero para el caso de Iris, las soluciones siempre son estables y ahi se pierde casi siempre. =>
- Principal desventaja: los algoritmos que buscan "bolas" son estables en distribuciones alargadas para k crecientes (K-means en una distribución uniforme unidimensional) y no son las que uno está buscando.

**Density Peaks**: Mide densidades, Cosas densas PERO que están lejos de los puntos densos

## Ensambles

Conjunto grande de modelos que se usan juntos como un "meta modelo"

- Idea base: usar conocimiento de distintas fuentes al tomar decisiones.

Componentes de un ensamble:
- Un método para seleccionar o construir los miembros (si buscar de la misma o distinta área)
- Un método para combinar las decisiones (votación, promedio, función de disparo)

**< Ensambles divisivos**: 
- expertos en *diferentes áreas* con alto conocimiento cada uno de *su* tema. Estructura jerárquica (a diferencia de la anterior), hay una cabeza que decide quien sabe del tema. (Mixture of experts, Stacking).
- Divide el problema en una serie de subproblemas con mínima sobreposición (a lo d&q)
- Útiles para atacar problemas grandes
- Se necesita una función (de disparo) que decida qué clasificador tiene que actuar (**¡si esta se equivoca y asigna mal al clasificador que tiene que actuar ya no hay vuelta atrás, el modelo va a actuar mal!).**

### Stacking
Se dividen los datos y se crean T clasificadores, uno experto en bootstrap, para luego hacer un metaclasificador que recibe como entrada la salida de las clases de los clasificadores anteriores y decide y aprende a quién darle importancia en base a las posturas que van tomando.

### Mixture of experts
Se divide al espacio y se hacen T clasificadores, cada uno experto en un lugar del espacio. La decisión final es una combinación lineal de los clasificadores, donde la función de disparo le da diferente peso a cada clasificador segun el lugar del espacio que se quiera ver.

**> Ensambles planos**: 
- muchos elementos, todos son pares con alto conocimiento sobre el mismo problema, *todos* votan. (Fusión, Bagging, Boosting, RF)
- Deben ser lo mejor posible individualmente pero deben opinar distinto en algunos casos. Sino mejor quedarse con uno solo. Que se equivoquen lo menos posible pero que a su vez sean lo más diverso posible.
- Hay del lado de una formalización más computacional-matemática (boosting) y otros más por la vía estadística (bagging, RF).

El dilema del sesgo-varianza explica por qué funcionan los ensambles.
Error de sesgo: 
- por falta de flexibilidad en mi función de aproximación (Todas las soluciones son estables), estoy apuntando hacia un lugar que no es el correcto (aunque haya áreas que no están tan mal).
- se da cuando se usan funciones rígidas, con buena estimación de los parámetros óptimos pero poca flexibilidad.

Error de varianza: 
- se le da mayor flexibilidad a las funciones con lo cual está la capacidad de aproximar mucho mejor que antes, pero a su vez un pequeño cambio en los datos que estoy ajustando, produce cambios inmensos en la función. (Sería el error en obtener los parámetros adecuados de la función)
- se da cuando se usan funciones flexibles (como polinomios), tiene buen ajuste pero mala estimación de los parámetros óptimos.

Formas de resolver el dilema de la complejidad de las funciones (ambos extremos dan errores altos, por el lado de baja complejidad de sesgo y por el lado de alta complejidad de varianza)
- Disminuir la varianza de los predictores sin sesgo: construir muchos predictores y promediarlos (Bagging y RF)
- Reducir el sesgo de los predictores estables: construir una secuencia tal que la combinación tenga menos sesgo (Boosting)

### Bagging

- Usar predictores buenos pero inestables (AD y ANN sobretodo)
- Conseguir diversidad perturbando los datos
- Usa bootstraps: muestras al azar de la misma longitud, con repetición (bolillero donde al sacare la bolilla se vuelve a poner en la esfera).
- El bootstrap no introduce bias en ninguna estadística! (al tener repetición es como si estuviera sampleando la muestra original)
- Cómo se construyen los miembros: Cada predictor se entrena sobre un bootstrap del conjunto de entrenamiento
- Cómo se combinan las decisiones:
  - Para regresión/ranking: promedio simple de las predicciones de cada modelo
  - Para clasificación:
    - Cada modelo vota por una clase. La que tiene más votos es elegida.
    - Cada modelo estima la probabilidad de clase. Se promedian. La de mayor probabilidad se elige. (Ayuda a resolver el problema del sistema de votos cuando las confidencias son muy bajas)
  - Para clustering: se crea una matriz que cuenta cuantas veces cada par de puntos termina en el mismo cluster, y se clusteriza esa matriz de similitud.

¿Cuántos predictores/miembros entrenar? ==> no es necesario fijar T, sino solo que sea grande (se están promediando cosas que no tienen bias, es más ruido al azar, después de 500 veces el ruido lo cancelé seguro al promediar cosas con pequeño ruido y lo que queda no cambia más)

La clave para que funcione Bagging es la inestabilidad de los clasificadores; que se cambien un poco los datos y den algo que en muchos puntos difiere (en otros no) pero donde hay errores tiende a cancelarse porque difiere.

Otras formas de generar diversidad (en vez de hacer bootstraps) o de hacer los miembros:
- Sub-sampling de los datos (dividir en train y val)
- Agregar ruido
- Variar los modelos en vez de los datos (la forma en que ajustan o la condicion inicial en ANN; las ANN al empezar en un punto al azar y descender por el gradiente al ser azar no tienen bias y por esto ya tienen la diversidad que buscamos, se pueden entrenar 100 redes sobre los datos originales, promediar y listo en vez de usar bootstraps, cuesta menos)

Otras formas de combinar las decisiones de los miembros:
- Usar pesos estadísticos (cuando se usan las probabilidades) en la combinación: votos *livianamente* "pesados". Los mejores clasificadores tienen más fuerza en la decisión
  - Funciona igual o mejor que el uniforme

### > Random Forest: evolución de Bagging al usar árboles como modelos
Aca se pueden usar datos más "reales", no como en el caso anterior que eran solo vectoriales.
- Problema bagging + árboles: Usar bootstraps genera diversidad, pero los árboles siguen estando muy correlacionados (las mismas variables tienden a ocupar los primeros cortes siempre)

Solución:
- Agregar un poco de azar al crecimiento
- En cada nodo, seleccinar un grupo chico de variables al azar y evaluar solo esas variables bajo el criterio, elegir al mejor y dividir los puntos hasta llegar a 1.
  - No agrega sesgo: a la larga todas las variables entran en juego.
  - Agrega varianza: pero eso se soluciona fácil promediando los modelos
  - Es efectivo para decorrelacionar los árboles.

Observaciones:
- Construye los árboles hasta separar todo. No hay podado. No hay criterio de parada.
- La cantidad random de variables a evaluar es importante (muy pequeño muy azaroso y muy grande es igual a bagging). El default sqrt(total_vars) suele ser bueno.
- Al igual que antes, el nro de árboles no es importante, mientras sean muchos (>500)

Subproductos de RF:
#### Out-of-bag estimation

- Cuando se toma un bootstrap hay ptos que quedan fuera (llamados Out Of Bag, OOB), un 37% del total en media
- Para cada predictor hay un conjunto OOB que no usó durante el training => Se pueden usar para estimar errores de predicción sin sesgo (con un ensemble más chico, pero sigue siendo en test)
- P/c pto del train set formo un subensamble que tiene solo los clasificadores que no entrenaron con ese punto
- Se hacen predicciones sobre todo el conj de train y promedio => Predicciones OOB, Errores OOB
- Son una buena estima del errot de test (pero tienen un sesgo, "seguro", ya que estiman de más al estar calculadas con un ensamble más chico). Pero al ser de más y no de menos ta todo ok.
- Es gratis, los puntos ya estaban ahí (datamining math :sparkles:)

#### Devuelve Medida de importancia de la variables
Dos criterios, los dos se estiman fácil y suelen ser equivalentes:
- Gini: Media de la reducción del Gini cuando se usó cada variable, promediada en el RF (subproducto del ajuste de los árboles)
- Randomization: aumento del error OOB cuando cada variable se randomiza por separado (si al randomizarla el error no cambia, no servía pa nada) (se hace al hacer una pasada por los conj OOB con cada una de las variables randomizadas)

#### Gráfica de proximidad
- Modo interno de proyectar datos en bajas dimensiones (como PCA o MDS)
- Calcula una matriz de distancias entre los datos basada en cuantas veces los ptos terminan en una mismo nodo de un árbol (ptos cercanos deberían terminar juntos seguido)
- Hace un MDS de esas distancias (no puedo hacer PCA porque no estoy en espacio euclideano?)

Muy usado porque:
- no hay que ajustar ningún parámetro
- no hay que dividir los datos en train/validation ni en train/test, al tener la estima OOB que está apenas sesgada para el lado malo
- Suele ser peor que boosting pero la diferencia es muy chica para ir por ese método que es mucho más complejo, este es plug and play básicamente y anda muy bien si no hay mucha exigencia
  
### > Boosting

- Es fácil construir un clasificador con un error apenas menor al 50% en cualquier problema de 2 clases balanceado (weaklearner)
- Es difícil conseguir uno que dé un error arbitrariamente chico en cualquier problema (stronglearner)

Idea central:
- Construir una secuencia de clasifs débiles, donde c/u aprenda un poco de lo que le falta a la secuencia previa
- Como los clasificadores débiles pueden mejorar un poco en cualquier problema (el error siempre es menor a 0.5, es decir, el azar), la secuencia converge a error cero.

¿Cómo se construyen los modelos?
Se trabaja con boostraps (como en bagging), pero la muestra no se toma con pesos estadísticos uniformes (bolillero), sino que se busca poner énfasis en aprender los puntos que fueron mal clasificados previamente por el ensamble ==> Para esto, se le aumenta el peso estadístico a los errores, y se le baja a los aciertos.

¿Cómo se combinan las decisiones?
- Se hace una suma de votos (como en Bagging)
- Los pesos en la suma no son uniformes => se le da más peso a los clasificadores que son mejores.


¿Qué pasa con el error de entrenamiento?
Se puede probar que el error sobre los datos de ajuste converge a 0, siempre y cuando el weaklearn de verdad puede siempre hacer algo mejor que el azar (esto no siempre pasa para todas las distribuciones) =>
- Se espera sobreajuste (el error de train tiende a cero)
- El clasificador se vuelve más complejo a cada paso

PERO NO!

Analizando el error de test real, se vio que:
- En realidad no sobreajusta
- Peor, el error de test sigue bajando con el error de train en 0, explicaciones de esto:

#### Margen
- El error de test solo mide la clase (decisión binaria), mientras que la respuesta del algoritmo es más compleja (suma pesada de votos por una clase y otra, para que pase de una clase a otra cuesta mucho más que pase de tener signo positivo a negativo por ej)
- Hay que tener en cuenta la "fuerza" de la rta (la calidad)
- Margen: fracción pesada de votos correctos - fracción incorrecta. Margen= -+1 punto mal/bien clasificado con alta confianza. 0 si hay poca confianza

T1: Mayor margen es equivalente a una menor cota superior en el error de test
T2: Boosting incrementa en cada iteración el margen de los datos

Conclusión: El error de test baja porque boosting aumenta el margen

#### Costo exponencial (explicación estadística)
El algoritmo minimiza a cada paso una función de costo exponencial => el costo exponencial es una cota superior del error de clasificación

![[minimizacion-boosting.png]]
(minimiza la roja en vez de la azul, por eso puede seguir minimizando)

#### Teoría de juegos
Boosting es un juego entre el booster y el weaklearn. La estrategia óptima da el algoritmo aplicado.

#### Extensiones
> Originalmente boosting solo era para clasificación binaria, pero fue extendido para regresión, clasif multiclase y ranking.

Ventajas boosting:
- eficiente y fácil de programar
- solo hay que ajustar el parámetro T
- flexible, funciona con casi cualquier learner (pero no tiene mucho sentido usarlo con strongs como redes)
- efectivo, tiene garantías de funcionamiento (siempre que el weaklearn cumpla, en la realidad si no consiguen mejorar el 0.5, hacen trampa y vuelven a arrancar el algoritmo)

## Métodos de Kernel
Se usa para problemas simples muy actuales que no es ni imagenes ni cosas secuenciales (acá ya hay que usar redes profundas).

Los puntos más cercanos al hiperplano se llaman vectores soporte.
Margen del hiperplano: distancia entre vectores soporte.
- Maximizar el margen es bueno para achicar el error de test acorde a la intuición y la teoría del aprendizaje => el error de test es más chico mientras más simple sea el clasificador y mientras más grande sea el margen ($E_t < E_a + f(VC/m$)
- Implica que solo los vectores soporte importan, otros puntos de entrenamiento son ignorables => en gral es muy rápido de calcular, cuando hay una buena solución con buen margen y pocos puntos mal clasificados.

¿Cómo obtener el hiperplano (lo más simple es lo lineal) que hace máximo el margen automáticamente?

- Quiero maximizar la distancia entre los vect. soporte de una clase y el plano + lo mismo pero para los soporte de la otra clase => esto es equivalente a minimizar la norma bajo la condición que deja todos los puntos de una clase de un lado, y los de la otra del otro.

### SVM no separable

- El tema es que este problema de minimizacion no tiene solución si las dos clases no son separables => se crea una variable de relajación: mide cuánto para el otro lado está desviado este punto (de donde debería estar para que cumpla la condición o que esté dentro de margen) 
- Se permite que algunos puntos estén mal clasificados, pero que sean lo menor posible => minimizo también la suma de la variable de relajación junto al margen

### Kernels

Para casos donde no se pueda separar linealmente, se proyectan los datos a un espacio de mayor dimensión (donde debería ser más fácil separar las 2 clases)

![[truco_kernel.png]]

- Habría que calcular los productos escalares $ϕ(x_s)$ y $ϕ(x_t)$, pero esto en un espacio de alta dimensión puede ser caro.
- Mejor usar un **kernel**: una función (x,z) -> k(x,z) que representa el producto escalar en un espacio vectorial "oculto":

Feature Spaces: Una  forma  de  utilizar  SVM  para  clasificar  problemas  no-linealmente  separables  es  mapear  los  puntos a  un  espacio  de  mayor  dimensión  en  la  cual  sí  sean  separables.   El  truco  del  Kernel  consiste  en  encontrar  un φ tal  que $K(x_i,x_j) =φ(x_i)^Tφ(x_j)$ , y Mercer nos dice que toda funcion semidefinida positiva es un kernel.

Ejemplos de kernels:
- Polinomial: $k(x,z) = (uxz + v)^p$
  - los vectores son x y z
  - u hace cambiar la forma del kernel, se puede optimizar (si hay tiempo y prata) pero en gral es 1
  - v suele ser 0
  - p se optimiza siempre:
    - se optimiza con un conjunto de validación: separa parte de él y con esa parte se busca la optimización
    - se suele probar con los grados [1..5]
- Gaussiano
- ¡not a kernel: tanh()!

Obs:
- Todo kernel positivo y simétrico f(u,v) es un producto interno en algun espacio, sin importar cuál es el espacio.
- Kernel algebra vale => combinaciones lineales de kernels son kernels
- Se pueden hacer kernels para objetos no vectoriales (como en el paper String Kernels)

¿Cómo se usan las SVMs entonces?
- Elegir un kernel k() y optimizar sus parámetros
- Optimizar C, el trade-off entre margen y errores. Se prueba con valores entre 10⁻5 y 10⁵.
- Se resuelve con el kernel el problema de minimización
- Calculo la clase calculando el producto usando el kernel

Entonces:
- SVMs maximizan el margen (en el espacio vectorial ampliado)
- Usar el truco del margen relajado
- Proyectar los datos en un espacio de dimensión más alta para casos no lineales
- Los kernels simplifican los cálculos
- El método Lagrangiano lleva a un problema de minimización cuadrática "linda" bajo condiciones.
-------

Comente el truco del kernel en SVM. Mencione y explique algún kernel en particular y cómo optimizaría parámetros del mismo.
El método de support vector machine nos plantea que dadas múltiples soluciones que separan linealmente (o con un hiperplano) dos conjuntos de datos,  podemos encontrar una óptima que maximice los márgenes entre dicho plano y los datos (en este caso, maximizar los vectores de soporte).  Ahora bien, existen casos donde no se puede encontrar un plano que separe ambos conjuntos, en estos casos, si soft-svm no funciona, existe una solución que nos plantea mapear los datos a un espacio de mayor dimension, en el cual sí puedan ser separados.


### Otros problemas

- Cualquier método de ML que solo dependa de productos internos de los datos puede usar kernels 

#### Clasificación multiclase
- Divide al problema en subproblemas de 2 clases: OVA (n problemas de One Versus All other classes) u OVO (ensamble de C(n,2) problemas One Versus One, la que tiene más votos va). OVO es más eficiente
- Hay formulaciones directas multiclase de SVM pero no son mejor que OVO.

#### Regresión
- Muy similar a clasificación, propuso "el tubo": los puntos tienen que estar dentro de un tubo de ancho $ϵ$. Los ptos están bien clasificados si estan dentro del tubo, y mal clasificados si están afuera.
- Seteo entonces, el kernel y sus parametros, C y ahora se agrega "el tubo".

#### Novelty detection
- No hay clases, sino comportamientos normales y raros del sistema
- Clásico: usa una función de densidad, puntos debajo del threshold son outliers
- 2 versiones de kernel:
  - Tax & Duin: encontrar la minima hiper esfera que contiene todos los datos, puntos afuera son outliers. Minimizo el radio de la esfera, permitiendo que algunos puntos queden afuera (relajando, como se hizo antes)
  - Scholkopf: encontrar el hiperplano con la maxima distancia al origen que deja todos los puntos de un lado (solo para kernel Gaussiano)
-------------------------------
Paper **Kernel PCA for novelty detection**: Una vez que estiró los datos en el espacio infinito-dimensional, es este espacio la pca extrae los principales componentes de la distribución de datos; midiendo distancias con el kernel busco lo que está lejos y eso es lo novedoso.
Ojo con la elección del sigma, cambia todo.

**kernels_for_proteins**: Construir kernels para algo es muy difícil xd

**String kernels**: Lo que funciona parte de ideas simples (redes profundas)
Método de clasificación que proyecta al azar en un espacio muy grande y ahí puedo encontrar una solución muy fácil, pero funciona en unos casos y otro no. (Todo lo que tenga azar involucrado en gral no sirve)