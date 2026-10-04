# Inteligencia Artificial
## Examen Práctico
## 27 de octubre, 2025


```python
import random
import math
```


```python
# Nombre:
# Apellidos:
```

En este examen vamos a implementar en Python una versión simplificada del algoritmo ID3 (preparada para trabajar sólo con atributos booleanos) y vamos a evaluar su rendimiento sobre un condjunto de entrenamiento relacionado con propiedades de frutas.

A continuación se detalla la lista de atributos y se instancia un conjunto de entrenamiento con información sobre diferentes frutas. El conjunto de entrenamiento viene dado por una lista de listas, donde cada lista interna representa los valores booleanos de los atributos para diferentes frutas.


```python
atributos = [
    'es_dulce',
    'es_cítrica',
    'es_tropical',
    'tiene_semillas',
    'es_roja',
    'se_come_cruda'
]

frutas = [[1, 0, 0, 1, 1, 1],
 [1, 0, 1, 0, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [0, 1, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [0, 1, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 0],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 0],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 1, 1, 1],
 [0, 0, 0, 1, 0, 0],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 1, 1, 1, 1, 1],
 [0, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 0, 0],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 1, 1],
 [0, 1, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 1, 0, 1, 1],
 [0, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [0, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [0, 1, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 1, 1, 1, 1, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 0, 0, 1],
 [0, 1, 1, 1, 1, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 1, 1, 1, 0, 0],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 0, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 0, 1, 0, 1],
 [1, 1, 1, 1, 1, 1],
 [1, 0, 1, 1, 1, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 0, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1],
 [1, 1, 1, 1, 0, 1]]
```

### Ejercicio 1  [0.5 pto]

La primera función que necesitamos definir para construir nuestra versión del algoritmo ID3 será la función `entropia(D,atr_clasif)` que recibiendo una lista `D` de ejemplos, en la que cada ejemplo es una lista de valores booleanos, y un entero `atr_clasif`, devuelva la entropía del conjunto formado por los valores del atributo que ocupa la posición `atr_clasif` en los ejemplos de `D`. En lo que sigue, identificaremos el valor `1` con _positivo_ y el valor `0` con _negativo_. 

La fórmula de la entropía:

$$ Ent(D)= - \frac{|P|}{|D|} log_2 \frac{|P|}{|D|} - \frac{|N|}{|D|} log_2 \frac{|N|}{|D|} $$

donde $P$ y $N$ son, respectivamente, los subconjuntos de ejemplos positivos y negativos de $D$. 



```python
#Solución:

```

Puedes probar tu código aquí:


```python
D1 = [[0,1,1,1,1],
     [0,0,1,1,1],
     [0,0,0,1,1],
     [0,0,0,0,1]]

entropia(D1,0)
#Salida esperada: 0
```


```python
entropia(D1,1)
#Salida esperada: 0.8112781244591328
```


```python
entropia(frutas,1)
#Salida esperada: 0.8112781244591328
```


```python
entropia(frutas,2)
#Salida esperada: 0.8414646362081757
```


```python
entropia(frutas,3)
#Salida esperada: 0.19439185783157623
```

### Ejercicio 2  [0.75 ptos]

A continuación vamos a definir una función `mejor_atributo(D,atr_clasif,atributos)` que recibiendo un conjunto de entrenamiento `D`, un entero `atr_clasif` indicando la posición que ocupa el atributo de clasificación y la lista de las posiciones de los atributos disponibles devuelva la posición del atributo que al ser utilizado para dividir el conjunto de entrenamiento produzca una mayor ganancia de información.

Fórmula de la ganacia de información:

$$ Ganancia(D,A) = Ent(D) - \sum_{v \in Valores(A)} \frac{|D_v|}{|D|} Ent(D_v) $$

donde $D_v$ es el subconjunto de ejemplos de $D$ con valor del atributo $A$ igual a $v$.



```python
#Solución:

```

Puedes probar tu código aquí:


```python
D2 = [[1,1,1,1,1],
     [0,0,0,1,1],
     [1,1,1,0,0],
     [0,0,0,0,0]]

mejor_atributo(D2,4,[0,1,2,3])
#Salida esperada: 3
```


