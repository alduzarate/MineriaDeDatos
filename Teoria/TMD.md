## Preprocesamiento

## Visualización

## Proyecciones

## Selección de variables

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