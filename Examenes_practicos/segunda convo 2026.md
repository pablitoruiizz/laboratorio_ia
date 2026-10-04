# Inteligencia Artificial
## Examen Práctico
## 7 de julio, 2026


```python
import math, random
```

En este examen vamos a implementar en Python el algoritmo de k-medias haciendo uso de tres métricas de distancia: **euclídea**, **Manhattan** y **Hamming**. Posteriormente analizaremos el comportamiento del algoritmo sobre dos conjuntos de datos de ejemplo.

El algoritmo de K-medias es un algoritmo de clustering que sirve para clasificar en grupos o clusters una serie de ejemplos (vectores numéricos) que constituyen el conjunto de datos de entrada. Además del conjunto de datos, recibe como entrada el número K de clusters de la clasificación que se pretende hacer.

Básicamente, comienza escogiendo K centros y asignando cada elemento a la clase representada por el centro más cercano. Una vez asignado un cluster a cada ejemplo, la media aritmética de los ejemplos de cada cluster se toma como nuevo centro del cluster. Este proceso de asignación de clusters y de recálculo de los centros se repite hasta que se cumple alguna condición de terminación (estabilización de los centros, por ejemplo).

Los siguientes ejercicios tienen como objetivo final la implementación en Python del algoritmo de K-medias. La función de distancia a utilizar será un parámetro de entrada al algoritmo.

Para hacer pruebas a medida que se van definiendo las funciones, usaremos dos conjuntos de datos:

- **`datos_reales`**: datos numéricos continuos sobre la altura y el peso de una población. Es de suponer que estos datos corresponden a dos grupos (hombres y mujeres), y en principio desconocemos a qué grupo pertenece cada dato. Este conjunto lo usaremos para probar las distancias euclídea y Manhattan.
- **`datos_binarios`**: respuestas binarias (sí=1 / no=0) de 12 personas a 6 preguntas de un cuestionario (por ejemplo: ¿hace deporte regularmente?, ¿fuma?, ¿es vegetariano?, ¿vive en ciudad?, ¿tiene coche?, ¿viaja frecuentemente?). Es de esperar que existan dos perfiles de respuesta distintos. Este conjunto lo usaremos para probar la distancia de Hamming, ya que esta métrica solo tiene sentido claro sobre datos categóricos o binarios (cuenta en cuántas posiciones difieren dos vectores, sin medir cuánto difieren). Con datos numéricos continuos, la distancia de Hamming apenas distingue nada, ya que casi ningún par de valores coincide exactamente.


```python
datos_reales = [[175.0, 71.0], [158.0, 54.5], [190.0, 95.0], [163.0, 57.5], [182.0, 84.0], [161.0, 56.0], 
         [177.0, 73.5], [155.0, 50.0], [187.0, 91.0], [166.0, 60.5], [180.0, 82.0], [159.0, 52.5], 
         [174.0, 70.0], [168.0, 62.0], [192.0, 98.5], [160.0, 55.0], [185.0, 88.0], [162.0, 58.0], 
         [178.0, 76.5], [165.0, 59.0]]

datos_binarios = [
    [1,0,1,1,0,1],
    [1,0,0,1,0,1],
    [0,1,0,0,1,0],
    [1,0,1,1,1,1],
    [0,1,0,0,0,0],
    [1,1,1,1,0,1],
    [0,0,0,0,1,0],
    [1,0,1,0,0,1],
    [0,1,0,1,1,0],
    [1,0,1,1,0,0],
    [0,1,0,0,1,1],
    [1,0,0,1,0,1],
]
```

### Ejercicio 1 [0.75 ptos]
Cree las funciones `distancia_euclidea(a,b)`, `distancia_manhattan(a,b)` y `distancia_hamming(a,b)` que implementen, respectivamente, las siguientes métricas de distancia:

**Distancia euclídea:**
$$ dist_{eucl}(a,b) = \sqrt{\sum_{i=1}^n (a_i - b_i)^2}$$

**Distancia Manhattan:**
$$ dist_{man}(a,b) = \sum_{i=1}^n | a_i - b_i | $$

**Distancia Hamming:**
$$ dist_{ham}(a,b) = \sum_{i=1}^n [a_i \neq b_i] $$

Estas tres funciones se usarán en el resto del examen pasándolas como parámetro a las funciones que necesiten calcular distancias.


```python
# Solución:

```

Ejemplo de uso:


```python
distancia_manhattan([1,0],[0,1])
#Salida esperada: 2
```


```python
distancia_euclidea([1,0],[0,1])
#Salida esperada: 1.4142135623730951
```


```python
distancia_hamming([1,1],[0,1])
#Salida esperada: 2
```


```python
distancia_manhattan([1,0],[0.5,0.5])
#Salida esperada: 1.0
```


```python
distancia_euclidea([1,0],[0.5,0.5])
#Salida esperada: 0.7071067811865476
```


```python
distancia_hamming([1,0,1,1],[1,1,1,0])
# Salida esperada: 2
```