```python
D3 = [[1,1,1,1,1],
     [0,0,0,1,1],
     [1,1,1,0,0],
     [0,0,0,0,0]]

mejor_atributo(D3,1,[0,2,3,4])
#Salida esperada: 0
```


```python
mejor_atributo(frutas,1,[0,2,3,4,5])
#Salida esperada: 2
```


```python
mejor_atributo(frutas,2,[0,1,3,4,5])
#Salida esperada: 1
```

### Ejercicio 3 [0.5 ptos]
 
A continuación, vamos a definir una función `mayoritario(D,atributo)` que recibiendo un conjunto de ejemplos `D` y un entero `atributo` indicando la posición de un atributo, devuelva el valor mayoritario de dicho atributo en los ejemplos en `D`. En caso de empate, se debe devolver el valor 1.



```python
#Solución:

```

Puedes probar tu código aquí:


```python
D4 = [[0,1,1,1,1],
     [0,0,1,1,1],
     [0,0,0,1,1],
     [0,0,0,0,1]]

mayoritario(D4,4)
#Salida esperada: 1
```


```python
mayoritario(D4,3)
#Salida esperada: 1
```


```python
mayoritario(frutas,0)
#Salida esperada: 1
```


```python
mayoritario(frutas,1)
#Salida esperada: 0
```


```python
mayoritario(frutas,2)
#Salida esperada: 1
```

### Ejercicio 4  [1 pto]

Ahora estamos listos para implementar el algoritmo ID3. Para ello definiremos una función recursiva `ID3(D,atr_clasif,atributos)` donde `D` es el conjunto de entrenamiento, `atr_clasif` es la posición del atributo que contiene el valor de clasificación, y `atributos` es la lista de las posiciones de los atributos disponibles. Esta función debe comportarse de la siguiente manera:

* Si todos los ejemplos en `D` tienen el mismo valor en el atributo `atr_clasif` devolver dicho valor.  
* Si `atributos` está vacı́o, devolver el valor mayoritario del atributo `atr_clasif` en `D`
* En caso contrario, devolver un diccionario `{"atr":atr,"izq":izq,"der":der}` donde:

     * `atr` es un entero que indica la posición del mejor atributo de `atributos` (utilizando la función `mejor_atributo`) 
     * `izq` es el resultado de llamar a la función `ID3` con los ejemplos en `D` que tienen valor positivo para el atributo `atr` y con el conjunto resultante de eliminar el atributo `atr` de `atributos`.
     * `der` es el resultado de llamar a la función `ID3` con los ejemplos en `D` que tienen valor negativo para el atributo `atr` y con el conjunto resultante de eliminar el atributo `atr` de `atributos`.

Completar el código que aparece a continuación, con la implementación que se ha descrito del algoritmo ID3. Si fuera necesario, añadir más líneas de código de las que aparecen. 


```python
def ID3(D,target,atributos):
    if ???????  : # si todos los ejemplos en D tienen el mismo valor de clasificación
        return mayoritario(D,target)
    if ???????:   # si atributos está vacío 
        return mayoritario(D,target)
    a = mejor_atributo(D,target,atributos)
    pos = ???????         # ejemplos en D que en a tienen valor positivo
    neg = ???????         # ejemplos en D que en a tienen valor negativo
    nuevos_atributos=????? # eliminar de atributos el mejor atributo 
    izq = mayoritario(D,target) if len(pos)==0 else ID3(pos,target,nuevos_atributos)
    der = mayoritario(D,target) if len(neg)==0 else ID3(neg,target,nuevos_atributos)
    return {"atr":a,"izq":izq,"der":der}
```

Puedes probar tu código aquí:


```python
D5 = [[1,1,1,1,1],
     [0,0,0,1,1],
     [1,1,1,0,0],
     [0,0,0,0,0]]

ID3(D5,4,[0,1,2,3])
#Salida esperada: {'atr': 3, 'izq': 1, 'der': 0}
```


