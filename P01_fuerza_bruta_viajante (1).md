### Inteligencia Artificial. Tema 1: Metaheurísticas para optimización

### Problema del viajante - Resolución por fuerza bruta

José Luis Ruiz Reina

El objetivo de este ejercicio preliminar es constatar la dificultad de resolver el problema del viajante por fuerza bruta cuando aumenta el número de ciudades.


```python
import random, time, math
from itertools import permutations
```

Se pide definir una clase `Viajante`, que sirva para definir un problema del viajante generado aleatoriamente. El constructor de la clase recibe un valor $n$ que indicará el número de ciudades y un parámetro $escala$. Las coordenadas $x$ e $y$ de cada ciudad se tomaran aleatoriamente en el rango $[-escala,+escala]$.

En concreto, un objeto de esta clase debe tener:

* Un atributo `n_ciudades` con la cantidad de ciudades consideradas (el valor de $n$).

* Un atributo `coordenadas` contiene una lista de $n$ pares $(x,y)$ de coordenadas generado aleatoriamente. 

* Un método `distancia_circuito` que recibe un circuito circular (una lista con una permutación de los índices de las ciudades representando el orden en el que se van a recorrer, tras la última se vuelve a la primera de la lista) y devuelve la distancia total recorrida en ese circuito.




```python
# Completa el siguiente código

# class Viajante():
#    
#    def __init__(self,n,escala):
#        self.n_ciudades = ...
#        self.coordenadas = ...
#        
#    def distancia_circuito(self,cr): # cr circuito
#        ...

```

Algunos ejemplos (tener en cuenta que hay una componente aleatoria y no tiene por qué salir siempre lo mismo): 


```python
# pv5 = Viajante(5,3)
# print("Nº de ciudades pv5: {}".format(pv5.n_ciudades))
# print("Coordenadas pv5: {}".format(pv5.coordenadas))      
# circuito5 = [2,0,3,4,1]
# print("Distancia recorrida circuito {}: {}".format(circuito5, pv5.distancia_circuito(circuito5)))

# Resultado:

# Nº de ciudades pv5: 5
# Coordenadas pv5: [(0.9933341119772914, -1.3142527442924534), (-2.534978816160301, -0.4348823719914323), (2.9237711389309746, 2.5503047663212124), (-2.3038610315148067, 0.2863670972692458), (-2.6807503499258694, 2.66066145309415)]
# Distancia recorrida circuito [2, 0, 3, 4, 1]: 19.70972943031935
```


```python
# pv7 = Viajante(7,6)
# print("Nº de ciudades pv7: {}".format(pv7.n_ciudades))
# print("Coordenadas pv7: {}".format(pv7.coordenadas))      
# circuito7 = [5,0,6,1,3,2,4]
# print("Distancia recorrida circuito {}: {}".format(circuito7, pv7.distancia_circuito(circuito7)))

# Resultado:

# Nº de ciudades pv7: 7
# Coordenadas pv7: [(-4.101506952514783, 2.8132013889243552), (5.850710983895281, 5.122936570240684), (-0.5878950106358758, -1.5103890561568427), (2.906093090298592, 5.110176944095176), (5.58644208048911, 1.2848246079736683), (1.1422345987613527, -5.370749751267727), (4.769985114498658, 5.249400227724447)]
# Distancia recorrida circuito [5, 0, 6, 1, 3, 2, 4]: 45.218967297846184
```

Piensa ahora en un método "sencillo" para resolver el problema del viajante y trata de implementarlo mediante una función `optimizacion_viajante(pv)`. La función debe devolver el mejor circuito y la distancia del mismo. 

Aplícalo para resolver distintas instancias de problemas del viajante (generadas como objetos de la clase anterior) y ve aumentando el número de ciudades para ver cómo se comporta tu método. Saca tus propias conclusiones.  

Nota: para definir la función puede ser útil usar la función `permutations` del módulo `itertools` que se ha importado más arriba. 

Algunos ejemplos:


```python
# optimizacion_viajante(pv5)

# Resultado: ([0, 1, 3, 4, 2], 16.723133150725506)
```


```python
# optimizacion_viajante(pv7)

# Resultado: ([0, 2, 5, 4, 1, 6, 3], 31.983405737842844)
```
