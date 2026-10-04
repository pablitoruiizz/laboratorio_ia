# Inteligencia Artificial

## Examen Final 25-26

### 13 de Enero, 2026


```python
# Apellidos:
# Nombre:
```

Un sistema de maximización basado en enjambre de partículas consta de $n$ partículas que ocupan una posición en $R^m$ y que llevan asociado un vector de velocidad, junto con una función fitness que asocia un valor a cada
posición. El objetivo es encontrar la posición donde la función fitness toma el valor máximo. Los siguientes ejercicios van orientados a la implementación y evaluación de un sistema de este tipo.


```python
import random
import math
```

El comportamiento de una partícula en este tipo de sistemas viene dado por:

1. La posición y velocidad inicial de las partículas se establece aleatoriamente.
2. Todas las partículas actualizan su posición en paralelo.
3. Para cada partícula, su posición en el instante $t + 1$ responde a la siguiente fórmula: $ x_i^{t+1} = x_i^t + v_i^{t+1}$

4. Para cada partícula, su velocidad en el instante $t + 1$ responde a la siguiente fórmula:

$$ v_i^{t+1} = W v_i^t + c_1 r_1 (m_i^t  - x_i^t) + c_2 r_2 (b^t - x_i^t)$$

Donde $W \in R$ representa la inercia,  $c_1$ representa la influencia individual, $c_2$ representa la influencia social, $r_1$ y $r_2$ son dos valores aleatorios en el intervalo $[0, 1]$ y $m_i^t$ y $b^t$ representan la mejor posición de la partícula $i$ en los pasos $0, . . . , t$ y la mejor posición de cualquier partícula $i$ en los pasos $0, . . . , t$ respectivamente.

### Ejercicio 1 [1.5 ptos]
Implementa la clase *Particula*, con el método constructor y un método llamado *actualiza* que permita actualizar la posición y velocidad de la partícula.

El método constructor debe recibir los parámetros del sistema asociados al comportamiento de la partícula ($W,c_1,c_2$) así como la función a maximizar y el valor de $m$ (el número de dimensiones de la función a maximizar), y debe inicializar la posición y velocidad inicial de la partícula. Además, deberá inicializar un atributo *bestpos* ($m_i^t$) que almacenará la mejor posición por la que haya pasado la partícula (inicialmente corresponderá con la posición inicial) y un atributo *bestvalue* que almacenará el valor asociado a la mejor posición por la que haya pasado la partíucla (inicialmente corresponderá con el valor asociado a la posición inicial).

El método *actualiza* debe recibir el valor de $b^t$ y debe actualizar la posición y velocidad de la partícula así como la posición $m_i^t$ y su valor asociado. El método debe devolver la posición $m_i^t$ actualizada y su valor asociado.


```python
#Solución:

```

### Ejercicio 2 [1.5 ptos]
Implementa la clase PSO, con el método constructor, un método llamado *actualiza* que permita actualizar la posición y velocidad de todas las partículas en el sistema y un método llamado *ejecutar* que lleve a cabo la ejecución del algoritmo y devuelva las coordenadas de maximización de la función así como su valor.

El método constructor debe recibir los parámetros del sistema ($n$,número de iteraciones -condición de parada-,$W$,$c_1$,$c_2$) así como la función a optimizar y el valor de $m$ (el número de dimensiones de la función a maximizar), y debe crear tantas partículas como indique el parámetro $n$. Además, deberá inicializar un atributo *bestposglobal* que almacenará la posición $b^t$ y un atributo *bestvalueglobal* que almacenará el valor asociado a dicha posición.

El método *actualiza* no recibe parámetros de entrada y debe actualizar la posición y velocidad de cada una de las partículas, así como la posición $b^t$ y su valor asociado como resultado de una iteración del sistema.

El método *ejecutar* tampoco recibe parámetros de entrada y como se ha indicado, lleva a cabo la ejecución del algoritmo y devuelve las coordenadas de maximización de la función así como su valor.



```python
#Solución:

```

### Ejercicio 3 [0.5 ptos]
Evalúa el funcionamiento de las clases anteriores tratando de encontrar las coordenadas de maximización de las siguientes funciones:
    
1. $f(x)=-(x-3)^2$
2. $g(x,y)=-(x-1)^2 - (y-2)^2$
3. $h(x,y,z)= 1 - |x^3 + y^2 + z|$


```python
# Solución:

```
