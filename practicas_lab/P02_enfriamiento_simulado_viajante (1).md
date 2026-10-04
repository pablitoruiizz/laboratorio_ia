# Práctica 2 - Inteligencia Artificial

## Tema 1: Metaheurísticas para optimización

### Búsqueda local: enfriamiento simulado
### Problema del viajante

En esta práctica se verá la implementación en Python del algoritmo de enfriamiento simulado y su uso para intentar resolver distintos casos concretos del problema del viajante.

La práctica se estructura en tres partes. 

* En la primera parte, vamos a implementar la representación del problema del viajante para problemas de búsqueda local. 
* En la segunda implementaremos el algoritmo de enfriamiento simulado.
* En la tercera parte, lo aplicaremos para resolver distintos problemas del viajante.

Necesitaremos estos dos módulos de la biblioteca estándar (consultar en https://www.python.org/ lo que proporcionan estos dos módulos; algunos de los métodos serán muy útiles en esta práctica) 


```python
import random
import math
```

En la teoría hemos visto que cualquier problema de optimización que quisieramos resolver mediante búsqueda local lo íbamos a representar (una vez hayamos decidido una representación para los estados) mediante definición de las siguientes funciones:

* Una función para generar un estado inicial 
* Una función para generar un sucesor a partir de un estado dado. 
* Una función de valoración (la función a optimizar)

La siguiente clase Problema_Busqueda_Local va a proporcionar un patrón general para representar problemas de optimización a resolver mediante búsqueda local. Los problemas concretos serán subclases de esta clase, obtenidos definiendo sus métodos de manera concreta.

Nótese que además de los tres métodos anteriormente mencionados, incluimos un atributo "mejor" para definir si se trata de un problema de minimización o de maximización. En concreto, "mejor" contendrá la función "menor que" si se trata de minimizar, o la función "mayor que" si se trata de maximizar (por defecto, el problema será de minimización)   


```python
class Problema_Busqueda_Local(object):
    """Clase abstracta para un problema de búsqueda local. Los problemas
    concretos habría que definirlos como subclases de esta clase,
    implementando genera_estado_inicial, genera_sucesor y valoración. Como
    atributo de dato, tendremos "mejor", que va a almacenar la función "menor
    que", o "mayor que" dependiendo de que se trate, respectivamente, de
    minimizar o maximizar."""     


    def __init__(self,mejor=lambda x,y: x < y ):
        self.mejor=mejor

    def genera_estado_inicial(self):
        """Genera, posiblemente con cierta componente aleatoria y heurística,
           un estado para empezar la búsqueda ."""
        pass
        
    def genera_sucesor(self, estado):
        """ Devuelve un estado "sucesor" del que recibe como
            entrada. Usualmente, esta función tendrá cierta componente
            aleatoria y heurística."""
        pass

    def valoracion(self, estado):
        """Devuelve la valoración de un estado. Es el valor a optimizar."""  
        pass
```

## Parte I: Problema del viajante como problema de búsqueda local


### Ejercicio 1

Implementar la clase Viajante_BL de problemas del viajante para ser resueltos mediante búsqueda local. La clase debe ser subclase de Problema_Busqueda_Local y contener además dos atributos de datos adicionales: uno con la lista de las ciudades y otro con una función distancia que se va a usar para calcular la distancia entre cualesquiera dos ciudades (estos dos datos se reciben como argumento por el constructor de la clase). 

Tanto el método genera_estado_inicial como el método genera_sucesor se han de definir como se ha explicado en clase. Es decir:
* genera_estado_inicial construye, aleatoriamente, una permutación de las ciudades
* genera_sucesor construye un circuito a partir de uno dado, invirtiendo el subcircuito entre dos ciudades elegidas aleatoriamente.    

Nota: para este ejercicio, pueden ser útiles las funciones `random.shuffle` y `random.choice` (consultar en la documentación del módulo random en https://www.python.org/


```python
# Escribe aquí la solución

# class Viajante_BL(Problema_Busqueda_Local):
#    def __init__(self, ciudades, distancia):
#         super().__init__()
#         ...
#        
#    def genera_estado_inicial(self):
#        ...
#
#    def genera_sucesor(self, estado):
#        ...
#
#    def valoracion(self, estado):
#        ....
```

### Ejercicio 2

La función distancia_euc2D calcula la distancias entre dos ciudades a partir de sus coordenadas 


```python
def distancia_euc2D(c1,c2,coords):
    """ Función que recibe dos ciudades y devuelve la distancia entre ellas,
    calculada mediante distancia euclidea en el plano. El tercer argumento de
    la función contiene las coordenadas de todas las ciudades del problema (en
    foma de lista o de diccionario)""" 
    coord_c1= coords[c1]
    coord_c2= coords[c2]
    return math.hypot(coord_c1[0]-coord_c2[0],coord_c1[1]-coord_c2[1])
```

El siguiente diccionario relaciona el nombre de las capitales andaluzas con sus coordenadas: 


```python
andalucia = {"almeria": (409.5, 93),
             "cadiz":   (63, 57),
             "cordoba": (198, 207),
             "granada": (309, 127.5),
             "huelva":  (3, 139.5),
             "jaen":    (295.5, 192),
             "malaga":  (232.5,  75),
             "sevilla": ( 90, 153)}
```

Usando esto, definir una variable viajante_andalucia, asignándole la instancia de la clase Viajante_BL que define el problema del viajante por las capitales andaluzas.


```python
# Escribe aquí la solución

# viajante_andalucia = Viajante_BL(...)
```

Con esta instancia concreta del problema del viajante, probar la implementación realizada de genera_estado_inical y de genera_sucesor.

Por ejemplo: 


```python
circuito=viajante_andalucia.genera_estado_inicial()
```


```python
circuito

# Posible respuesta:
# ['cadiz', 'huelva', 'almeria', 'jaen', 'malaga', 'sevilla', 'granada', 'cordoba']
```


```python
viajante_andalucia.genera_sucesor(circuito)

# Posible respuesta:
# ['cadiz', 'huelva', 'jaen', 'almeria', 'malaga', 'sevilla', 'granada', 'cordoba']
```


```python
circuito_suc = viajante_andalucia.genera_sucesor(circuito)
circuito_suc

# Posible respuesta:
# ['cadiz', 'huelva', 'almeria', 'granada', 'sevilla', 'malaga', 'jaen', 'cordoba']
```

Nótese que, puesto que estas funciones tienen una componente aleatoria, no tienen que devolver lo mismo que en estos ejemplos.

## Parte II: implementación del algoritmo de enfriamiento simulado

### Ejercicio 3

Definir una función sorteo(p), que recibe una probabilidad p y devuelve aleatoriamente True ó False, de tal manera que la probabilidad de devolver True sea precisamente p.


```python
# Escribe aquí la solución

```


```python
# Un experimento:

def experimento (p,n):
    cont=0
    for _ in range(n):
        if sorteo(p):
            cont += 1
    return cont/n 

# Esta función, para n grande, debe devolver aproximadamente p.
```


```python
# Prueba
experimento(0.3,100000)
```

### Ejercicio 4

Definir una función 

aceptar_e_s(valor_candidata, valor_actual, T, mejor)

que implementa el mecanismo de aceptación de nuevos estados dentro del algoritmo de enfriamiento simulado, donde:

* valor_candidata es la valoración del estado nuevo
* valor_actual es la valoración del estado actual
* T es la temperatura
* mejor es la función que se usa para comprobar si un valor es mejor qaue otro

La función debe devolver True o False, dependiendo de si la candidata se acepta como nueva actual o no. 

Nota: se necesitará la función math.exp, que calcula la exponenciación del número e. 


```python
# Escribe aquí la solución

```


```python
# Ejemplo de uso
aceptar_e_s(12,11.5,10,lambda x,y: x < y)

# Posible respuesta;
# True
```

### Ejercicio 5

Implementar el algoritmo de enfriamiento simulado, completando el siguiente código:


```python
def enfriamiento_simulado(problema, t_inicial, factor_descenso,
                           n_enfriamientos, n_iteraciones):  

     """Función que implementa el algoritmo de optimización mediante
        enfriamiento simulado. Recibe como entrada:
        - Un problema de búsqueda local (clase Problema_Busqueda_Local)
        - Una temperatura inicial
        - Un factor de descenso (para el programa de enfriamiento)
        - Un número total de enfriamientos 
        - Un número de iteraciones para cada temperatura"""

    actual=???????
    valor_actual=?????????
    mejor=????????
    valor_mejor=?????
    T=??????
    for _ in range(???????):
        for _ in range(???????):
            candidata = ???????
            valor_candidata = ????
            if ???????????:
                actual=candidata
                valor_actual=valor_candidata
                if problema.mejor(valor_actual, valor_mejor):
                    mejor = actual
                    valor_mejor = valor_actual
        T *= ?????????
    return (??????,???????)
```

## Parte III: Resolviendo diversos problemas del viajante

### Ejercicio 6

Tratar de resolver el problema del viajante por Andalucía, usando enfriamento simulado. Probar con diversos valores para los parámetros de entrada al algoritmo  de enfriamiento simulado:


```python
# Escribe aquí la solución



# Posible respuesta: 
# (['malaga', 'cadiz', 'huelva', 'sevilla', 'cordoba', 'jaen', 'almeria',
#   'granada'], 
#   929.9255755927754)


```

### Ejercicio 7

Definir una función cuadrado_puntos_bl(n), que recibiendo un número natural n, devuelve una instancia del problema del viajante (para búsqueda local), considerando que las ciudades son $4n$ puntos del plano dispuestos en forma de cuadrado, tal y como se ha explicado en clase.




```python
# Escribe aquí la solución

```

Probar el algoritmo de enfriamiento simulado, en este problema del cuadrado de puntos, para distintos valores de n.

Nótese que en esta caso, por consideraciones geométricas, se conoce que el camino óptimo es de distancia $4n$. Esto nos puede servir de referencia para comprobar el rendimiento del algoritmo.


```python
enfriamiento_simulado(cuadrado_puntos_bl(3), 5, 0.95, 100, 100)

# Posible respuesta:
# ([(2, 0), (3, 0), (3, 1), (3, 2), (3, 3), (2, 3), (1, 3), (0, 3), 
#   (0, 2), (0, 1), (0, 0), (1, 0)], 
#  12.0)
```


```python
 enfriamiento_simulado(cuadrado_puntos_bl(15), 35, 0.95, 100, 100)[1]

# Posible respuesta
# 74.0
```

### Ejercicio 8

Finalmente, se pide probar la potencia de nuestro algoritmo con dos problemas del viajante reales, algo más complicados. Estos problemas se han tomado de TSPLIB, una biblioteca con problemas del viajante resueltos
http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/


```python
# Problema Berlin 52:
# 52 lugares de Berlin (Groetschel)
# La ruta óptima está valorada en 7542
# La siguiente variable contiene las coordinadas de los lugares. La distancia
# entre ciudades es la euclídea en dos dimensiones.

berlin52=[(565.0, 575.0),
        (25.0, 185.0),
        (345.0, 750.0),
        (945.0, 685.0),
        (845.0, 655.0),
        (880.0, 660.0),
        (25.0, 230.0),
        (525.0, 1000.0),
        (580.0, 1175.0),
        (650.0, 1130.0),
        (1605.0, 620.0 ),
        (1220.0, 580.0),
        (1465.0, 200.0),
        (1530.0, 5.0),
        (845.0, 680.0),
        (725.0, 370.0),
        (145.0, 665.0),
        (415.0, 635.0),
        (510.0, 875.0  ),
        (560.0, 365.0),
        (300.0, 465.0),
        (520.0, 585.0),
        (480.0, 415.0),
        (835.0, 625.0),
        (975.0, 580.0),
        (1215.0, 245.0),
        (1320.0, 315.0),
        (1250.0, 400.0),
        (660.0, 180.0),
        (410.0, 250.0),
        (420.0, 555.0),
        (575.0, 665.0),
        (1150.0, 1160.0),
        (700.0, 580.0),
        (685.0, 595.0),
        (685.0, 610.0),
        (770.0, 610.0),
        (795.0, 645.0),
        (720.0, 635.0),
        (760.0, 650.0),
        (475.0, 960.0),
        (95.0, 260.0),
        (875.0, 920.0),
        (700.0, 500.0),
        (555.0, 815.0),
        (830.0, 485.0),
        (1170.0, 65.0),
        (830.0, 610.0),
        (605.0, 625.0),
        (595.0, 360.0),
        (1340.0, 725.0),
        (1740.0, 245.0)]

```


```python
# Escribe aquí la definición de viajante_berlin52 y haz la prueba.



enfriamiento_simulado(viajante_berlin52,1000,0.95,300,300)[1]

# Posible respuesta:
# 7598.442340904538

# La ruta óptima está valorada en 7542.
```


```python
# Problema pr76
# 76 ciudades (presentado por Padberg y Rinaldi)
# La ruta óptima está valorada en 108159
# La siguiente variable contiene las coordinadas de los lugares. La distancia
# entre ciudades es la euclídea en dos dimensiones.

pr76=[(3600, 2300),
      (3100, 3300),
      (4700, 5750),
      (5400, 5750),
      (5608, 7103),
      (4493, 7102),
      (3600, 6950),
      (3100, 7250),
      (4700, 8450),
      (5400, 8450),
      (5610, 10053),
      (4492, 10052),
      (3600, 10800),
      (3100, 10950),
      (4700, 11650),
      (5400, 11650),
      (6650, 10800),
      (7300, 10950),
      (7300, 7250),
      (6650, 6950),
      (7300, 3300),
      (6650, 2300),
      (5400, 1600),
      (8350, 2300),
      (7850, 3300),
      (9450, 5750),
      (10150, 5750),
      (10358, 7103),
      (9243, 7102),
      (8350, 6950),
      (7850, 7250),
      (9450, 8450),
      (10150, 8450),
      (10360, 10053),
      (9242, 10052),
      (8350, 10800),
      (7850, 10950),
      (9450, 11650),
      (10150, 11650),
      (11400, 10800),
      (12050, 10950),
      (12050, 7250),
      (11400, 6950),
      (12050, 3300),
      (11400, 2300),
      (10150, 1600),
      (13100, 2300),
      (12600, 3300),
      (14200, 5750),
      (14900, 5750),
      (15108, 7103),
      (13993, 7102),
      (13100, 6950),
      (12600, 7250),
      (14200, 8450),
      (14900, 8450),
      (15110, 10053),
      (13992, 10052),
      (13100, 10800),
      (12600, 10950),
      (14200, 11650),
      (14900, 11650),
      (16150, 10800),
      (16800, 10950),
      (16800, 7250),
      (16150, 6950),
      (16800, 3300),
      (16150, 2300),
      (14900, 1600),
      (19800, 800),
      (19800, 10000),
      (19800, 11900),
      (19800, 12200),
      (200, 12200),
      (200, 1100),
      (200, 800)]
```


```python
# Escribe aquí la definición de viajante_pr76 y haz la prueba.

enfriamiento_simulado(viajante_pr76,200000,0.95,1000,1000)[1]

# Posible respuesta:
# 111378.8272440735

# La ruta óptima está valorada en 108159
```


```python

```
