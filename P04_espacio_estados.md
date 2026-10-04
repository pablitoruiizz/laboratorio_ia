# Práctica 4 - Inteligencia Artificial

## Tema 2: Búsqueda en Espacio de Estados y Planificación

### Búsqueda en espacio de estados
### 8 puzle

José Luis Ruiz Reina

En esta práctica aplicaremos los algoritmos de búsqueda vistos en clase, viendo cómo se comportan con el problema del 8 puzzle. La práctica tiene tres partes bien diferenciadas:

* __Parte I:__ Representación de problemas de espacios de estados. Veremos una técnica general para hacerlo, y en particular se implementará el problema del ocho puzzle.

* __Parte II:__ Experimentación con los algoritmos implementados. Ejecución de los algoritmos implementados, para la búsqueda de soluciones a instancias concretas de los problemas.

* __Parte III:__ Calcularemos algunos estadísticas sobre la ejecución de los algoritmos para resolución de problemas de ocho puzzle. Así, se comprobarán experimentalmente algunas propiedades de los algoritmos.

El código que se usa en esta práctica está basado principalmente en el código Python que se proporciona con el libro "Artificial Intelligence: A Modern Approach" de S. Russell y P. Norvig (http://code.google.com/p/aima-python, módulo search.py). Las modificaciones al código y la traducción han sido realizadas por José Luis Ruiz Reina (Dpto. de Ciencias de la Computación e Inteligencia Artificial de la Universidad de Sevilla).

##  PARTE I. REPRESENTACIÓN DE ESPACIOS DE ESTADOS

Recuérdese que según lo que se ha visto en clase, la implementación de la representación de un problema de espacio de estados consiste en:

* Representar estados y acciones mediante una estructura de datos.
* Definir: estado_inicial, es_estado_final(_), acciones_aplicables(_) y aplicar(_,_).

La siguiente clase Problema representa este esquema general de cualquier problema de espacio de estados. Un problema concreto será una subclase de Problema, y requerirá implementar acciones_aplicables, aplicar, y eventualmente __init__ y es_estado_final. 


```python
class Problema(object):
    """Clase abstracta para un problema de espacio de estados. Los problemas
    concretos habría que definirlos como subclases de Problema, implementando
    acciones_aplicables, aplicar, y eventualmente __init__ y es_estado_final.
    Una vez hecho esto, se han de crear instancias de
    dicha subclase, que serán la entrada a los distintos algoritmos de
    resolución mediante búsqueda."""  

    def __init__(self, estado_inicial, estado_final=None):
        """El constructor de la clase especifica el estado inicial y
        puede que un estado_final, si es que es único. Las subclases podrían
        añadir otros argumentos"""
        
        self.estado_inicial = estado_inicial
        self.estado_final = estado_final

    def acciones_aplicables(self, estado):
        """Devuelve las acciones aplicables a un estado dado. Lo normal es
        que aquí se devuelva una lista, pero si hay muchas se podría devolver
        un iterador, ya que sería más eficiente."""
        pass

    def aplicar(self, estado, accion):
        """ Devuelve el estado resultante de aplicar accion a estado. Se
        supone que accion es aplicable a estado (es decir, debe ser una de las
        acciones que devuelve self.acciones_aplicables(estado)."""
        pass

    def es_estado_final(self, estado):
        """Devuelve True cuando estado es final. Por defecto, compara con el
        estado final, si éste se hubiera especificado al constructor. Si se da
        el caso de que no hubiera un único estado final, o se definiera
        mediante otro tipo de comprobación, habría que redefinir este método
        en la subclase.""" 
        return estado == self.estado_final

```

Lo que sigue es un ejemplo de cómo definir un problema como subclase de `Problema`. En concreto, el problema de las jarras, visto en clase:


```python
class Jarras(Problema):
    """Problema de las jarras:
    Representaremos los estados como tuplas (x,y) de dos números enteros,
    donde x es el número de litros de la jarra de 4 e y es el número de litros
    de la jarra de 3"""

    def __init__(self):
        super().__init__({4: 0, 3: 0})

    def acciones_aplicables(self,estado):
        jarra_de_4 = estado[4]
        jarra_de_3 = estado[3]
        accs = list()
        if jarra_de_4 > 0:
            accs.append(("V", 4))
            if jarra_de_3 < 3:
                accs.append(("T", 4))
        if jarra_de_4 < 4:
            accs.append(("L", 4))
            if jarra_de_3 > 0:
                accs.append(("T", 3))
        if jarra_de_3 > 0:
            accs.append(("V", 3))
        if jarra_de_3 < 3:
            accs.append(("L", 3))
        return accs

    def aplicar(self,estado,accion):
        nv = {4: estado[4], 3: estado[3]}
        if accion[0] == "L":
            nv[accion[1]] = accion[1]
        elif accion[0] == "V":
            nv[accion[1]] = 0
        else:
            if accion[1] == 3:
                hueco = 4 - nv[4]
                tras = min(nv[3], hueco)
                nv[4] += tras
                nv[3] -= tras
            else:
                hueco = 3 - nv[3]
                tras = min(nv[4], hueco)
                nv[4] -= tras
                nv[3] += tras
        return nv

    def es_estado_final(self,estado):
        return estado[4] == 2 or estado[3] == 2
```

Veamos algunos ejemplos de cómo se usa


```python
pj = Jarras()
```


```python
pj.estado_inicial
# Resultado: {4: 0, 3: 0}
```




    {4: 0, 3: 0}




```python
pj.acciones_aplicables(pj.estado_inicial)
# Resultado: [('L', 4), ('L', 3)]
```




    [('L', 4), ('L', 3)]




```python
pj.aplicar(pj.estado_inicial,('L', 4))
# Resultado: {4: 4, 3: 0}
```




    {4: 4, 3: 0}




```python
pj.es_estado_final(pj.estado_inicial)
# Resultado:False
```




    False



### Ejercicio 1

Definir la clase Ocho_Puzzle, que implementa una representación del problema del 8-puzzle. Para ello, completar el código que se presenta a continuación, en los lugares marcados con interrogantes.


```python
# class Ocho_Puzzle(Problema):
#     """Problema del 8-puzzle. Los estados serán tuplas de nueve elementos,
#     permutaciones de los números del 0 al 8 (el 0 es el hueco). Representan la
#     disposición de las fichas en el tablero, leídas por filas de arriba a
#     abajo, y dentro de cada fila, de izquierda a derecha. Por ejemplo, el
#     estado final será la tupla (1, 2, 3, 8, 0, 4, 7, 6, 5). Las cuatro
#     acciones del problema las representaremos mediante las cadenas:
#     "Mover hueco arriba", "Mover hueco abajo", "Mover hueco izquierda" y
#     "Mover hueco derecha", respectivamente. 
#     """
# 
#     def __init__(self,tablero_inicial):
#         super().__init__(estado_inicial=?????, estado_final=?????)
# 
#     def acciones_aplicables(self,estado):
#         pos_hueco = estado.index(0)
#         accs = list()
#         if pos_hueco not in ?????: 
#             accs.append(?????)
#         if pos_hueco not in ?????: 
#             accs.append(?????)
#         if pos_hueco not in ?????: 
#             accs.append(?????)
#         if pos_hueco not in ?????: 
#             accs.append(?????)
#         return accs     
# 
#     def aplicar(self,estado,accion):
#         ???????
```

Ejemplos que se pueden ejecutar una vez se ha definido la clase:


```python
p8p_1 = Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))
p8p_1.estado_inicial
# Resultado: (2, 8, 3, 1, 6, 4, 7, 0, 5)
```




    (2, 8, 3, 1, 6, 4, 7, 0, 5)




```python
p8p_1.estado_final
# Resultado: (1, 2, 3, 8, 0, 4, 7, 6, 5)
```




    (1, 2, 3, 8, 0, 4, 7, 6, 5)




```python
p8p_1.acciones_aplicables(p8p_1.estado_inicial)
# Resultado: ['Mover hueco arriba', 'Mover hueco izquierda', 'Mover hueco derecha']
```




    ['Mover hueco arriba', 'Mover hueco izquierda', 'Mover hueco derecha']




```python
p8p_1.aplicar(p8p_1.estado_inicial, "Mover hueco arriba")
# Resultado: (2, 8, 3, 1, 0, 4, 7, 6, 5)
```




    (2, 8, 3, 1, 0, 4, 7, 6, 5)



##  PARTE I. EXPERIMENTANDO

Los algoritmos de búsquedas están implementados el el fichero *algoritmos_de_búsqueda.py*. Importamos las funcionesdefinidas en el módulo.


```python
from algoritmos_de_búsqueda import *
```

### Ejercicio 2

Usar búsqueda en anchura y en profundidad para encontrar soluciones tanto al problema de las jarras como al problema del ocho puzzle con distintos estados iniciales. Puedes probar los siguientes ejemplos.


```python
búsqueda_en_anchura(Jarras()).solucion()
# Resultado:
# [('L', 3), ('T', 3), ('L', 3), ('T', 3)]
```




    [('L', 3), ('T', 3), ('L', 3), ('T', 3)]




```python
búsqueda_en_profundidad(Jarras()).solucion()
# Resultado:
# [('L', 3), ('T', 3), ('L', 3), ('T', 3)]
```




    [('L', 3), ('T', 3), ('L', 3), ('T', 3)]




```python
búsqueda_en_anchura(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))).solucion()
# Resultado:
# ['Mover hueco arriba', 'Mover hueco arriba', 'Mover hueco izquierda', 
#  'Mover hueco abajo', 'Mover hueco derecha']
```




    ['Mover hueco arriba',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha']




```python
búsqueda_en_profundidad(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))).solucion()
# Resultado:
# ['Mover hueco derecha', 'Mover hueco arriba', ... ] # ¡más de 3000 acciones!
```




    ['Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     ...]



### Ejercicio 3

Definir las dos funciones heurísticas para el 8 puzzle que se han visto en las diapositivas. Es decir:
* h1_ocho_puzzle(estado): cuenta el número de casillas mal colocadas respecto del estado final.
* h2_ocho_puzzle_estado(estado): suma la distancia Manhattan desde cada casilla a la posición en la que debería estar en el estado final. 



```python
# Solución:

# def h1_ocho_puzzle(estado):
#     """Cuenta el número de piezas descolocadas"""
#     ???      

# def h2_ocho_puzzle(estado):
#     """Suma la distancia Manhattan de cada casilla a donde debería estar"""
#     ???
```

Lo probamos


```python
h1_ocho_puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))
# Resulatado: 4
```




    4




```python
h2_ocho_puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))
# Resultado: 5
```




    5




```python
h1_ocho_puzzle((5,2,3,0,4,8,7,6,1))
# Resultado: 4
```




    4




```python
h2_ocho_puzzle((5,2,3,0,4,8,7,6,1))
# Resultado: 11
```




    11



### Ejercicio 4

Resolver usando búsqueda_en_anchura, búsqueda_en_profundidad y búsqueda_primero_el_mejor (con las dos heurísticas), el problema del 8 puzzle parael siguiente estado inicial:


```python
# Estado inicial

#              +---+---+---+
#              | 2 | 8 | 3 |
#              +---+---+---+
#              | 1 | 6 | 4 |
#              +---+---+---+
#              | 7 | H | 5 |
#              +---+---+---+
```


```python
búsqueda_en_anchura(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))).solucion()
# Resultado:
# ['Mover hueco arriba', 'Mover hueco arriba', 'Mover hueco izquierda', 
#  'Mover hueco abajo', 'Mover hueco derecha']
```




    ['Mover hueco arriba',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha']




```python
búsqueda_en_profundidad(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5))).solucion()
```




    ['Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     ...]




```python
búsqueda_primero_el_mejor(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5)),h1_ocho_puzzle).solucion()
# Resultado:
# ['Mover hueco arriba', 'Mover hueco izquierda', 'Mover hueco arriba', 
#  'Mover hueco derecha', 'Mover hueco abajo', 'Mover hueco izquierda', 
#  'Mover hueco arriba', 'Mover hueco derecha', 'Mover hueco abajo']
```




    ['Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo',
     'Mover hueco izquierda',
     'Mover hueco arriba',
     'Mover hueco derecha',
     'Mover hueco abajo']




```python
búsqueda_primero_el_mejor(Ocho_Puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5)),h2_ocho_puzzle).solucion()
# Resultado:
# ['Mover hueco arriba', 'Mover hueco arriba', 'Mover hueco izquierda', 
#  'Mover hueco abajo', 'Mover hueco derecha']
```




    ['Mover hueco arriba',
     'Mover hueco arriba',
     'Mover hueco izquierda',
     'Mover hueco abajo',
     'Mover hueco derecha']



## PARTE III. Estadísticas

La siguientes definiciones nos van a permitir experimentar con distintos estados iniciales, algoritmos y heurísticas, para resolver el 8-puzzle. Además se van a contar el número de nodos analizados durante la búsqueda:


```python
class Problema_con_Analizados(Problema):

    """Es un problema que se comporta exactamente igual que el que recibe al
       inicializarse, y además incorpora un atributos nuevos para almacenar el
       número de nodos analizados durante la búsqueda. De esta manera, no
       tenemos que modificar el código del algorimo de búsqueda.""" 
         
    def __init__(self, problema):
        self.estado_inicial = problema.estado_inicial
        self.problema = problema
        self.analizados  = 0

    def acciones_aplicables(self, estado):
        return self.problema.acciones_aplicables(estado)

    def aplicar(self, estado, accion):
        return self.problema.aplicar(estado, accion)

    def es_estado_final(self, estado):
        self.analizados += 1
        return self.problema.es_estado_final(estado)

def resuelve_ocho_puzzle(estado_inicial, algoritmo, h=None):
    """Función para aplicar un algoritmo de búsqueda dado al problema del ocho
       puzzle, con un estado inicial dado y (cuando el algoritmo lo necesite)
       una heurística dada.
       Ejemplo de uso:

       >>> resuelve_ocho_puzzle((2, 8, 3, 1, 6, 4, 7, 0, 5),búsqueda_a_estrella,h2_ocho_puzzle)
       Solución: ['Mover hueco arriba', 'Mover hueco arriba', 'Mover hueco izquierda', 
                  'Mover hueco abajo', 'Mover hueco derecha']
       Algoritmo: búsqueda_a_estrella
       Heurística: h2_ocho_puzzle
       Longitud de la solución: 5. Nodos analizados: 7
       """

    p8p=Problema_con_Analizados(Ocho_Puzzle(estado_inicial))
    sol= (algoritmo(p8p,h).solucion() if h else algoritmo(p8p).solucion()) 
    print("Solución: {0}".format(sol))
    print("Algoritmo: {0}".format(algoritmo.__name__))
    if h: 
        print("Heurística: {0}".format(h.__name__))
    else:
        pass
    print("Longitud de la solución: {0}. Nodos analizados: {1}".format(len(sol),p8p.analizados))
```

### Ejercicio 5

Intentar resolver usando las distintas búsquedas y en su caso, las distintas heurísticas, el problema del 8 puzzle para los siguientes estados iniciales:


```python
#           E1              E2              E3              E4
#           
#     +---+---+---+   +---+---+---+   +---+---+---+   +---+---+---+    
#     | 2 | 8 | 3 |   | 4 | 8 | 1 |   | 2 | 1 | 6 |   | 5 | 2 | 3 |
#     +---+---+---+   +---+---+---+   +---+---+---+   +---+---+---+
#     | 1 | 6 | 4 |   | 3 | H | 2 |   | 4 | H | 8 |   | H | 4 | 8 |
#     +---+---+---+   +---+---+---+   +---+---+---+   +---+---+---+
#     | 7 | H | 5 |   | 7 | 6 | 5 |   | 7 | 5 | 3 |   | 7 | 6 | 1 |
#     +---+---+---+   +---+---+---+   +---+---+---+   +---+---+---+    
```

Se pide, en cada caso, hacerlo con la función resuelve_ocho_puzzle, para obtener, además de la solución, la longitud de la solución obtenida y el número de nodos analizados. Anotar los resultados en la siguiente tabla (L, longitud de la solución, NA, nodos analizados), y justificarlos con las distintas propiedades teóricas estudiadas.


```python
# -----------------------------------------------------------------------------------------
#                                       E1           E2           E3          E4
                                
# Anchura                             L=            L=           L=          L=  
#                                     NA=           NA=          NA=         NA= 
                                                                              
# Profundidad                         L=            L=           L=          L=  
#                                     NA=           NA=          NA=         NA= 
                                                                              
                                                                              
# Primero el mejor (h1)               L=            L=           L=          L=
#                                     NA=           NA=          NA=         NA=
                                                                              
# Primero el mejor (h2)               L=            L=           L=          L= 
#                                     NA=           NA=          NA=         NA=
                                                                              

# -----------------------------------------------------------------------------------------
```


```python

```
