# Práctica 3 - Inteligencia Artificial

## Tema 1: Metaheurísticas para optimización

### Algoritmos genéticos
### Problema de la mochila

José Luis Ruiz Reina

En este ejercicio veremos la implementación en Python de un algoritmo genético y su aplicación para resolver instancias concretas del problema de la mochila.



```python
import random
```

Lo que sigue es la definición genérica de un problema de optimización para ser resuelto mediante un algoritmo genético 


```python
class Problema_Genetico(object):
    """ Clase para representar un problema para que sea abordado mediante un
    algoritmo genético general. Consta de los siguientes atributos:
    - genes: lista de posibles genes en un cromosoma
    - longitud_cromosoma: la longitud de los cromosomas (todos son del mismo tamaño)
    - decodifica: método que recibe el genotipo (cromosoma) y devuelve el
      fenotipo (elemento del problema original que el cromosoma representa) 
    - fitness: método de valoración de los cromosomas (actúa sobre el
      genotipo)"""

    def __init__(self, genes, longitud_cromosoma, decodifica, fitness):
        self.genes = genes
        self.longitud_cromosoma = longitud_cromosoma
        self.decodifica = decodifica
        self.fitness = fitness
```

#### Ejercicio 1

Añadir a la clase anterior, completando las partes que faltan en las siguientes funciones, los principales métodos auxiliares para definir un algoritmo genético:


```python
# def poblacion_inicial(self, N):
#     """Dado un número natural N,
#         construye aleatoriamente una población inicial con N cromosomas"""
#     return [[random.choice(...) for _ in range(...)] 
#             for _ in range(N)]

# def cruza_padres(self, c1, c2):
#    """ Operador de cruce en un punto de dos cromosomas c1 y c2"""
#    pos = random.randrange(1, ...)
#    hijo1 = ... 
#    hijo2 = ...
#    return [hijo1, hijo2]

# def cruza(self, padres):
#    """Dado una población de padres obtiene el resultado de cruzar de dos en dos, 
#       en el orden en el que aparecen"""
#    hijos = []
#    for j in range(0, len(padres), 2):
#        hijos.extend(self.cruza_padres(padres[j], padres[j+1])) 
#    return hijos

# def muta_cromosoma(self, cr, prob):
#    """A partir de un cromosoma cr, se obtiene un nuevo cromosoma resultado de
#       mutar (con una probabilidad prob), cada gen de cr. Nótese que si prob es bajo, entonces
#       es probable que el cromosoma devuelto sea igual a cr"""
#    cm = cr[:] # una copia
#    for i in range(...):
#        if ... :
#            cm[i] = random.choice(self.genes)
#    return cm

# def muta(self, generacion, prob):
#    """Obtiene el resultado de mutar cada cromosoma de población, 
#       con probabilidad prob"""
#    return [self.muta_cromosoma(cr, prob) for cr in generacion]
```

#### Ejercicio 2

Añadir, igualmente, los siguientes métodos, que definen la selección basada en torneo:


```python
# def torneo(self, generacion, k, mejor):
#    """ Recibe una población, un número natural k y
#        y la función mejor (que puede ser max o min, dependiendo si es un problema de 
#        maximización o minimización); y selecciona un cromosoma de la población, 
#        mediante un torneo de tamaño k"""
#    participantes = ...
#    return ...

# def selecciona_torneo(self, generacion, k, mejor, n):
#    """ Recibe una población, un número natural k, 
#        la función mejor (que puede ser max o min, dependiendo si es un problema de 
#        maximización o minimización), y un número n; y selecciona n cromosomas de la población, 
#        mediante n torneos de tamaño k"""    
#    return [self.torneo(generacion, k, mejor) for _ in range(n)]
```

#### Ejercicio 3

Usando la función `binario_a_decimal` que se incluye a continuación, definir un objeto `cuad_gen` que define la representación genética usada en el problema de encontrar el número entero entre 0 y $2^{10}$ que tiene un menor cuadrado (usando lo descrito en las diapositivas, pág. 30) 


```python
def binario_a_decimal(cr):
    return sum(g*(2**i) for (i,g) in enumerate(cr))
```


```python
# Solución:

# cuad_gen = Problema_Genetico(...)
```

#### Ejercicio 4

Consideremos la siguiente versión de algoritmo genético:

* parámetros: `N` (tamaño de cada generación), `prop_padres` (proporción de padres), `prob_mutar` (probabilidad de mutación)
* métodos: 
    * condición de terminación: número de generaciones, `nGen`
    * selección: por torneo de tamaño `k` y cruce en un punto
    * cruce: en un punto