### Ejercicio 2 [0.25 ptos]

La asignación de clusters a cada ejemplo durante cada iteración del algoritmo la almacenaremos en una lista que llamaremos *lista de clasificación* cuyos elementos son a su vez listas con dos elementos: cada dato concreto y el número de cluster que tiene asignado. Para comenzar, definir una función `clasificacion_inicial_vacia(ejemplos)` que recibe un conjunto de datos y crea una lista de clasificación, en el que el número de cluster de cada dato está sin definir.


```python
# Solución:

```

Ejemplos de uso:


```python
clasificacion_inicial_vacia(datos_reales)
# Salida esperada:
# 
# [[[175.0, 71.0], None],
#  [[158.0, 54.5], None],
# [[190.0, 95.0], None],
# [[163.0, 57.5], None],
# ...
```


```python
clasificacion_inicial_vacia(datos_binarios)
# Salida esperada:
# [[[1, 0, 1, 1, 0, 1], None],
#  [[1, 0, 0, 1, 0, 1], None],
# ...
```

### Ejercicio 3 [0.25 ptos]

Los centros que se vayan calculando durante el algoritmo los vamos a almacenar en un lista de K componentes a la que llamaremos lista de centros. Para definir los centros iniciales, vamos a elegir aleatoriamente K ejemplos distintos de entre los datos de entrada. Se pide definir una función `centros_iniciales(ejemplos,k)` que recibiendo el conjunto de datos de entrada, y el valor de K (número total de clusters), genera una lista inicial de centros.


```python
# Solución:

```

Ejemplos de uso:


```python
centros_iniciales(datos_reales,2)
# Salida esperada (puede variar, es aleatoria)
# [[182.0, 84.0], [187.0, 91.0]]
```


```python
centros_iniciales(datos_reales,4)
# Salida esperada (puede variar, es aleatoria)
# [[160.0, 55.0], [185.0, 88.0], [180.0, 82.0], [168.0, 62.0]]
```


```python
centros_iniciales(datos_binarios,2)
# Salida esperada (puede variar, es aleatoria)
```

### Ejercicio 4 [0.5 ptos]

Definir una función `calcula_centro_mas_cercano(ejemplo,centros,distancia)` que recibiendo como entrada un ejemplo, una lista de centros de cada cluster y una función de distancia `distancia` (una de `distancia_euclidea`, `distancia_manhattan` o `distancia_hamming`), devuelve el número de cluster cuyo centro está más cercano al ejemplo (los clusters los numeraremos de 0 a K-1), usando la función `distancia` recibida para calcular la distancia entre el ejemplo y cada centro.


```python
# Solución:

```

Ejemplos de uso:


```python
calcula_centro_mas_cercano([6.0, 2.9, 5.0, 1.0],
                           [[5.8, 2.7, 3.9, 1.2], 
                            [6.0, 3.0, 4.8, 1.8],
                            [6.3, 2.8, 5.1, 1.5]],distancia_euclidea)
# Salida:
# 2
```


```python
calcula_centro_mas_cercano([41.0],[[39.0],[45.0]],distancia_manhattan)
# Salida:
# 0
```


```python
calcula_centro_mas_cercano([1,0,1,1],[[1,1,1,0],[1,0,0,0]],distancia_hamming)
# Salida:
# 0
```


```python
calcula_centro_mas_cercano(datos_reales[0],[[182.0, 84.0], [187.0, 91.0]],distancia_euclidea)
# Salida:
# 0
```


```python
calcula_centro_mas_cercano(datos_reales[2],[[163.0, 57.5], [175.0, 71.0], [165.0, 59.0], [178.0, 76.5]],distancia_manhattan)
# Salida:
# 3
```


```python
calcula_centro_mas_cercano([1,0],[[0,0],[1,0],[1,1]],distancia_hamming)
# Salida:
# 1
```

### Ejercicio 5 [0.5 ptos]

Define la función `asigna_cluster_a_cada_ejemplo(clasif,centros,distancia)` que recibiendo una lista de clasificación `clasif`, una lista de centros `centros` y una función de distancia `distancia`, devuelva el valor actualizado de `clasif` de manera que en cada dato, el cluster asignado sea el del centro en `centros` más cercano al ejemplo (usando la función `distancia` recibida).


```python
# Solución:

```

Ejemplos de uso:


```python
clas=clasificacion_inicial_vacia(datos_reales)
centr= [[155.0, 50.0],[163.0, 57.5]]
asigna_cluster_a_cada_ejemplo(clas,centr,distancia_euclidea)
clas
# Salida esperada:
# [[[175.0, 71.0], 1],
# [[158.0, 54.5], 0],
# [[190.0, 95.0], 1],
# [[163.0, 57.5], 1],
# [[182.0, 84.0], 1],
# [[161.0, 56.0], 1],
# [[177.0, 73.5], 1],
# [[155.0, 50.0], 0],
# ...
```