```python
D6 = [[1,0,1,1,1],
     [1,0,0,1,1],
     [1,0,1,0,0],
     [0,1,0,0,0]]

ID3(D6,0,[1,2,3,4])
#Salida esperada: {'atr': 1, 'izq': 0, 'der': 1}
```


```python
D7 = [[1,1,1,1,1],
     [1,0,0,0,0],
     [0,1,1,0,1],
     [0,1,0,0,0]]

ID3(D7,0,[1,2,3,4])
#Salida esperada: {'atr': 1, 'izq': {'atr': 3, 'izq': 1, 'der': 0}, 'der': 1}
```

### Ejercicio 5  [0.75 pto]

A continuación, implementa la función recursiva `clasifica(arbol,ej)`, que recibiendo un árbol de decisión en forma de diccionario (resultante de la ejecución de la función `ID3`) y un ejemplo `ej`, devuelva la clasificación que dicho arbol realiza para el ejemplo `ej`.


```python
#Solución:

```

Puedes probar tu código aquí:


```python
arbol1 = {'atr': 3, 'der': 0, 'izq': 1}

clasifica(arbol1,[0,1,0,0,0])
#Salida esperada: 0
```


```python
arbol2 = {'atr': 3, 'der': {'atr': 1, 'der': 1, 'izq': 0}, 'izq': 1}

clasifica(arbol2,[0,1,0,0,0])
#Salida esperada: 0
```


```python
clasifica(arbol2,[1,1,1,1,1])
#Salida esperada: 1
```


```python
clasifica(arbol2,[1,0,0,0,0])
#Salida esperada: 1
```


```python
atributos = [
    'es_dulce',
    'es_cítrica',
    'es_tropical',
    'tiene_semillas',
    'es_roja',
    'se_come_cruda'
]

arbol_fruta_roja = ID3(frutas,4,[0,1,2,3,5]) #Árbol que trata de predecir si una fruta es roja.

fresa = [1,0,0,0,1,1]

clasifica(arbol_fruta, fresa)
#Salida esperada: 1
```


```python
mango = [1,0,1,1,1,1]

clasifica(arbol_fruta_roja, mango)
#Salida esperada: 0
```


```python
naranja = [1,1,0,1,0,1]

clasifica(arbol_fruta_roja, naranja)
#Salida esperada: 1
```


```python
limon = [0,1,0,1,0,1]

clasifica(arbol_fruta_roja, limon)
#Salida esperada: 0
```

### Probando las implementaciones

Ahora vamos a evaluar el rendimiento de nuestro sistema sobre el conjunto de datos sobre frutas presentado anteriormente.

Primero dividimos el conjunto de frutas en dos subconjuntos. Un 80% de los ejemplos seleccionados al azar para formar el conjunto de entrenamiento y el 20% restantes para formar el conjutno de prueba:


```python
train_data = frutas[:80]
test_data = frutas[80:]
```

Ahora utilizaremos el subconjunto `train_data` para entrenar un árbol de decisión para predecir cada atributo en el conjunto _frutas_ a partir de los demás: 


```python
arboles=[]

for i in range(len(atributos)):
    l = list(range(len(atributos)))
    l.pop(i)
    arboles.append(ID3(train_data,i,l))
```

Por último, evaluamos el rendimiento de cada arbol sobre el conjunto `test_data`:


```python
for i in range(len(atributos)):
    l = list(range(len(atributos)))
    l.pop(i)
    aciertos=0
    for ej in test_data:
        real = ej[i]
        predicho = clasifica(arboles[i],ej)
        if real == predicho:
            aciertos+=1
    print("Feature "+atributos[i]+". Promedio aciertos= "+str(aciertos/len(test_data)))
#Salida esperada:
#Feature es_dulce. Promedio aciertos= 0.95
#Feature es_cítrica. Promedio aciertos= 0.7
#Feature es_tropical. Promedio aciertos= 0.8
#Feature tiene_semillas. Promedio aciertos= 1.0
#Feature es_roja. Promedio aciertos= 0.7
#Feature se_come_cruda. Promedio aciertos= 0.95
```