```
generacion <- poblacion_inicial(N)
Repetir nGen veces:
    directos <- seleccionar N * (1 - prop_padres) cromosomas de generacion
    padres <- seleccionar N * prop_padres cromosomas de generacion
    hijos <- cruzar los padres usando cruce en un punto
    hijos <- mutar los genes de hijos con probabilidad `prob_mutar`
    generacion <- hijos y directos
Devolver el fenotipo, y el fitness, del mejor cromosoma de generacion
```

La siguiente función `algoritmo genetico` implementa dicha versión. Sólo queda por implementar la función `nueva_poblacion`, que a partir de una generación obtiene la siguiente. Se pide implementar esa función.


```python
def algoritmo_genetico(problema_genetico, N, prop_padres, prob_mutar, nGen, k, mejor):
    """Algoritmo genético siguiendo el esquema descrito en el enunciado que, 
       además, va mostrando por pantalla estadísticas de las valoraciones de cada generación
       Devuelve el fenotipo del mejor cromosoma de la última generación, junto con su fitness.
       Recibe como entrada los siguientes argumentos:
       - problema_genetico: un objeto de la clase Problema_Genetico, con la representación del
         problema de optimización.
       - N: tamaño de cada generación
       - prop_padres: proporción de una generación que se usa como padres
       - prob_mutar: probabilidad de mutación de un gen
       - nGen: número de generaciones 
       - k: número de cromosomas que intervienen en cada torneo
       - mejor: min o max, dependiendo si el problema es de minimización o de maximización
    """
    n_padres = round(N * prop_padres)
    n_padres = (n_padres if n_padres%2==0 else n_padres-1)
    n_directos = N - n_padres

    generacion = problema_genetico.poblacion_inicial(N)
    
    for t in range(nGen):
        generacion = nueva_poblacion(problema_genetico, generacion, n_padres, n_directos, prob_mutar, k, mejor)
        mejor_cr = mejor(generacion, key = problema_genetico.fitness)
        mejor_fen = problema_genetico.decodifica(mejor_cr)
        print("Generacion: {0}. Media: {1}. Fitness mejor:{2}".format(t+1, media(problema_genetico.fitness, generacion, N),
                                                                      problema_genetico.fitness(mejor_cr)))        
    return (mejor_fen, problema_genetico.fitness(mejor_cr)) 

def media(fitness, generacion, N):
    return sum(map(fitness, generacion)) / N
```


```python
# Solución:
# def nueva_poblacion(...):
#     ...
```

#### Ejercicio 5

Experimentar y observar con el algoritmo genético anterior cómo evolucionan las generaciones en el problema del mínimo de la función cuadrado que se ha representado en el Ejercicio 3


```python
algoritmo_genetico(cuad_gen,20,10,0.75,0.1,min,4)
```


    ---------------------------------------------------------------------------
    

    NameError                                 Traceback (most recent call last)
    

    Cell In[3], line 1
    

    ----> 1 algoritmo_genetico_v1(cuad_gen,20,10,0.75,0.1,min,4)
    

    
    

    NameError: name 'cuad_gen' is not defined


## Problema de la mochila

#### Ejercicio 6

Se dan a continuación tres problemas de la mochila de distinta complejidad. 

Se pide aplicar el algoritmo genético anterior a cada uno de los problemas, y comprobar los resultados que se obtienen (se incluye la solución óptima de cada uno de ellos, para que sirva de referencia sobre el rendimiento de nuestro algoritmo genético). 


```python
# Problema de la mochila 1:
# 10 objetos, peso máximo 165
pesos1 = [23,31,29,44,53,38,63,85,89,82]
valores1 = [92,57,49,68,60,43,67,84,87,72]

# Solución óptima= [1,1,1,1,0,1,0,0,0,0], con valor 309
# ---------------------------------------------------------




# Problema de la mochila 2:
# 15 objetos, peso máximo 750

pesos2 = [70,73,77,80,82,87,90,94,98,106,110,113,115,118,120]
valores2 = [135,139,149,150,156,163,173,184,192,201,210,214,221,229,240]

# Solución óptima= [1,0,1,0,1,0,1,1,1,0,0,0,0,1,1] con valor 1458
# ------------------------------------------------------------




# Problema de la mochila 3:
# 24 objetos, peso máximo 6404180

pesos3 = [382745,799601,909247,729069,467902, 44328,
       34610,698150,823460,903959,853665,551830,610856,
       670702,488960,951111,323046,446298,931161, 31385,496951,264724,224916,169684]
valores3 = [825594,1677009,1676628,1523970, 943972,  97426,
       69666,1296457,1679693,1902996,
       1844992,1049289,1252836,1319836, 953277,2067538, 675367,
       853655,1826027, 65731, 901489, 577243, 466257, 369261]

# Solución óptima= [1,1,0,1,1,1,0,0,0,1,1,0,1,0,0,1,0,0,0,0,0,1,1,1] con valoración 13549094
# --------------------------------------------------------------------

```