```python
clas_bin = clasificacion_inicial_vacia(datos_binarios)
centr_bin = [[1,0,1,1,0,1],[0,1,0,0,1,0]]
asigna_cluster_a_cada_ejemplo(clas_bin,centr_bin,distancia_hamming)
clas_bin
# Salida esperada:
# [[[1, 0, 1, 1, 0, 1], 0],
# [[1, 0, 0, 1, 0, 1], 0],
# [[0, 1, 0, 0, 1, 0], 1],
# [[1, 0, 1, 1, 1, 1], 0],
# [[0, 1, 0, 0, 0, 0], 1],
# ...
```

### Ejercicio 6 [0.75 ptos]

Definir una función `recalcula_centros(clasif,num_clusters)` que recibiendo una lista de clasificación y el número total de clusters, devuelve la lista con los nuevos centros calculados como media aritmética de los datos de cada cluster.


```python
# Solución:

```

Ejemplo de usos (tomando `clas` y `clas_bin` con los valores de los ejemplos anteriores):


```python
recalcula_centros(clas,2)
# Salida esperada:
# [[157.33333333333334, 52.333333333333336],
# [174.41176470588235, 72.79411764705883]]
```


```python
recalcula_centros(clas_bin,2)
# Salida esperada:
# [[1.0,
#   0.14285714285714285,
#   0.7142857142857143,
#   0.8571428571428571,
#   0.14285714285714285,
#   0.8571428571428571],
#  [0.0, 0.8, 0.0, 0.2, 0.8, 0.2]]
```

### Ejercicio 7 [0.5 ptos]

Usando las funciones definidas anteriormente, definir la función `k_medias(k,ejemplos,distancia)` que implementa la siguiente versión del algoritmo k-medias:


```python
# -------------------------------------------------------------------
# K-MEDIAS(K,ejemplos, distancia)

# 1. Inicializar c_i (i=1,...,k) (aleatoriamente o con algún criterio
#    heurístico) 
# 2. REPETIR (hasta que los c_i no cambien):
#    2.1 PARA j=1,...,N, HACER: 
#        Calcular el cluster correspondiente a cada ejemplo x_j, escogiendo, de entre
#        todos los c_i, el c_h tal que distancia(x_j,c_h) sea mínima 
#    2.2 PARA i=1,...,k HACER:
#        Asignar a c_i la media aritmética de los ejemplos asignados al
#        cluster i-ésimo      
# 3. Devolver c_1,...,c_k y el cluster asignado a cada dato
# ---------------------------------------------------------------------
```

El algoritmo debe devolver como salida una tupla con los centros finalmente obtenidos y con la lista de clasificación.


```python
# Solución:

```

Ejemplos de uso:


```python
k_medias(2,datos_reales,distancia_euclidea)
# Salida esperada (puede variar):
# ([[161.7, 56.5], [182.0, 82.95]],
#  [[[175.0, 71.0], 1],
#  [[158.0, 54.5], 0],
#  [[190.0, 95.0], 1],
#  [[163.0, 57.5], 0],
#  [[182.0, 84.0], 1],
#  [[161.0, 56.0], 0],
#  [[177.0, 73.5], 1],
#  [[155.0, 50.0], 0],
#  [[187.0, 91.0], 1],
#  ...
```


```python
k_medias(2,datos_reales,distancia_manhattan)
# Salida esperada (puede variar):
# ([[182.0, 82.95], [161.7, 56.5]],
#  [[[175.0, 71.0], 0],
#   [[158.0, 54.5], 1],
#   [[190.0, 95.0], 0],
#   [[163.0, 57.5], 1],
#   [[182.0, 84.0], 0],
#  ...
```

Por último, probamos con `datos_binarios` y la distancia de Hamming, que es donde esta métrica realmente tiene sentido (dos personas con respuestas parecidas al cuestionario deberían quedar en el mismo grupo):


```python
k_medias(2,datos_binarios,distancia_hamming)
# Salida esperada (puede variar):
# ([[0.8571428571428571,
#    0.0,
#    0.5714285714285714,
#    0.7142857142857143,
#    0.2857142857142857,
#    0.7142857142857143],
#   [0.2, 1.0, 0.2, 0.4, 0.6, 0.4]],
#  [[[1, 0, 1, 1, 0, 1], 0],
#   [[1, 0, 0, 1, 0, 1], 0],
#   [[0, 1, 0, 0, 1, 0], 1],
#   [[1, 0, 1, 1, 1, 1], 0],
#   [[0, 1, 0, 0, 0, 0], 1],
#   [[1, 1, 1, 1, 0, 1], 1],
#   [[0, 0, 0, 0, 1, 0], 0],
#   [[1, 0, 1, 0, 0, 1], 0],
#   [[0, 1, 0, 1, 1, 0], 1],
#   [[1, 0, 1, 1, 0, 0], 0],
#   [[0, 1, 0, 0, 1, 1], 1],
#   [[1, 0, 0, 1, 0, 1], 0]])
```
