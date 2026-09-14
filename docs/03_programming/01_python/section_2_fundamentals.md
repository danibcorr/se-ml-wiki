---
authors: Daniel Bazo Correa
description:
    Sintaxis, tipos de datos, estructuras de control, funciones, clases y biblioteca
    estándar de Python.
title: Fundamentos
---

Este capítulo recorre los fundamentos del lenguaje Python. Comienza por los tipos de
datos y las operaciones que los combinan, la entrada y la salida por consola y el
tratamiento de cadenas de texto. Continúa con las estructuras de control y de datos, con
las funciones y con los mecanismos que Python construye sobre ellas, esto es los
decoradores, los generadores y las reglas de alcance de los nombres. La segunda mitad
aborda la programación orientada a objetos, incluidas las _dataclasses_, un recorrido
por los módulos más frecuentes de la biblioteca estándar, el manejo de errores y
excepciones, el trabajo con archivos y directorios, y las expresiones regulares.

## Bibliografía

- Python Software Foundation. (s.f.). _Python 3 Documentation_.
  <https://docs.python.org/3/>
- van Rossum, G., Warsaw, B. y Coghlan, N. (2001). _PEP 8: Style Guide for Python Code_.
  <https://peps.python.org/pep-0008/>
- van Rossum, G., Lehtosalo, J. y Langa, Ł. (2014). _PEP 484: Type Hints_.
  <https://peps.python.org/pep-0484/>
- Portilla, J. (s.f.). _Complete Python Bootcamp_ \[Curso\]. Udemy.
  <https://www.udemy.com/course/complete-python-bootcamp/>

## Introducción

<figure markdown="span">
  ![Logo de Python](../../assets/img/docs/logos/python-logo.png)
  <figcaption>Logo de Python.</figcaption>
</figure>

**Python** es un lenguaje de programación de alto nivel, interpretado y de propósito
general, desarrollado por Guido van Rossum. Su principal ventaja es una sintaxis legible
y concisa, que reduce el esfuerzo de escritura y de mantenimiento del código. A ello se
debe en buena medida su rápida adopción en el sector tecnológico, impulsada además por
el auge de la inteligencia artificial.

Python cuenta con una amplia comunidad de desarrolladores y un ecosistema robusto de
bibliotecas y _frameworks_ que permiten abordar una gran diversidad de proyectos,
incluyendo aplicaciones web, análisis de datos, automatización de tareas y aprendizaje
automático.

### Creación y configuración del entorno

Antes de comenzar a programar en Python es necesario disponer de un entorno de
desarrollo correctamente configurado. El capítulo de
[entornos virtuales](section_1_environments.md) cubre el ecosistema de herramientas
disponible para su gestión y las instrucciones detalladas de configuración. En este
documento se utiliza uv como gestor de entornos, por lo que no es necesario instalar
nada adicional para empezar a trabajar con Python.

### Jupyter Notebooks

Existen dos formas principales de trabajar en Python. La primera es mediante archivos
con extensión `.py`, que funcionan como archivos de texto plano y permiten al entorno de
desarrollo (por ejemplo, Visual Studio Code) ofrecer funcionalidades como autocompletado
y corrección de sintaxis. Esta es la forma de programar más recomendable para proyectos
de producción.

Sin embargo, para explorar el lenguaje y para proyectos de ciencia de datos, se tiende a
utilizar **Jupyter Notebooks**, una herramienta interactiva que integra código, texto y
visualizaciones en un único documento. Entre sus principales ventajas destacan:

- **Interactividad**: Permite ejecutar bloques de código de manera independiente, lo que
  facilita la prueba de ideas y la depuración paso a paso.
- **Documentación integrada**: Admite texto en formato Markdown, permitiendo incluir
  explicaciones y notas directamente junto al código.
- **Visualización**: Facilita la incorporación de gráficos y visualizaciones, mostrando
  los resultados de manera inmediata dentro del mismo documento.

La elección entre una u otra herramienta depende sobre todo de la facilidad para
organizar los proyectos y del enfoque de exploración y desarrollo que se adopte. También
influye la forma de trabajo del equipo. Ambos enfoques son complementarios: el
_notebook_ predomina en la fase exploratoria y el archivo `.py` en el código que llega a
producción.

## Tipos de datos y operaciones

Para dominar Python, es fundamental comprender primero los pilares que sostienen
cualquier programa: cómo se almacena la información, cómo se manipula y cómo se controla
el flujo de las instrucciones. En las siguientes secciones se exploran los elementos
esenciales del lenguaje, desde los tipos de datos básicos y las operaciones matemáticas
hasta las estructuras de control que permiten dotar de lógica al código.

### Tipos de datos

Python ofrece varios tipos de datos fundamentales que permiten definir, almacenar y
manipular información. La siguiente tabla resume los principales tipos de datos y sus
características:

| Tipo de datos                               | Tipo integrado | Ejemplos                         |
| ------------------------------------------- | -------------- | -------------------------------- |
| **Números enteros**                         | `int`          | `3`                              |
| **Números flotantes**                       | `float`        | `2.3`                            |
| **Cadenas de texto**                        | `str`          | `"Hola"`                         |
| **Listas** (colección ordenada y mutable)   | `list`         | `[10, "hello", 200.3]`           |
| **Diccionarios** (pares clave-valor)        | `dict`         | `{"edad": 20, "nombre": "Dani"}` |
| **Tuplas** (secuencia ordenada e inmutable) | `tuple`        | `(10, "hello", 200.3)`           |
| **_Sets_** (colección desordenada y única)  | `set`          | `{"a", "b"}`                     |
| **Booleanos** (valores lógicos)             | `bool`         | `True`, `False`                  |

Los nombres de la segunda columna son **tipos integrados** (_built-ins_), es decir,
funciones y clases que Python pone a disposición del programa sin necesidad de importar
nada. No son palabras reservadas, de modo que el lenguaje permite reasignarlos, aunque
hacerlo es una mala práctica porque oculta el tipo original durante el resto del
programa.

Las **palabras reservadas** (_keywords_) son distintas: forman parte de la gramática del
lenguaje y no pueden emplearse como nombre de variable ni de función bajo ninguna
circunstancia. Entre ellas se encuentran `if`, `else`, `for`, `while`, `def`, `class`,
`return`, `import`, `True`, `False` y `None`. La lista completa se obtiene con
`keyword.kwlist`, del módulo `keyword` de la biblioteca estándar.

Python es un lenguaje de **tipificación dinámica**, por lo que no es necesario declarar
explícitamente el tipo de dato, ya que este se asigna automáticamente según el valor.
Sin embargo, cada vez es más común, y constituye una buena práctica, utilizar lo que se
conoce como _typing_ para anotar los tipos. Por ejemplo:

```python linenums="1"
# Declaración sin anotaciones de tipo
valor_entero = 12

# Declaración con anotaciones de tipo
valor_entero: int = 12
lista_valores: list[int] = [1, 2, 3]
diccionario_valores: dict[str, list[int]] = {"esto_es_un_string": [1, 2, 3]}
```

!!! note

    Para conocer el tipo de una variable se utiliza la función `type(variable)`.

### Operaciones aritméticas

Python permite realizar una amplia variedad de operaciones sobre datos numéricos y otros
tipos. Las principales operaciones matemáticas y funciones disponibles son:

| Operador o función            | Descripción                                                                             |
| ----------------------------- | --------------------------------------------------------------------------------------- |
| `+`, `-`, `*`, `/`, `//`, `%` | Suma, resta, multiplicación, división, división entera y módulo (resto de la división). |
| `-x`                          | Cambia el signo de un número.                                                           |
| `abs(x)`                      | Devuelve el valor absoluto de $x$.                                                      |
| `pow(x, y)` o `x**y`          | Potencia de $x$ elevado a $y$, es decir, $x^y$.                                         |
| `max(x, y)`                   | Devuelve el valor máximo entre $x$ e $y$.                                               |
| `min(x, y)`                   | Devuelve el valor mínimo entre $x$ e $y$.                                               |
| `round(x, n)`                 | Redondea $x$ a $n$ decimales.                                                           |
| `hex(x)`                      | Convierte $x$ a hexadecimal.                                                            |
| `bin(x)`                      | Convierte $x$ a binario.                                                                |

### Operadores de comparación y lógicos

Existen diferentes tipos de operadores en Python. Los **operadores de comparación**
permiten evaluar relaciones entre dos valores y devuelven un resultado booleano (`True`
o `False`):

| Expresión | Descripción               |
| --------- | ------------------------- |
| `A == B`  | A es igual a B.           |
| `A != B`  | A es distinto de B.       |
| `A < B`   | A es menor que B.         |
| `A <= B`  | A es menor o igual que B. |
| `A > B`   | A es mayor que B.         |
| `A >= B`  | A es mayor o igual que B. |

Por otro lado, los **operadores lógicos** permiten combinar varias condiciones y
controlar el flujo de ejecución en función de los resultados:

| Operador | Descripción                                              |
| -------- | -------------------------------------------------------- |
| `and`    | Devuelve `True` si todas las condiciones son verdaderas. |
| `or`     | Devuelve `True` si al menos una condición es verdadera.  |
| `not`    | Invierte el valor lógico de la condición.                |

Los operadores lógicos se utilizan principalmente en estructuras de control, como
condicionales y bucles, para determinar el flujo del programa en función de condiciones
lógicas.

### Variables

Una variable es un nombre que hace referencia a un valor almacenado en memoria. Al
crearlas en Python se deben respetar ciertas reglas:

- Los nombres no pueden comenzar con números.
- No se permiten espacios en los nombres.
- No se pueden utilizar los siguientes símbolos:
  `: ' " < > / , ? | \ ( ) ! @ # $ % ^ & * ~ - +`.
- No se pueden emplear palabras reservadas del lenguaje.

Más allá de estas restricciones, la convención recogida en la guía de estilo PEP 8
establece el uso de nombres en minúsculas con palabras separadas por guiones bajos
(_snake case_), por ejemplo `numero_de_alumnos`. Las constantes, por su parte, se
escriben en mayúsculas, como en `TASA_MAXIMA`. Conviene además elegir nombres
descriptivos que expresen el propósito del valor almacenado, ya que esto reduce la
necesidad de comentarios explicativos.

## Entrada y salida de datos

### Salida por pantalla

Para mostrar datos en pantalla se utiliza la función `print()`:

```python linenums="1"
print("Esto es una prueba")
```

Es posible concatenar variables que contienen cadenas de texto o métodos que devuelvan
un valor utilizando el operador `+`, por ejemplo:

```python linenums="1"
nombre: str = "Daniel"
edad: int = 19

print("Yo me llamo " + nombre + " y tengo " + str(edad) + " años.")
```

Este método resulta engorroso y obliga a convertir de forma explícita los valores que no
son cadenas. Desde Python 3.6 se dispone de las **_f-strings_**, cadenas precedidas por
el carácter `f` que permiten insertar variables o expresiones directamente entre llaves
`{}`:

```python linenums="1"
nombre: str = "Daniel"
edad: int = 19

print(f"Yo me llamo {nombre} y tengo {edad} años")
```

Incluso es posible modificar la cantidad específica de decimales para un valor de tipo
`float` utilizando el formato `{valor_float:.precisiónf}`. Por ejemplo, para mostrar el
número $\pi$ con 5 decimales:

```python linenums="1"
import math

pi: float = math.pi
print(f"El número pi con 5 decimales es: {pi:.5f}")
```

### Entrada del usuario

Python permite recibir información del usuario mediante la función `input()`. Esta
función siempre devuelve el valor introducido como una cadena de texto, por lo que es
necesario realizar una conversión de tipo (lo que se denomina como _casting_) si se
requiere un tipo de dato diferente:

```python linenums="1"
nombre: str = input("Introduce tu nombre: ")
edad: str = input("Introduce tu edad: ")

print("\n\t- DATOS DEL USUARIO - \n")
print(f"Nombre: {nombre}")
print(f"Edad: {edad}")
```

Para convertir el resultado de `input()` en un número es necesario aplicar un _casting_
explícito, como en el siguiente ejemplo, donde una entrada de tipo `str` se transforma
en un `float`:

```python linenums="1"
numero: float = float(input("Introduce un número: "))
```

## Cadenas de texto

Una cadena de texto, o _string_, es una secuencia de caracteres que puede contener
letras, números, símbolos o espacios. A continuación se muestra un ejemplo básico de
_string_ junto con el uso del indexado:

```python linenums="1"
frase: str = "Hola buenas"

# Muestra el carácter 'H'
print(f"El primer carácter de mi string es {frase[0]}")

# Muestra el carácter 'b'
print(f"El sexto carácter de mi string es {frase[5]}")
```

El **índice** de un _string_ comienza en `0`, por lo que `frase[0]` hace referencia al
primer carácter (`"H"`), `frase[1]` al segundo y `frase[5]` al sexto (`"b"`), ya que el
espacio en blanco también cuenta como carácter. Los índices negativos cuentan desde el
final hacia el principio, de modo que `frase[-1]` devuelve el último carácter, `'s'`.

Los _strings_ son **inmutables**, lo que significa que no es posible cambiar un carácter
específico en un _string_ ya creado. Intentar modificar directamente un elemento produce
un error:

```python linenums="1"
frase: str = "Hola buenas"

# Intentar cambiar el primer carácter provoca un error de tipo TypeError
frase[0] = "h"
```

Este código genera un error de tipo `TypeError`. Para modificar un _string_, es
necesario crear uno nuevo combinando partes del original:

```python linenums="1"
frase: str = "Hola buenas"

# Crear un nuevo string con la primera letra modificada
nueva_frase: str = "h" + frase[1:]

# Imprime: hola buenas
print(nueva_frase)
```

### Operaciones sobre cadenas de texto

Sobre una cadena pueden aplicarse tanto funciones integradas del lenguaje, que la
reciben como argumento, como **métodos**, que se invocan sobre la propia cadena con la
notación `cadena.metodo()`. La distinción entre ambos se detalla en el apartado de
funciones. La siguiente tabla recoge las operaciones más habituales:

| Operación                                          | Descripción                                                                                                    |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `str(variable_a_convertir_en_string)`              | Convierte una variable en una cadena de texto.                                                                 |
| `variable *= x`                                    | Repite la cadena `variable` `x` veces y reasigna el resultado, siendo `x` un entero.                           |
| `variable[índice:]`                                | Obtiene una subcadena desde el índice hasta el final de la cadena.                                             |
| `variable[::X]`                                    | Obtiene caracteres de la cadena con un paso de `X`, es decir, toma un carácter cada `X` caracteres.            |
| `variable[::-1]`                                   | Invierte la cadena.                                                                                            |
| `variable.lower()`                                 | Convierte toda la cadena a minúsculas.                                                                         |
| `variable.upper()`                                 | Convierte toda la cadena a mayúsculas.                                                                         |
| `variable.isupper()`                               | Devuelve `True` si toda la cadena está en mayúsculas, `False` en caso contrario.                               |
| `variable.upper().isupper()`                       | Convierte la cadena a mayúsculas y devuelve `True` si toda la cadena está en mayúsculas.                       |
| `variable.split()`                                 | Divide la cadena en una lista de subcadenas basadas en espacios. Puede especificarse un delimitador diferente. |
| `len(variable)`                                    | Devuelve el número de caracteres en la cadena.                                                                 |
| `variable.index("a")` o `variable.index("buenas")` | Devuelve el índice de la primera aparición de la subcadena indicada y lanza `ValueError` si no aparece.        |
| `variable.replace("buenas", "me llamo Daniel")`    | Reemplaza una subcadena dentro de la cadena por otra subcadena.                                                |
| `variable.count("x")`                              | Cuenta el número de veces que aparece el carácter especificado.                                                |
| `variable.find("x")`                               | Devuelve el índice de la primera aparición de la subcadena indicada, o `-1` si no aparece.                     |
| `variable.isalnum()`                               | Devuelve `True` si todos los caracteres son alfanuméricos.                                                     |
| `variable.isalpha()`                               | Devuelve `True` si todos los caracteres son alfabéticos.                                                       |
| `variable.islower()`                               | Devuelve `True` si todos los caracteres están en minúsculas.                                                   |
| `variable.isspace()`                               | Devuelve `True` si todos los caracteres son espacios en blanco.                                                |
| `variable.istitle()`                               | Devuelve `True` si cada palabra empieza por mayúscula y el resto de sus caracteres están en minúsculas.        |
| `variable.split("x")`                              | Divide la cadena en partes cuando encuentra el carácter `x`.                                                   |
| `variable.partition("x")`                          | Devuelve una tupla con la parte anterior a la primera aparición de `x`, el propio `x` y la parte posterior.    |
| `variable.strip()`                                 | Elimina los espacios al principio y al final de la cadena.                                                     |

## Estructuras de control

### Condicionales

Las sentencias condicionales de Python (`if`, `elif` y `else`) permiten ejecutar
diferentes bloques de código según se cumplan o no ciertas condiciones. Esto resulta
fundamental para controlar el flujo de un programa y tomar decisiones en función de los
datos evaluados.

El condicional básico es la sentencia `if`, que ejecuta un bloque de código solo si la
condición se cumple:

```python linenums="1"
if condicion:
    # Código a ejecutar si la condición es verdadera
    ...
```

Si la condición no se cumple, se puede usar una instrucción `else` para ejecutar un
bloque alternativo:

```python linenums="1"
if condicion:
    # Código a ejecutar si la condición es verdadera
    ...
else:
    # Código a ejecutar si la condición es falsa
    ...
```

Para manejar múltiples condiciones, se utiliza la instrucción `elif`, que permite
evaluar varias condiciones de forma secuencial. Esto significa que, si la primera
condición se cumple, el resto de condiciones no se evalúan y se descartan directamente.
En el caso de que la primera condición no se cumpla, se evalúa la siguiente:

```python linenums="1"
if primera_condicion:
    # Código a ejecutar si la primera condición es verdadera
    ...
elif segunda_condicion:
    # Código a ejecutar si la segunda condición es verdadera
    ...
else:
    # Código a ejecutar si ninguna de las condiciones anteriores es verdadera
    ...
```

???+ example "Condicional `if`"

    En este ejemplo se utiliza un condicional `if` para verificar si una letra está
    presente en una palabra:

    ```python linenums="1"
    letra: str = "y"
    palabra: str = "Laguna"

    if letra in palabra:
        print(f"La palabra {palabra} contiene la letra {letra}")
    else:
        print(f"La palabra {palabra} no contiene la letra {letra}")
    ```

    Si `letra` se encuentra en el `string` `palabra`, el programa imprime un mensaje
    indicando que la palabra contiene la letra. En caso contrario, se ejecuta el
    bloque `else`.

### Bucle `for`

El bucle `for` es ideal para iterar sobre secuencias como listas o _strings_. Su
sintaxis básica es:

```python linenums="1"
for variable in iterable:
    # Código a ejecutar para cada elemento del iterable
    ...
```

???+ example "Recorrer un rango de números"

    La función `range(n, m, s)` genera una secuencia de números desde `n` hasta
    `m - 1`, con un paso de `s`. Por ejemplo, para mostrar números desde 0 hasta 10
    en pasos de 2:

    ```python linenums="1"
    for numero in range(0, 11, 2):
        print(numero)
    ```

???+ example "Recorrer los caracteres de un _string_"

    Se puede utilizar `range()` y `len()` para iterar sobre los índices de un _string_:

    ```python linenums="1"
    mi_string: str = "Hola caracola"
    for indice in range(len(mi_string)):
        print(mi_string[indice])
    ```

    Alternativamente, se puede iterar directamente sobre los caracteres del _string_:

    ```python linenums="1"
    mi_string: str = "Hola caracola"
    for letra in mi_string:
        print(letra)
    ```

???+ example "Recorrer dos secuencias simultáneamente con `zip()`"

    `zip()` permite recorrer dos secuencias al mismo tiempo, emparejando sus elementos:

    ```python linenums="1"
    cadena1: str = "Hola"
    cadena2: str = "Yadira"

    for item in zip(cadena1, cadena2):
        print(item)
    ```

    La iteración se detiene al agotarse el _string_ más corto, por lo que los dos
    últimos caracteres de `cadena2` quedan fuera del recorrido.

???+ example "Uso de `enumerate()` para obtener índices y valores"

    `enumerate()` permite obtener el índice y el valor de cada elemento en una
    secuencia:

    ```python linenums="1"
    palabra: str = "abcde"

    for idx, letra in enumerate(palabra):
        print(f"Índice {idx}: {letra}")
    ```

### Bucle `while`

El bucle `while` continúa ejecutándose mientras una condición se mantenga verdadera. Su
sintaxis básica es:

```python linenums="1"
while condicion:
    # Código a ejecutar mientras la condición sea verdadera
    ...
```

???+ example "Crear un contador"

    Un bucle `while` puede usarse para incrementar un contador hasta que alcance un
    valor determinado:

    ```python linenums="1"
    contador: int = 0
    while contador < 5:
        print(contador)
        contador += 1
    ```

### Control de flujo con `break`, `continue` y `pass`

La instrucción `break` termina el bucle inmediatamente, incluso si no ha terminado de
recorrer todos los elementos:

```python linenums="1"
mi_string: str = "Daniel"

for letra in mi_string:
    if letra == "a":
        break
    print(letra)
```

En este ejemplo, el bucle se detiene al encontrar la letra `'a'` y no continúa con el
resto de las iteraciones.

Por otra parte, `continue` omite el resto del código en la iteración actual y pasa a la
siguiente:

```python linenums="1"
mi_string: str = "Daniel"

for letra in mi_string:
    if letra == "a":
        continue
    print(letra)
```

Cuando el bucle encuentra la letra `'a'`, omite el `print()` y continúa con la siguiente
letra.

Finalmente, `pass` no realiza ninguna acción y se utiliza como marcador de posición allí
donde la sintaxis exige un bloque pero todavía no hay nada que ejecutar:

```python linenums="1"
for letra in "Python":
    if letra == "h":
        # Sin pass, el bloque vacío provocaría un IndentationError
        pass
    else:
        print(f"Letra actual: {letra}")
```

## Estructuras de datos

En Python, las estructuras de datos son fundamentales para almacenar y manipular datos
de manera eficiente. A continuación se exploran las estructuras de datos más comunes del
lenguaje.

### Listas

Las **listas** en Python son estructuras de datos que permiten almacenar secuencias
ordenadas y mutables de elementos. A diferencia de otros lenguajes, las listas en Python
pueden contener elementos de diferentes tipos. Su tamaño es dinámico, lo que significa
que puede cambiar durante la ejecución del programa. La indexación comienza en `0`, y
los índices negativos permiten acceder a los elementos desde el final de la lista.

Para definir una lista, basta con usar corchetes y separar los elementos por comas:

```python linenums="1"
lista_amigos: list[str] = ["Jorge", "Fran", "Ricardo"]
```

También es posible inicializar una lista vacía:

```python linenums="1"
lista: list[str] = []
```

El acceso a los elementos se realiza mediante el índice:

```python linenums="1"
lista_amigos: list[str] = ["Jorge", "Fran", "Ricardo"]

# Accede al primer elemento
print(f"El primer amigo es {lista_amigos[0]}")

# Accede al último elemento
print(f"Mi amigo del pueblo es {lista_amigos[-1]}")

# Selecciona un rango de elementos
print(lista_amigos[0:2])

# Muestra la lista completa
print(lista_amigos)
```

#### Operaciones sobre listas

| Operación                 | Descripción                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------- |
| `lista[indice] = x`       | Cambia el elemento en el índice especificado por `x`.                                  |
| `lista.extend(x)`         | Agrega los elementos de `x` al final de la lista actual.                               |
| `lista.append(x)`         | Añade un elemento `x` al final de la lista.                                            |
| `lista.insert(indice, x)` | Inserta `x` en el índice especificado.                                                 |
| `lista.remove(x)`         | Elimina la primera aparición de `x` en la lista.                                       |
| `lista.clear()`           | Vacía la lista.                                                                        |
| `lista.pop(indice)`       | Elimina y devuelve el elemento del índice indicado, o el último si se omite el índice. |
| `lista.index(x)`          | Devuelve el índice de la primera aparición de `x`.                                     |
| `lista.count(x)`          | Devuelve el número de veces que `x` aparece en la lista.                               |
| `lista.sort()`            | Ordena la lista en orden ascendente.                                                   |
| `lista.reverse()`         | Invierte el orden de los elementos en la lista.                                        |
| `lista2 = lista1.copy()`  | Crea una copia de `lista1` en `lista2`.                                                |
| `max(lista)`              | Devuelve el valor máximo de la lista.                                                  |
| `min(lista)`              | Devuelve el valor mínimo de la lista.                                                  |
| `del lista[indice]`       | Elimina el elemento situado en `indice`.                                               |

#### Comprensión de listas

Los bucles `for` permiten iterar sobre los elementos de una lista de manera sencilla.
Además, Python ofrece la **comprensión de listas** (_list comprehension_) para crear
listas nuevas a partir de operaciones sobre una secuencia de elementos. Esta técnica
proporciona una sintaxis concisa y expresiva:

```python linenums="1"
# Crear una lista con los caracteres de un string
mi_lista: list[str] = [letra for letra in "Hola"]
print(mi_lista)

# Crear una lista con los cuadrados de una secuencia de números
mi_lista_cuadrados: list[int] = [numero**2 for numero in range(0, 20, 2)]
print(mi_lista_cuadrados)

# Convertir temperaturas de grados Celsius a grados Fahrenheit
celsius: list[float] = [0, 10, 20, 34.5]
fahrenheit: list[float] = [((9 / 5) * temp + 32) for temp in celsius]
print(fahrenheit)

# Crear una lista con los cuadrados de los números pares
mi_lista_pares: list[int] = [
    numero**2 for numero in range(0, 15) if numero % 2 == 0
]
print(mi_lista_pares)
```

#### Listas anidadas y matrices

Las listas en Python pueden contener otras listas, lo que permite representar matrices o
tablas de datos. Este tipo de estructura resulta útil para manejar información en varias
dimensiones, como una imagen, que en realidad es una composición de tres matrices, una
por cada canal rojo, verde y azul (RGB):

```python linenums="1"
matriz: list[list[int]] = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]

# Acceder al elemento en la tercera fila y tercera columna
print(matriz[2][2])
```

En este caso, `matriz[2][2]` devuelve el valor `9`, que es el elemento ubicado en la
tercera fila y tercera columna.

### Tuplas

Las **tuplas** en Python son secuencias ordenadas e **inmutables**, lo que significa
que, a diferencia de las listas, sus elementos no pueden ser modificados después de su
creación. Las tuplas resultan útiles cuando se necesita garantizar que los datos no
cambien a lo largo del programa. Al no admitir modificaciones, su construcción es algo
más rápida y su consumo de memoria menor que el de una lista equivalente, si bien el
acceso y la iteración tienen un coste prácticamente idéntico.

Para definir una tupla se utilizan paréntesis:

```python linenums="1"
coordenadas: tuple[int, int] = (4, 5)

print(f"Coordenada completa {coordenadas}")
print(f"Primera coordenada {coordenadas[0]} y segunda coordenada {coordenadas[1]}")
```

También es posible combinar tuplas con otras estructuras de datos, como listas de
tuplas:

```python linenums="1"
lista_tuplas: list[tuple[int, int]] = [(1, 2), (3, 4), (5, 6)]
print(f"Mi lista de tuplas es {lista_tuplas}")
```

#### Métodos de tuplas

A pesar de ser inmutables, las tuplas disponen de algunos métodos útiles:

| Método           | Descripción                                                    |
| ---------------- | -------------------------------------------------------------- |
| `tupla.count(x)` | Devuelve el número de veces que `x` aparece en la tupla.       |
| `tupla.index(x)` | Devuelve el índice de la primera aparición de `x` en la tupla. |

### _Sets_

Los _sets_ en Python son colecciones **desordenadas** de elementos únicos. A diferencia
de las listas y tuplas, los _sets_ no permiten duplicados, lo que los convierte en una
herramienta útil para eliminar valores repetidos o para realizar operaciones matemáticas
como uniones e intersecciones.

Un _set_ se puede definir enumerando sus elementos entre llaves, como `{"a", "b", "c"}`,
o con la función `set()`. Un par de llaves vacías crea un diccionario, de modo que un
_set_ vacío solo puede construirse con `set()`:

```python linenums="1"
# Inicializar un set vacío
mi_set: set[int] = set()

# Añadir elementos
mi_set.add(1)

# El siguiente elemento no se añade, ya que los valores de un set son únicos
mi_set.add(1)

# Inicializar un set con elementos
mi_nuevo_set: set[str] = {"a", "b", "c"}
```

#### Métodos de _sets_

| Método                     | Descripción                                                                              |
| -------------------------- | ---------------------------------------------------------------------------------------- |
| `s.add(x)`                 | Añade un elemento `x` al _set_.                                                          |
| `s.clear()`                | Elimina todos los elementos del _set_.                                                   |
| `sc = s.copy()`            | Crea una copia del _set_.                                                                |
| `s1.difference(s2)`        | Devuelve los elementos en `s1` que no están en `s2`.                                     |
| `s1.difference_update(s2)` | Elimina los elementos en `s1` que están en `s2`.                                         |
| `s.discard(elem)`          | Elimina el elemento `elem` del _set_ sin causar error si `elem` no está presente.        |
| `s1.intersection(s2)`      | Devuelve los elementos comunes a `s1` y `s2`.                                            |
| `s1.issubset(s2)`          | Verifica si todos los elementos de `s1` están en `s2`.                                   |
| `s1.union(s2)`             | Devuelve la unión de `s1` y `s2`, combinando todos los elementos únicos de ambos _sets_. |

### Diccionarios

Los **diccionarios** en Python son colecciones de datos que almacenan pares de
**clave-valor**. Las claves son únicas y se utilizan para acceder a los valores
correspondientes. Los diccionarios son mutables, por lo que se pueden modificar después
de su creación.

Un diccionario se define utilizando llaves `{}`, donde cada elemento es un par de
clave-valor:

```python linenums="1"
conversion_meses: dict[str, str] = {
    "Ene": "Enero",
    "Feb": "Febrero",
    "Mar": "Marzo"
}

# Acceso a valores
print(conversion_meses["Ene"])
print(conversion_meses.get("Ene"))

# Manejo de claves no encontradas
clave: str = "Daniel"
print(conversion_meses.get(clave, f"La clave {clave} no está en el diccionario"))
```

#### Métodos de diccionarios

| Método                 | Descripción                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `diccionario.items()`  | Devuelve una vista de los pares clave-valor del diccionario. |
| `diccionario.keys()`   | Devuelve una vista de las claves del diccionario.            |
| `diccionario.values()` | Devuelve una vista de los valores del diccionario.           |

???+ example "Diccionarios anidados"

    Es posible crear diccionarios dentro de otros diccionarios para representar
    estructuras más complejas:

    ```python linenums="1"
    inventario: dict[str, dict[str, int]] = {"almacen": {"tornillos": 100}}

    # Acceder al valor asociado a la clave interna
    print(inventario["almacen"]["tornillos"])
    ```

???+ example "Iteración sobre diccionarios"

    Se puede iterar sobre claves, valores o pares clave-valor en un diccionario:

    ```python linenums="1"
    d: dict[str, int] = {"k1": 1, "k2": 2}

    for llave in d.keys():
        print(llave)

    for valor in d.values():
        print(valor)

    for elemento in d.items():
        print(elemento)
    ```

???+ example "Listas de diccionarios"

    Es posible combinar listas y diccionarios para crear estructuras más elaboradas,
    como una lista de clientes y sus animales:

    ```python linenums="1"
    # El tipo de los valores varía según la clave, ya que unos almacenan una cadena
    # con el nombre y otros una lista de nombres de animales
    clientes: list[dict[str, str | list[str]]] = [
        {"nombre": "Lucía", "animales": ["Pakito", "Pakon", "Pakonazo"]},
        {"nombre": "Marcos", "animales": ["Rodolfo"]},
        {"nombre": "Elena"},
    ]

    for cliente in clientes:
        animales = cliente.get("animales", "No tiene animales")
        print(f"{cliente['nombre']} tiene: {animales}")
    ```

## Funciones

En Python, las funciones son herramientas esenciales para la programación modular y la
reutilización del código. Comprender la diferencia entre métodos y funciones y dominar
su uso permite escribir programas más organizados, legibles y fáciles de mantener.

### Métodos

Los métodos son funciones que están asociadas a un objeto específico, como una instancia
de una clase. Actúan sobre el objeto y pueden modificar su estado o realizar alguna
operación sobre él.

Cada tipo de objeto dispone de un conjunto específico de métodos. Por ejemplo, los
métodos para objetos de tipo `str` permiten realizar operaciones como convertir a
mayúsculas, dividir la cadena en palabras o reemplazar subcadenas.

```python linenums="1"
texto: str = "hola mundo"

# Convertir a mayúsculas. Imprime: HOLA MUNDO
print(texto.upper())

# Dividir en palabras. Imprime: ['hola', 'mundo']
print(texto.split())

# Reemplazar una subcadena. Imprime: hola Python
print(texto.replace("mundo", "Python"))
```

???+ note "Obtener una lista de métodos disponibles"

    Para obtener una lista de todos los métodos disponibles para un tipo de objeto, se
    puede usar la función `dir()`:

    ```python linenums="1"
    # Muestra todos los métodos disponibles para objetos de tipo str
    print(dir(str))
    ```

???+ note "Obtener ayuda sobre un método específico"

    Es posible obtener información detallada sobre un método específico utilizando la
    función `help()`:

    ```python linenums="1"
    # Muestra la documentación para el método upper()
    help(str.upper)
    ```

### Definición de funciones

Las funciones son bloques de código reutilizables que realizan una tarea específica y
pueden ser invocadas desde cualquier lugar del programa. A diferencia de los métodos,
las funciones no están vinculadas a ningún tipo de objeto en particular.

Para definir una función se utiliza la palabra clave `def`, seguida del nombre de la
función y paréntesis con los posibles parámetros:

```python linenums="1"
def saludo(nombre: str) -> str:
    """
    Genera un saludo para el nombre indicado.

    Args:
        nombre: Nombre de la persona a la que se saluda.

    Returns:
        El mensaje de saludo construido a partir del nombre.
    """

    return f"¡Hola, {nombre}!"

print(saludo("Mundo"))
```

Las funciones pueden tomar cualquier número de parámetros, y estos pueden tener valores
predeterminados. Si un parámetro tiene un valor predeterminado, es posible omitirlo al
llamar a la función:

```python linenums="1"
def saludo(nombre: str = "Mundo") -> str:
    """
    Genera un saludo para el nombre indicado.

    Args:
        nombre: Nombre de la persona a la que se saluda. Si se omite, se utiliza
            el valor predeterminado "Mundo".

    Returns:
        El mensaje de saludo construido a partir del nombre.
    """

    return f"¡Hola, {nombre}!"

print(saludo())
print(saludo("Python"))
```

En este ejemplo, `nombre` tiene un valor predeterminado de `"Mundo"`. Si se llama a
`saludo()` sin ningún argumento, se utiliza el valor predeterminado. Si se proporciona
un argumento, este reemplaza el valor predeterminado.

???+ example "Función para comprobar una lista"

    Esta función toma una lista de números como entrada y separa los números pares e
    impares en dos _sets_ distintos:

    ```python linenums="1"
    def comprobar_lista(lista: list[int]) -> None:
        """
        Separa los números pares e impares de una lista y muestra el resultado.

        Args:
            lista: Números enteros que se desean clasificar.
        """

        pares: set[int] = set()
        impares: set[int] = set()

        for numero in lista:
            if numero % 2 == 0:
                pares.add(numero)
            else:
                impares.add(numero)

        print(f"Números pares de la lista recibida: {pares}")
        print(f"Números impares de la lista recibida: {impares}")

    comprobar_lista([1, 1, 1, 1, 1, 1, 23, 56, 87, 918, 23, 12, 3, 2, 4, 6, 5])
    ```

???+ example "Función con tuplas"

    Este ejemplo muestra una función que determina el trabajador con más horas
    trabajadas:

    ```python linenums="1"
    def mejor_trabajador(lista: list[tuple[str, int]]) -> tuple[str, int]:
        """
        Determina el trabajador con mayor número de horas trabajadas.

        Args:
            lista: Pares formados por el nombre del empleado y sus horas trabajadas.

        Returns:
            Una tupla con el nombre del mejor trabajador y sus horas trabajadas.
        """

        maximo: int = 0
        mejor: str = ""

        for empleado, horas in lista:
            if horas > maximo:
                maximo = horas
                mejor = empleado

        return (mejor, maximo)

    horas_trabajadores: list[tuple[str, int]] = [
        ("Daniel", 22), ("Kike", 20), ("Ricardo", 25)
    ]
    mejor, maximo = mejor_trabajador(horas_trabajadores)
    print(f"El mejor trabajador es {mejor} que ha trabajado un total de {maximo} horas")
    ```

???+ example "Funciones que llaman a otras funciones"

    En este ejemplo se muestra un juego simple donde las funciones interactúan entre sí.
    Se utiliza la función `shuffle()` del módulo `random`, que reordena una lista de
    manera aleatoria:

    ```python linenums="1"
    from random import shuffle

    # Lista de vasos donde la letra O representa la bolita
    vasos: list[str] = [" ", "O", " "]

    def barajar_lista(mi_lista: list[str]) -> list[str]:
        """
        Reordena aleatoriamente los elementos de una lista.

        Args:
            mi_lista: Lista de vasos que se desea barajar.

        Returns:
            La misma lista con sus elementos reordenados.
        """

        shuffle(mi_lista)
        return mi_lista

    def inicio() -> None:
        """
        Muestra la posición inicial de la bolita antes de barajar los vasos.
        """

        print("La bolita se encuentra en el vaso 2\n")
        print("vaso 1: ")
        print("vaso 2: O")
        print("vaso 3: ")
        print("\nMoviendo la bola por los diferentes vasos...\n")

    def operar() -> None:
        """
        Solicita al usuario el vaso elegido, valida que esté dentro del rango y delega la
        comprobación del acierto en `comprobar()`.
        """

        resultado: int = int(input("¿En qué vaso está la bolita?: "))

        while resultado < 1 or resultado > 3:
            print("Este vaso no existe")
            resultado = int(input("¿En qué vaso está la bolita?: "))

        comprobar(resultado)

    def comprobar(resultado: int) -> None:
        """
        Comprueba si la elección del usuario coincide con la posición de la bolita.

        Args:
            resultado: Número del vaso elegido por el usuario.
        """

        if vasos[resultado - 1] == "O":
            print("\n¡Has acertado!\n")
        else:
            print("\nHas fallado :(\n")

        for i, vaso in enumerate(vasos, start=1):
            print(f"vaso {i}: {vaso}")

    inicio()
    barajar_lista(vasos)
    operar()
    ```

### Argumentos arbitrarios (`*args` y `**kwargs`)

En Python, las construcciones `*args` y `**kwargs` se emplean en la definición de
funciones para que estas acepten un número arbitrario de argumentos. Lo significativo
son los operadores `*` y `**`, ya que `args` y `kwargs` son solo los nombres que la
comunidad ha adoptado por convención. Esta capacidad resulta especialmente útil cuando
no se conoce de antemano cuántos valores recibirá la función.

En el siguiente ejemplo, `a` y `b` son argumentos posicionales:

```python linenums="1"
def porcentaje_de_dos(a: float, b: float) -> float:
    """
    Calcula el cinco por ciento de la suma de dos valores.

    Args:
        a: Primer valor de la suma.
        b: Segundo valor de la suma.

    Returns:
        El cinco por ciento de la suma de ambos valores.
    """

    return sum((a, b)) * 0.05

porcentaje_de_dos(40, 60)
```

Si se desea que la función pueda manejar más de dos números, una opción sería asignar un
valor predeterminado a los parámetros adicionales:

```python linenums="1"
def porcentaje_de_tres(a: float, b: float, c: float = 0) -> float:
    """
    Calcula el cinco por ciento de la suma de hasta tres valores.

    Args:
        a: Primer valor de la suma.
        b: Segundo valor de la suma.
        c: Tercer valor de la suma, opcional.

    Returns:
        El cinco por ciento de la suma de los valores recibidos.
    """

    return sum((a, b, c)) * 0.05
```

`*args` permite configurar la función para aceptar un número arbitrario de argumentos
posicionales. Python toma todos los parámetros que se pasan y los agrupa como una tupla:

```python linenums="1"
def porcentaje_de_varios(*args: float) -> float:
    """
    Calcula el cinco por ciento de la suma de un número arbitrario de valores.

    Args:
        *args: Valores numéricos que se desean sumar.

    Returns:
        El cinco por ciento de la suma de todos los valores recibidos.
    """

    return sum(args) * 0.05
```

De manera similar, `**kwargs` permite manejar un número arbitrario de argumentos de
palabras clave. En lugar de crear una tupla, crea un diccionario:

```python linenums="1"
def mostrar_favoritos(**kwargs: str) -> None:
    """
    Muestra la fruta y la verdura favoritas si se han indicado.

    Args:
        **kwargs: Argumentos de palabra clave, entre los que se admiten las claves
            "fruta" y "verduras".
    """

    if "fruta" in kwargs:
        print(f"Mi fruta favorita es la {kwargs['fruta']}")
    else:
        print("No se encontró la fruta")

    if "verduras" in kwargs:
        print(f"Mi verdura favorita es la {kwargs['verduras']}")
    else:
        print("No se encontró la verdura")

mostrar_favoritos(fruta="manzana", verduras="zanahoria")
```

También es posible combinar ambos en la misma función:

```python linenums="1"
def describir_mascota(*args: int, **kwargs: str) -> None:
    """
    Combina argumentos posicionales y de palabra clave en un único mensaje.

    Args:
        *args: Valores numéricos posicionales.
        **kwargs: Argumentos de palabra clave, entre los que se espera la clave
            "animal".
    """

    print(f"Tengo {args[0]} coneja llamada {kwargs['animal']}")

describir_mascota(1, 2, 3, 4, animal="Misifu")
```

En este caso, `args` es una tupla de los argumentos posicionales y `kwargs` es un
diccionario de los argumentos de palabras clave, lo que proporciona una gran
flexibilidad a la hora de definir funciones.

### Funciones _lambda_, `map` y `filter`

Las **expresiones _lambda_** permiten crear funciones anónimas, es decir, funciones que
no se vinculan a ningún nombre. Su cuerpo debe ser una única expresión, cuyo valor se
devuelve de forma implícita, y pueden tomar varios argumentos. Por esa forma reducida
resultan adecuadas para operaciones simples y concisas, mientras que para lógica más
elaborada es preferible definir una función con `def`.

Una expresión _lambda_ puede definirse e invocarse en el mismo punto:

```python linenums="1"
# Imprime: 9
print((lambda num: pow(num, 2))(3))
```

La guía de estilo PEP 8 recomienda no asignar una expresión _lambda_ a un nombre, ya que
en ese caso resulta preferible declarar la función con `def`. Su verdadera utilidad
aparece cuando se pasan como argumento a otras funciones.

La función `map()` aplica una función a cada elemento de un iterable. No devuelve una
lista, sino un **iterador**, es decir, un objeto que produce sus elementos de uno en uno
a medida que se recorren, sin construirlos todos de antemano. Por eso es necesario
envolverlo en `list()` para materializar los resultados:

```python linenums="1"
mis_nums: list[int] = [1, 2, 3, 4, 5]
resultado: list[int] = list(map(lambda num: pow(num, 2), mis_nums))

# Imprime: [1, 4, 9, 16, 25]
print(resultado)
```

La función `filter()` selecciona los elementos de un iterable que satisfacen una
condición. Igual que `map()`, devuelve un iterador y no una lista:

```python linenums="1"
mis_nums: list[int] = [1, 2, 3, 4, 5]
resultado: list[int] = list(filter(lambda num: num % 2 == 0, mis_nums))

# Imprime: [2, 4]
print(resultado)
```

!!! note "Evaluación perezosa de `map()` y `filter()`"

    En Python 2 ambas funciones devolvían listas. En Python 3 devuelven iteradores, lo
    que evita construir en memoria una colección completa cuando solo se necesita
    recorrerla una vez. La diferencia se aprecia al imprimir el resultado sin
    convertirlo, que muestra algo como `<map object at 0x...>` en lugar de los valores.

    Como consecuencia, un iterador se agota tras recorrerlo. Si el resultado debe
    consultarse más de una vez, hay que materializarlo con `list()`.

Un uso habitual consiste en aplicar `map()` para transformar colecciones de cadenas de
texto:

```python linenums="1"
personas: list[str] = [
    "Dr. Christopher Brooks",
    "Dr. Kevyn Collins-Thompson",
    "Dr. VG Vinod Vydiswaran",
    "Dr. Daniel Romero",
]

resultado: list[str] = list(
    map(lambda persona: f"{persona.split()[0]} {persona.split()[-1]}", personas)
)

# Imprime: ['Dr. Brooks', 'Dr. Collins-Thompson', 'Dr. Vydiswaran', 'Dr. Romero']
print(resultado)
```

## Ejecución de _scripts_ y módulos

Una vez que se sabe definir funciones, el siguiente paso consiste en organizar un
archivo de código para que pueda emplearse tanto como programa ejecutable como en forma
de biblioteca de funciones reutilizables. Python resuelve esta distinción mediante la
variable especial `__name__`, que permite determinar si un archivo se está ejecutando
directamente como un _script_ o si está siendo importado como módulo desde otro archivo.
Comprender este comportamiento resulta útil para estructurar el código de manera que
ciertos bloques se ejecuten únicamente cuando el archivo constituye el punto de entrada
del programa.

Cuando un archivo de Python se ejecuta directamente, el intérprete asigna a la variable
`__name__` el valor `"__main__"`. En cambio, si el archivo se importa como módulo,
`__name__` toma el nombre del archivo sin la extensión `.py`.

???+ example "Uso de `__name__`"

    En este ejemplo se emplean dos archivos, `modulo_a.py` y `modulo_b.py`, donde el
    primero importa al segundo:

    Archivo `modulo_a.py`:

    ```python linenums="1"
    import modulo_b

    print(f"Módulo A, __name__ establecido a: {__name__}")

    if __name__ == "__main__":
        print("Módulo A ejecutado directamente")
    else:
        print("Módulo A importado desde otro módulo")
    ```

    Archivo `modulo_b.py`:

    ```python linenums="1"
    print(f"Módulo B, __name__ establecido a: {__name__}")

    if __name__ == "__main__":
        print("Módulo B ejecutado directamente")
    else:
        print("Módulo B importado desde otro módulo")
    ```

    Al ejecutar `python modulo_a.py`, el resultado es el siguiente:

    ```plaintext linenums="1"
    Módulo B, __name__ establecido a: modulo_b
    Módulo B importado desde otro módulo
    Módulo A, __name__ establecido a: __main__
    Módulo A ejecutado directamente
    ```

    Un módulo se ejecuta de arriba abajo, y una sentencia `import` se resuelve en el
    punto exacto en que aparece. Como en `modulo_a.py` el `import` es la primera
    sentencia, todo el contenido de `modulo_b.py` se ejecuta antes que el resto del
    archivo. Si el `import` estuviese después de los `print`, estos se ejecutarían
    primero.

    En el momento de la importación, la variable `__name__` de `modulo_b` contiene el
    nombre del módulo, mientras que en `modulo_a.py` contiene `"__main__"` porque es el
    archivo invocado directamente.

    Constituye una buena práctica definir una función `main()` que contenga el código
    principal del programa, ya que así se separa la lógica de ejecución de las
    definiciones reutilizables:

    ```python linenums="1"
    import modulo_b

    def main() -> None:
        """
        Ejecuta la lógica principal del programa.
        """

        print("Código principal de modulo_a.py")

    if __name__ == "__main__":
        main()
    ```

    Con esta estructura, el código de `main()` solo se ejecuta cuando el archivo se
    invoca directamente. Si el módulo se importa desde otro archivo, únicamente se
    evalúan las definiciones y las sentencias situadas fuera de la función, lo que
    resulta adecuado para exponer funciones y clases sin provocar efectos secundarios.

## Decoradores

Los **decoradores** en Python permiten modificar el comportamiento de una función sin
alterar su código fuente. Esto resulta útil cuando se desea añadir funcionalidades a una
función existente sin modificar su definición.

Los decoradores tienen múltiples aplicaciones. Por ejemplo, se utilizan en el desarrollo
web con _frameworks_ como Flask para añadir comportamientos a las funciones de ruta,
como requerir autenticación para acceder a ciertas páginas. También se emplean para
crear _loggers_ que registran cuándo se invocan determinadas funciones y con qué
argumentos, lo cual resulta útil para depurar y entender el flujo de ejecución de un
programa.

En Python, las funciones son objetos de primera clase. Esto significa que pueden ser
asignadas a variables, almacenadas en estructuras de datos, pasadas como argumentos a
otras funciones e incluso retornadas como valores de otras funciones.

???+ example "Decorador básico"

    ```python linenums="1"
    from collections.abc import Callable

    def nuevo_decorador(funcion_original: Callable[[], None]) -> Callable[[], None]:
        """
        Envuelve una función para mostrar mensajes antes y después de su ejecución.

        Args:
            funcion_original: Función sin argumentos que se desea decorar.

        Returns:
            La función decorada con el comportamiento añadido.
        """

        def funcion_nueva() -> None:
            """
            Ejecuta la función original añadiendo mensajes alrededor de la llamada.
            """

            print("Antes de la función original")
            funcion_original()
            print("Después de la función original")

        return funcion_nueva

    @nuevo_decorador
    def funcion_necesita_decorador() -> None:
        """
        Muestra un mensaje por pantalla.
        """

        print("Necesita un nuevo decorador")

    funcion_necesita_decorador()
    ```

    En este ejemplo, `nuevo_decorador` imprime un mensaje antes de ejecutar la función
    original y otro después. La sintaxis `@nuevo_decorador` antes de la definición de
    `funcion_necesita_decorador` es lo que aplica el decorador a la función.

## Generadores

Los **generadores** en Python son una forma eficiente de crear iteradores. A diferencia
de las funciones normales, los generadores utilizan la palabra clave `yield` en lugar de
`return`. Esto permite que produzcan valores de uno en uno, y solo cuando se necesitan,
en lugar de calcular todos los valores a la vez y almacenarlos en memoria. Son
especialmente útiles cuando se trabaja con grandes cantidades de datos que no caben en
memoria, ya que producen los elementos de forma perezosa (_lazy evaluation_), lo que
puede mejorar significativamente el rendimiento del programa.

???+ example "Generador con `yield`"

    Una función generadora devuelve un objeto generador que puede ser iterado para
    obtener los valores generados por `yield`:

    ```python linenums="1"
    from collections.abc import Generator

    def funcion_cubo_generador(n: int) -> Generator[int, None, None]:
        """
        Genera los cubos de los números comprendidos entre cero y n - 1.

        Args:
            n: Límite superior, no incluido, de la secuencia generada.

        Yields:
            El cubo de cada número de la secuencia.
        """

        for x in range(n):
            yield pow(x, 3)

    # Imprime: [0, 1, 8, 27, 64, 125, 216, 343, 512, 729]
    print(list(funcion_cubo_generador(10)))
    ```

La función `iter()` convierte un objeto iterable en un iterador, lo que permite utilizar
la función `next()` para acceder a sus elementos uno a uno:

```python linenums="1"
texto: str = "hola"
texto_iterador = iter(texto)

# Imprime: h
print(next(texto_iterador))
```

## Alcance de las variables

En Python, es fundamental comprender cómo se gestionan las variables en función de su
ubicación en el código. Cada nombre de variable pertenece a un ámbito o _scope_, que
determina desde qué partes del programa resulta visible:

```python linenums="1"
x: int = 25

def devolver_valor() -> int:
    """
    Devuelve un valor local que no altera la variable global del mismo nombre.

    Returns:
        El valor asignado dentro del ámbito local de la función.
    """

    x: int = 50
    return x

# Imprime: 25
print(x)

# Imprime: 50
print(devolver_valor())
```

La asignación de `x` dentro de la función `devolver_valor()` crea una variable local
independiente y no altera el valor global de `x`. Esto se debe a la regla de alcance de
Python, que sigue el orden **LEGB**:

- **L (_Local_)**: Nombres asignados dentro de una función (`def` o `lambda`) y que no
  se declaran globales en esa función.
- **E (_Enclosing function locals_)**: Nombres del ámbito local de las funciones
  envolventes (`def` o `lambda`), de la más interior a la más exterior.
- **G (_Global_)**: Nombres asignados en el nivel superior de un archivo de módulo, o
  declarados globales en un `def` dentro del archivo.
- **B (_Built-in_)**: Nombres integrados que Python predefine en el módulo `builtins`,
  como `open`, `range` o `SyntaxError`.

Este es el orden en el que Python busca las variables:

```python linenums="1"
# Variable global
nombre: str = "Esto es un string global"

def prueba() -> None:
    """
    Define una variable de encierro y llama a una función anidada.
    """

    # Variable del ámbito envolvente
    nombre: str = "Daniel"

    def hola() -> None:
        """
        Muestra el nombre visible en el ámbito más cercano.
        """

        # Variable local
        nombre: str = "Carlitos"
        print(f"Hola {nombre}")

    hola()

prueba()
```

En este ejemplo, la función `hola()` muestra primero la variable local `"Carlitos"`. Si
se comenta la asignación local, toma la variable del ámbito envolvente, `"Daniel"`. Y si
también se comenta esa asignación, toma la variable global `"Esto es un string global"`.

Cuando dentro de una función se asigna un valor a un nombre que también existe en el
ámbito global, la asignación afecta únicamente a la variable local. Para modificar la
variable global desde dentro de una función, se puede usar la palabra clave `global`:

```python linenums="1"
x: int = 50

def prueba() -> None:
    """
    Reasigna la variable global x mostrando su valor antes y después del cambio.
    """

    global x
    print(f"Valor de x antes: {x}")
    x = 200
    print(f"Valor de x después: {x}")

prueba()
print(f"Valor de x fuera: {x}")
```

Sin embargo, se recomienda evitar el uso de `global` a menos que sea absolutamente
necesario. Es más seguro devolver un objeto y luego asignarlo a la variable, evitando
así sobrescribir la variable global dentro de una función de forma inadvertida.

## Programación orientada a objetos

La **programación orientada a objetos** es un paradigma que organiza el código en torno
a **objetos** en lugar de funciones y lógica. Estos objetos combinan **datos**
(atributos) y **funciones** (métodos) que actúan sobre dichos datos. Este enfoque
permite la reutilización, la modularidad y la escalabilidad del código.

### Clases y objetos

Una **clase** es un molde o plantilla para crear objetos, que son instancias de la
clase. Los objetos poseen **atributos** (características) y **métodos**
(comportamientos):

```python linenums="1"
class NombreDeClase:
    """
    Plantilla mínima que ilustra la estructura de una clase.
    """

    def __init__(self, parametro1: str, parametro2: int) -> None:
        """
        Inicializa la instancia con los valores recibidos.

        Args:
            parametro1: Valor de tipo cadena asociado a la instancia.
            parametro2: Valor numérico asociado a la instancia.
        """

        self.parametro1 = parametro1
        self.parametro2 = parametro2

    def algun_metodo(self) -> None:
        """
        Muestra un mensaje que confirma la ejecución del método.
        """

        print("Este es un método dentro de la clase")
```

Cuando se define una función dentro de una clase, se denomina método. El método especial
`__init__` es un **constructor** que se ejecuta automáticamente al crear una nueva
instancia de la clase. El primer argumento de cualquier método en una clase es `self`,
que hace referencia a la propia instancia del objeto:

```python linenums="1"
class Coche:
    """
    Representa un coche con sus características principales.
    """

    def __init__(
        self,
        marca: str,
        modelo: str,
        mejorado: bool,
        acceso_coche: list[str],
    ) -> None:
        """
        Inicializa el coche con sus datos identificativos.

        Args:
            marca: Fabricante del vehículo.
            modelo: Modelo concreto del vehículo.
            mejorado: Indica si el vehículo incorpora mejoras.
            acceso_coche: Nombres de las personas autorizadas a conducirlo.
        """

        self.marca = marca
        self.modelo = modelo
        self.mejorado = mejorado
        self.acceso_coche = acceso_coche

mi_coche = Coche("Toyota", "Corolla", True, ["Juan", "Maria"])
print(f"Mi coche es un {mi_coche.marca} {mi_coche.modelo}")
```

Cuando un método devuelve una instancia del mismo tipo que la clase, se puede utilizar
la anotación `Self` del módulo `typing`, disponible desde Python 3.11, para expresarlo
con precisión. `Self` denota el tipo real de la instancia, de modo que la anotación
sigue siendo correcta en las subclases:

```python linenums="1"
from typing import Self

class Usuario:
    """
    Ejemplo de clase cuyos métodos devuelven instancias del propio tipo.
    """

    def duplicar(self) -> Self:
        """
        Crea una instancia nueva del mismo tipo que la instancia actual.

        Returns:
            Una instancia nueva del tipo concreto sobre el que se invoca el método.
        """

        # type(self) devuelve la clase real, que puede ser una subclase de Usuario
        return type(self)()
```

!!! warning "`Self` no admite devolver la clase escrita a mano"

    Escribir `return Usuario()` en un método anotado con `-> Self` es incorrecto, aunque
    el intérprete no lo impida. Un comprobador de tipos como mypy lo señala con el error
    `Incompatible return value type (got "Usuario", expected "Self")`, porque una
    subclase heredaría el método y devolvería un objeto de la clase base en lugar de uno
    de su propio tipo.

    La construcción adecuada es `type(self)()`, que instancia la clase real. Si de
    verdad se desea devolver siempre la clase base, la anotación correcta es
    `-> "Usuario"` y no `-> Self`.

### Atributos y métodos

Algunos atributos son comunes a todas las instancias, y se denominan atributos de clase,
mientras que otros son específicos de cada objeto y se denominan atributos de instancia:

```python linenums="1"
class Perro:
    """
    Representa un perro con sus atributos de instancia y de clase.
    """

    # Atributo de clase, común para todas las instancias
    especie: str = "mamífero"

    def __init__(self, raza: str, nombre: str, edad: int) -> None:
        """
        Inicializa el perro con sus atributos de instancia.

        Args:
            raza: Raza del animal.
            nombre: Nombre del animal.
            edad: Edad del animal en años.
        """

        self.raza = raza
        self.nombre = nombre
        self.edad = edad

    def sonido(self) -> str:
        """
        Devuelve el sonido característico del animal.

        Returns:
            El sonido emitido por el perro.
        """

        return "Woof!"

    def informacion(self) -> None:
        """
        Muestra por pantalla los datos del animal.
        """

        print(
            f"Nombre: {self.nombre}, Raza: {self.raza}, "
            f"Edad: {self.edad}, Especie: {self.especie}"
        )

if __name__ == "__main__":
    mi_perro = Perro("Labrador", "Fido", 3)
    mi_perro.informacion()
```

En este ejemplo, `especie` es un atributo de clase compartido por todas las instancias
de `Perro`, mientras que `raza`, `nombre` y `edad` son atributos únicos para cada
instancia.

### Herencia y polimorfismo

La **herencia** permite crear nuevas clases a partir de clases ya existentes. La nueva
clase (subclase) hereda los atributos y métodos de la clase padre, pero también puede
tener sus propios atributos y métodos o sobrescribir los heredados:

```python linenums="1"
class Animal:
    """
    Clase base que define el comportamiento común de los animales.
    """

    def __init__(self, nombre: str) -> None:
        """
        Inicializa el animal con su nombre.

        Args:
            nombre: Nombre del animal.
        """

        self.nombre = nombre

    def quien_soy(self) -> None:
        """
        Muestra una descripción genérica del animal.
        """

        print("Soy un animal")

    def comer(self) -> None:
        """
        Muestra un mensaje indicando que el animal está comiendo.
        """

        print("Estoy comiendo")

class Perro(Animal):
    """
    Especialización de Animal que redefine su descripción.
    """

    def quien_soy(self) -> None:
        """
        Muestra una descripción específica del perro.
        """

        print(f"Soy un perro llamado {self.nombre}")

mi_perro = Perro("Fido")

# Imprime: Soy un perro llamado Fido
mi_perro.quien_soy()

# Imprime: Estoy comiendo
mi_perro.comer()
```

En este caso, `Perro` hereda de `Animal`, por lo que puede usar el método `comer`.
Además, la subclase `Perro` sobrescribe el método `quien_soy` de la clase `Animal`.

Por otro lado, el **polimorfismo** permite usar el mismo nombre de método en diferentes
clases. Aunque el método tenga el mismo nombre, cada clase puede implementarlo de manera
diferente:

```python linenums="1"
class Perro(Animal):
    """
    Representa un perro capaz de emitir su sonido característico.
    """

    def sonido(self) -> None:
        """
        Muestra el sonido emitido por el perro.
        """

        print(f"El perro {self.nombre} ladra")

class Gato(Animal):
    """
    Representa un gato capaz de emitir su sonido característico.
    """

    def sonido(self) -> None:
        """
        Muestra el sonido emitido por el gato.
        """

        print(f"El gato {self.nombre} maulla")

mi_perro = Perro("Fido")
mi_gato = Gato("Miau")

# Imprime: El perro Fido ladra
mi_perro.sonido()

# Imprime: El gato Miau maulla
mi_gato.sonido()
```

### Clases abstractas

Una **clase abstracta** es aquella que no se espera que se instancie directamente. Solo
sirve como base para otras clases que implementen sus métodos. Para definir clases
abstractas en Python se utiliza el módulo `abc` (_Abstract Base Classes_):

```python linenums="1"
from abc import ABC, abstractmethod

class Animal(ABC):
    """
    Clase abstracta que obliga a definir el sonido de cada animal.
    """

    def __init__(self, nombre: str) -> None:
        """
        Inicializa el animal con su nombre.

        Args:
            nombre: Nombre del animal.
        """

        self.nombre = nombre

    @abstractmethod
    def sonido(self) -> str:
        """
        Devuelve el sonido característico del animal.

        Returns:
            El sonido emitido por el animal.
        """

class Perro(Animal):
    """
    Implementación concreta de Animal para un perro.
    """

    def sonido(self) -> str:
        """
        Devuelve el sonido característico del perro.

        Returns:
            El mensaje con el sonido del perro.
        """

        return f"{self.nombre} hace woof!"

mi_perro = Perro("Fido")

# Imprime: Fido hace woof!
print(mi_perro.sonido())
```

Si no se implementa el método abstracto en la subclase, se genera un error al intentar
instanciarla.

### _Dataclasses_

En Python, cuando se necesita crear una clase cuya función principal es almacenar datos,
el código tiende a volverse repetitivo: es necesario escribir el método `__init__` para
asignar cada atributo, `__repr__` para obtener una representación legible del objeto y
`__eq__` para poder comparar instancias. El decorador `@dataclass`, disponible en el
módulo `dataclasses` desde Python 3.7, genera automáticamente todos estos métodos a
partir de las anotaciones de tipo de los atributos, lo que reduce significativamente el
código _boilerplate_.

Para definir una _dataclass_, basta con decorar la clase con `@dataclass` y declarar los
atributos con sus tipos:

```python linenums="1"
from dataclasses import dataclass

@dataclass
class Producto:
    """
    Representa un producto con su precio y cantidad en existencias.
    """

    nombre: str
    precio: float
    cantidad: int = 0

p1 = Producto("Teclado", 49.99, 10)
p2 = Producto("Teclado", 49.99, 10)

# Imprime: Producto(nombre='Teclado', precio=49.99, cantidad=10)
print(p1)

# Imprime: True, ya que la comparación se realiza por el valor de los atributos
print(p1 == p2)

# Imprime: 49.99
print(p1.precio)
```

En este ejemplo, Python genera automáticamente el constructor, la representación en
texto y la comparación por igualdad. El atributo `cantidad` tiene un valor
predeterminado de `0`, por lo que es opcional al crear una instancia.

El decorador `@dataclass` acepta varios parámetros que modifican el comportamiento de la
clase:

```python linenums="1"
from dataclasses import dataclass

@dataclass(frozen=True, order=True, slots=True)
class Usuario:
    """
    Representa un usuario inmutable y comparable por sus atributos.
    """

    nombre: str
    edad: int

u1 = Usuario("alice", 30)
u2 = Usuario("bob", 25)

# Imprime: True, la comparación sigue el orden de declaración de los atributos
print(u1 < u2)

# La siguiente línea lanzaría FrozenInstanceError, ya que la instancia es inmutable
# u1.nombre = "otro"
```

Cada parámetro cumple una función específica:

- `frozen=True`: Hace que las instancias sean inmutables. Cualquier intento de modificar
  un atributo después de la creación del objeto lanza un `FrozenInstanceError`. Esto
  resulta útil cuando se necesita garantizar la integridad de los datos o utilizar las
  instancias como claves de diccionarios o elementos de _sets_, ya que las hace
  _hashables_.
- `order=True`: Genera automáticamente los métodos de comparación (`__lt__`, `__le__`,
  `__gt__`, `__ge__`), permitiendo ordenar instancias. La comparación se realiza por el
  orden en que se declaran los atributos, de forma similar a como se comparan las
  tuplas.
- `slots=True` (disponible desde Python 3.10): Sustituye el diccionario interno
  `__dict__` por _slots_, lo que reduce el consumo de memoria por instancia y mejora
  ligeramente la velocidad de acceso a los atributos. Como contrapartida, no es posible
  crear atributos que no hayan sido declarados previamente en la clase.

El método `__post_init__` se ejecuta automáticamente después del `__init__` generado por
la _dataclass_. Resulta útil para realizar validaciones o transformaciones sobre los
atributos una vez asignados:

```python linenums="1"
from dataclasses import dataclass

@dataclass(frozen=True)
class Usuario:
    """
    Representa un usuario cuyo nombre se normaliza al crear la instancia.
    """

    nombre: str

    def __post_init__(self) -> None:
        """
        Normaliza el nombre eliminando espacios y pasándolo a minúsculas.
        """

        # En una dataclass frozen se usa object.__setattr__ para modificar atributos
        object.__setattr__(self, "nombre", self.nombre.strip().lower())

u = Usuario("  Alice  ")

# Imprime: alice
print(u.nombre)
```

Cuando la _dataclass_ es inmutable (`frozen=True`), no es posible reasignar atributos
directamente con `self.name = ...` dentro de `__post_init__`, ya que esto lanzaría un
error. En su lugar, se utiliza `object.__setattr__` para sortear la restricción de
inmutabilidad exclusivamente durante la fase de inicialización.

Cuando un atributo necesita un valor predeterminado mutable (como una lista o un
diccionario), no se puede asignar directamente, ya que todas las instancias compartirían
la misma referencia. Para estos casos se utiliza la función `field` con
`default_factory`:

```python linenums="1"
from dataclasses import dataclass, field

@dataclass
class Inventario:
    """
    Representa un inventario con una lista de artículos y metadatos internos.
    """

    nombre: str
    articulos: list[str] = field(default_factory=list)
    metadatos: dict[str, str] = field(default_factory=dict, repr=False)

inv = Inventario("Almacén A")
inv.articulos.append("Tornillo")

# Imprime: Inventario(nombre='Almacén A', articulos=['Tornillo'])
# El atributo metadatos no aparece porque se ha configurado con repr=False
print(inv)
```

El parámetro `repr=False` en `field` permite excluir un atributo de la representación en
texto del objeto, lo cual resulta útil para atributos internos o de gran tamaño que no
aportan claridad al inspeccionar la instancia.

Las _dataclasses_ admiten herencia de forma natural. La subclase hereda los atributos de
la clase padre y puede añadir los suyos propios. También es posible combinar
_dataclasses_ con clases abstractas del módulo `abc` para definir interfaces que las
subclases deben implementar:

```python linenums="1"
from dataclasses import dataclass
from abc import ABC, abstractmethod

@dataclass
class Vehiculo(ABC):
    """
    Clase base abstracta para los vehículos del catálogo.
    """

    marca: str
    modelo: str

    @abstractmethod
    def tipo(self) -> str:
        """
        Devuelve la categoría comercial del vehículo.

        Returns:
            El nombre de la categoría a la que pertenece el vehículo.
        """

@dataclass
class Coche(Vehiculo):
    """
    Vehículo de tipo turismo con un número configurable de puertas.
    """

    num_puertas: int = 4

    def tipo(self) -> str:
        """
        Devuelve la categoría comercial del coche.

        Returns:
            La cadena que identifica al vehículo como turismo.
        """

        return "Turismo"

c = Coche("Toyota", "Corolla")

# Imprime: Coche(marca='Toyota', modelo='Corolla', num_puertas=4)
print(c)

# Imprime: Turismo
print(c.tipo())
```

## Módulos de la biblioteca estándar

Python dispone de una amplia colección de módulos en su biblioteca estándar que amplían
las capacidades del lenguaje sin necesidad de instalar paquetes externos. A continuación
se presentan algunos de los más utilizados.

### Módulo `collections`

El módulo `collections` proporciona tipos de datos especializados como `Counter`,
`defaultdict` y `namedtuple`, que son alternativas más eficientes a los contenedores
generales de Python (`dict`, `list`, `set` y `tuple`).

#### `Counter`

`Counter` es una subclase de diccionario para contar elementos de manera rápida.
Almacena los elementos como claves y su recuento como valores:

```python linenums="1"
from collections import Counter

lista: list[int | str] = [1, 1, 1, 2, 2, 3, "a", "adios"]
cuenta: Counter = Counter(lista)

# Devuelve los elementos ordenados de mayor a menor número de apariciones
print(cuenta.most_common())
```

#### `defaultdict`

`defaultdict` es una subclase de `dict` que, cuando se accede a una clave inexistente,
la crea con el valor que produce la factoría indicada en lugar de lanzar un `KeyError`:

```python linenums="1"
from collections import defaultdict

d: defaultdict[str, int] = defaultdict(lambda: 0)

# Imprime: 0
print(d["inexistente"])

# Imprime: defaultdict(<function <lambda> at 0x...>, {'inexistente': 0})
print(d)
```

!!! warning "El acceso inserta la clave"

    `defaultdict` no solo devuelve el valor por defecto, sino que además **añade la
    clave al diccionario**. Tras la consulta del ejemplo, `len(d)` vale 1 y la clave
    `"inexistente"` forma parte del diccionario, algo que resulta sorprendente si solo
    se pretendía comprobar un valor.

    Cuando no se desea ese efecto, la alternativa es
    `dict.get(clave, valor_por_defecto)`, que devuelve el valor indicado sin modificar
    el diccionario.

#### `namedtuple`

`namedtuple` es una subclase de tupla que permite acceder a sus elementos por nombre en
lugar de por índice:

```python linenums="1"
from collections import namedtuple

Conejo = namedtuple("Conejo", ["edad", "color", "nombre"])
misifu = Conejo(edad=2, color="Blanco", nombre="Misifu")

# Imprime: 2
print(misifu.edad)
```

### Módulo `datetime`

El módulo `datetime` permite trabajar con fechas y horas en Python. Es posible crear
objetos de fecha, realizar cálculos y extraer información como el año, el mes o el día:

```python linenums="1"
import datetime
from datetime import date

# Crear un objeto de tiempo
mi_tiempo: datetime.time = datetime.time(2, 20)

# Imprime: 20
print(mi_tiempo.minute)

# Imprime: 02:20:00
print(mi_tiempo)

# Obtener la fecha actual
hoy: date = datetime.date.today()
print(hoy)

# Extraer día, mes y año
print(f"Día: {hoy.day}, Mes: {hoy.month}, Año: {hoy.year}")

# Operaciones con fechas
fecha1: date = date(2021, 11, 3)
fecha2: date = date(2020, 11, 2)

# Imprime: 366 days, 0:00:00
print(fecha1 - fecha2)
```

### Módulo `math`

Es posible extender la funcionalidad con módulos de la biblioteca estándar, incluidos
con la propia instalación de Python, o con paquetes de terceros como NumPy o Pandas. Por
ejemplo, el módulo `math` amplía las operaciones disponibles:

| Operador o función | Descripción                                              |
| ------------------ | -------------------------------------------------------- |
| `math.floor(x)`    | Redondea $x$ hacia abajo, es decir, $\lfloor x \rfloor$. |
| `math.ceil(x)`     | Redondea $x$ hacia arriba, es decir, $\lceil x \rceil$.  |
| `math.sqrt(x)`     | Devuelve la raíz cuadrada de $x$, es decir, $\sqrt{x}$.  |
| `math.pi`          | Devuelve el valor de la constante $\pi$.                 |

Para utilizar estas funciones basta con importar el módulo:

```python linenums="1"
import math

math.floor(3.1415)
```

El módulo proporciona además las constantes y las funciones matemáticas más comunes,
entre ellas los logaritmos y las funciones trigonométricas:

```python linenums="1"
import math

# Imprime: 3.141592653589793
print(math.pi)

# Imprime: 2.718281828459045
print(math.e)

# Logaritmo en base 2 de 100. Imprime: 6.643856189774725
print(math.log(100, 2))

# Función específica de base 2, más precisa. Imprime: 6.643856189774724
print(math.log2(100))

# Funciones trigonométricas. Imprime: 1.0
print(math.sin(math.radians(90)))

# Imprime: 90.0
print(math.degrees(math.pi / 2))
```

!!! note "Precisión de `math.log` frente a `math.log2`"

    Los dos logaritmos anteriores difieren en el último dígito. `math.log(100, 2)`
    calcula el cociente de dos logaritmos naturales y arrastra el error de ambas
    operaciones, mientras que `math.log2(100)` emplea la función específica de la
    biblioteca de C y resulta más precisa. Cuando la base es 2 o 10 conviene emplear
    `math.log2` o `math.log10`.

### Precisión decimal con el módulo `decimal`

Cuando se trabaja con valores monetarios o con cálculos que exigen precisión decimal
exacta, es recomendable emplear el tipo `Decimal` del módulo `decimal` en lugar de
`float`, ya que este último introduce errores de redondeo inherentes a la representación
en punto flotante:

```python linenums="1"
from decimal import Decimal

# Imprime: 0.30000000000000004
print(0.1 + 0.2)

# Imprime: 0.3
print(Decimal("0.1") + Decimal("0.2"))
```

El valor de un `Decimal` debe construirse a partir de una cadena y no de un `float`, ya
que `Decimal(0.1)` heredaría el error de representación del literal en punto flotante.

### Módulo `random`

El módulo `random` genera números pseudoaleatorios y ofrece varias funciones para elegir
elementos aleatoriamente o barajar listas:

```python linenums="1"
import random

# La semilla se fija antes de generar valores para que la secuencia sea reproducible
random.seed(101)

# Número aleatorio entre 0 y 100, ambos incluidos
print(random.randint(0, 100))

# Lista de números del 0 al 9
lista: list[int] = list(range(10))
print(lista)

# Elegir un número aleatorio de la lista
print(random.choice(lista))

# Elegir varios números aleatorios, con posibilidad de repetición
print(random.choices(lista, k=5))

# Elegir varios números aleatorios sin repetición
print(random.sample(lista, k=4))

# Barajar la lista de forma aleatoria, modificándola en el sitio
random.shuffle(lista)
print(lista)
```

!!! warning "No apto para usos criptográficos"

    El módulo `random` genera números **pseudoaleatorios** a partir de un estado interno
    determinista, de modo que fijar la semilla reproduce siempre la misma secuencia. Esa
    propiedad es deseable para poder repetir un experimento, pero inaceptable al generar
    contraseñas, _tokens_ de sesión o claves. Para esos casos existe el módulo
    `secrets`, que se apoya en la fuente de entropía del sistema operativo.

### Módulos `time` y `timeit`

Para evaluar la eficiencia del código, es posible medir el tiempo que una función tarda
en ejecutar una acción específica. El módulo `time` de la biblioteca estándar ofrece una
forma sencilla de hacerlo:

```python linenums="1"
import time

def func_uno(n: int) -> list[str]:
    """
    Construye una lista de cadenas mediante comprensión de listas.

    Args:
        n: Número de elementos que se desean generar.

    Returns:
        La lista de cadenas generada.
    """

    return [str(num) for num in range(n)]

def func_dos(n: int) -> list[str]:
    """
    Construye una lista de cadenas mediante la función map.

    Args:
        n: Número de elementos que se desean generar.

    Returns:
        La lista de cadenas generada.
    """

    return list(map(str, range(n)))

# Paso 1: registrar el tiempo de inicio
tiempo_inicio: float = time.time()

# Paso 2: ejecutar el código que se desea cronometrar
resultado: list[str] = func_uno(1000000)

# Paso 3: calcular el tiempo total de ejecución
tiempo_transcurrido: float = time.time() - tiempo_inicio
print(tiempo_transcurrido)
```

Para mediciones más precisas, se puede utilizar el módulo `timeit`, que permite realizar
múltiples repeticiones y obtener resultados estadísticamente más fiables:

```python linenums="1"
import timeit

# El parámetro setup contiene el código de preparación, que no se cronometra
setup: str = """
def func_uno(n):
    return [str(num) for num in range(n)]
"""

# El parámetro stmt contiene la sentencia cuyo tiempo se desea medir
stmt: str = "func_uno(100)"
print(timeit.timeit(stmt, setup, number=100000))

setup2: str = """
def func_dos(n):
    return list(map(str, range(n)))
"""

stmt2: str = "func_dos(100)"
print(timeit.timeit(stmt2, setup2, number=100000))
```

### Funciones mágicas de Jupyter

Los _notebooks_ de Jupyter ofrecen además las **funciones mágicas**, órdenes propias del
entorno que no forman parte del lenguaje ni de su biblioteca estándar. Existen dos
variantes. Las de línea se escriben con un único signo de porcentaje y afectan solo a la
expresión que las sigue, mientras que las de celda se escriben con dos signos y afectan
a la celda completa:

```python linenums="1"
# Función mágica de línea: cronometra únicamente esta expresión
%timeit func_uno(100)
```

```python linenums="1"
# Función mágica de celda: cronometra todo el contenido de la celda
%%timeit
func_uno(100)
func_dos(100)
```

## Manejo de errores y excepciones

### Validación de datos

Cuando se crean funciones que reciben valores de entrada del usuario, es importante
verificar dichas entradas para asegurarse de que son correctas. Este proceso se conoce
como validación de datos.

La función `input()` en Python puede resultar problemática porque espera la interacción
del usuario. Si se ejecuta accidentalmente dos veces, el programa puede quedarse
esperando una respuesta que no llega. En Jupyter, en ese caso, sería necesario reiniciar
el _kernel_, teniendo en cuenta que todas las variables anteriores se borrarán y habrá
que ejecutarlas de nuevo.

Una forma cómoda de validar datos es utilizar bucles `while` para pedir al usuario que
introduzca un valor repetidamente cuando este no es válido:

```python linenums="1"
def limite(eleccion: str) -> bool:
    """
    Comprueba si el valor introducido está dentro del rango permitido.

    Args:
        eleccion: Cadena que representa el número introducido por el usuario.

    Returns:
        True si el número está entre 1 y 10, False en caso contrario.
    """

    return 1 <= int(eleccion) <= 10

def eleccion_usuario() -> int:
    """
    Solicita al usuario un número entre 1 y 10 hasta obtener un valor válido.

    Returns:
        El número validado introducido por el usuario.
    """

    eleccion: str = input("Número de 1 a 10: ")

    while not eleccion.isdigit() or not limite(eleccion):
        eleccion = input("Número de 1 a 10: ")

        if not eleccion.isdigit():
            print("El valor introducido no es un número")

        if eleccion.isdigit() and not limite(eleccion):
            print("El número introducido supera el límite")

    return int(eleccion)

eleccion_usuario()
```

Para limpiar la consola cuando el usuario introduce valores incorrectos en un _notebook_
de Jupyter, se puede importar y usar la función `clear_output()` del módulo
`IPython.display`:

```python linenums="1"
from IPython.display import clear_output
```

Esta función borra la salida de la celda actual, lo que resulta útil para limpiar los
mensajes de error acumulados al validar la entrada. Solo tiene efecto dentro de un
_notebook_ de Jupyter.

### Excepciones (`try`, `except` y `finally`)

El manejo de errores es una estrategia que permite planificar y gestionar posibles
errores que puedan surgir en el código. Por ejemplo, si un usuario intenta escribir en
un archivo que se ha abierto en modo de solo lectura y no existe ninguna declaración de
error en el código, el programa entero se detendrá. Para evitar esto, se utiliza el
manejo de excepciones, que permite continuar con el programa, notificar el error y
seguir con la ejecución.

Las palabras clave del manejo de errores en Python son las siguientes:

- `try`: Bloque de código que se intenta ejecutar (puede producir un error).
- `except`: Bloque de código que se ejecuta en caso de que haya un error en el bloque
  `try`.
- `else`: Bloque que se ejecuta solo si el bloque `try` termina sin lanzar ninguna
  excepción.
- `finally`: Bloque final de código que se ejecuta independientemente de si hubo un
  error o no.
- `raise`: Vuelve a lanzar la excepción capturada, de modo que se propague hacia el
  código que invocó a la función.

```python linenums="1"
try:
    with open("mi_archivo.txt", "w") as f:
        f.write("Línea de prueba")
except TypeError:
    print("Hubo un problema con el tipo de dato")
except OSError:
    print("Hubo un error de entrada y salida")
except Exception:
    print("Hubo un fallo en otro tipo de excepciones")
finally:
    print("De todos modos, la ejecución continúa")
```

En este otro ejemplo, se pide constantemente un dato al usuario hasta que introduzca un
valor adecuado:

```python linenums="1"
def introducir_entero() -> None:
    """
    Solicita un número entero al usuario hasta que la conversión sea correcta.
    """

    while True:
        try:
            valor: int = int(input("Introduce un número entero: "))
        except ValueError:
            print("El valor introducido no es un número")
        else:
            print(f"El valor {valor} es un valor correcto")
            break

introducir_entero()
```

A partir de Python 3.11, es posible añadir notas adicionales a las excepciones
capturadas mediante el método `add_note`, lo que facilita la depuración al proporcionar
contexto extra sobre el error:

```python linenums="1"
try:
    ...
except Exception as e:
    e.add_note("Contexto adicional sobre el error")
    raise
```

### Depuración

El depurador o _debugger_ se emplea para identificar y corregir errores en el código. En
lugar de utilizar `print()` para inspeccionar el estado del programa, se puede usar el
depurador interactivo de Python, `pdb`:

```python linenums="1"
x: list[int] = [1, 2, 3]
z: int = 2
y: int = 1



# La función breakpoint(), disponible desde Python 3.7, detiene la ejecución e
# inicia el depurador. La forma clásica equivalente es pdb.set_trace()
breakpoint()

# La siguiente línea provoca un TypeError, ya que no es posible sumar int y list
resultado: int = y + x
```

## Trabajo con archivos y directorios

### Lectura y escritura

Es posible abrir un archivo usando la función `open()`:

```python linenums="1"
archivo = open("mi_archivo.txt")
```

El segundo argumento de `open()` indica el modo de apertura, que determina si el archivo
se abre para leer, para escribir o para ambas cosas:

| Modo | Descripción                                                                                                                  |
| ---- | ---------------------------------------------------------------------------------------------------------------------------- |
| `r`  | Solo lectura.                                                                                                                |
| `w`  | Solo escritura, reescribe los archivos existentes o crea uno nuevo.                                                          |
| `a`  | Para añadir información al final del archivo.                                                                                |
| `r+` | Lectura y escritura.                                                                                                         |
| `w+` | Escritura y lectura, reescribe los archivos existentes o crea uno nuevo.                                                     |
| `wb` | Escritura en binario. El sufijo `b` se combina con cualquier modo y hace que se trabaje con `bytes` en lugar de con cadenas. |

Para leer un archivo se pueden utilizar los siguientes métodos:

| Método        | Descripción                                                            |
| ------------- | ---------------------------------------------------------------------- |
| `readable()`  | Devuelve un booleano para saber si se puede leer o no el archivo.      |
| `read()`      | Devuelve todo el contenido restante del archivo como una única cadena. |
| `readline()`  | Devuelve la siguiente línea a partir de la posición actual del cursor. |
| `readlines()` | Devuelve todas las líneas restantes del archivo en una lista.          |

Todos estos métodos operan a partir de la posición del cursor, que avanza con cada
lectura. Así, `readline()` devuelve la primera línea la primera vez que se invoca, la
segunda en la siguiente llamada y una cadena vacía cuando se alcanza el final del
archivo.

```python linenums="1"
nombre_arch: str = input("Nombre del archivo: ")

archivo = open(nombre_arch, "r")

if archivo.readable():
    lista: list[str] = archivo.readlines()

    for empleado in lista:
        print(empleado)

# Es recomendable cerrar el archivo después de trabajar con él
archivo.close()
```

Si se lee un archivo completo con `read()`, una segunda lectura no devuelve nada, porque
el cursor ha quedado al final del contenido. Para volver al principio se emplea
`archivo.seek(0)`.

Otra forma de abrir un archivo y operar con él es mediante el gestor de contexto `with`,
que cierra automáticamente el archivo al finalizar el bloque:

```python linenums="1"
with open("mi_archivo.txt", mode="r") as archivo_abierto:
    contenido: str = archivo_abierto.read()

print(contenido)
```

Un ejemplo de cómo añadir información al final de un archivo:

```python linenums="1"
nombre_arch: str = input("Nombre del archivo: ")
nuevo_empleado: str = input("Nombre del nuevo empleado: ")
funcion_empleado: str = input(f"Puesto del empleado {nuevo_empleado}: ")

with open(nombre_arch, "a") as archivo:
    archivo.write(f"\n{nuevo_empleado} - {funcion_empleado}")
```

### Rutas de archivos con `pathlib`

El módulo `pathlib` proporciona una interfaz orientada a objetos para trabajar con rutas
del sistema de archivos. Su uso resulta más limpio que la manipulación de cadenas de
texto y es multiplataforma, lo que garantiza la compatibilidad entre sistemas
operativos:

```python linenums="1"
from pathlib import Path

# El operador / compone rutas con el separador propio del sistema
ruta: Path = Path("datos") / "archivo.csv"

# Imprime: datos/archivo.csv
print(ruta)

# Componentes de la ruta: nombre completo, nombre sin extensión, extensión y directorio
print(ruta.name, ruta.stem, ruta.suffix, ruta.parent)

# Comprobaciones sobre el sistema de archivos
print(ruta.exists(), ruta.is_file())

# Creación del directorio padre, sin error si ya existe
ruta.parent.mkdir(parents=True, exist_ok=True)
```

Frente a la manipulación de cadenas, `pathlib` evita construir separadores a mano y
concentra en un único objeto tanto la descomposición de la ruta como las operaciones
sobre el sistema de archivos.

### Gestión de directorios

En Python se utilizan varios módulos para la apertura, lectura y manipulación de
archivos y directorios en el sistema operativo. Los módulos principales son `shutil` y
`os`, que permiten realizar operaciones como navegar por los directorios, mover y
eliminar archivos, entre otras. En el siguiente ejemplo se emplea además el paquete
externo `send2trash`, que envía los archivos a la papelera del sistema en lugar de
borrarlos de forma definitiva y que debe instalarse previamente en el entorno virtual:

```python linenums="1"
import os
import shutil

import send2trash

# Creación de un archivo de prueba
with open("Prueba.txt", "w+") as f:
    f.write("Esto es una prueba de escritura en un archivo")

# Obtención del directorio de trabajo actual
print(os.getcwd())

# Listado de los elementos en el directorio de trabajo
print(os.listdir())

# Listado de los elementos en un directorio específico
print(os.listdir("/home/usuario/"))

# Movimiento de archivos entre directorios
shutil.move("Prueba.txt", "/home/usuario/")

# Eliminación segura de archivos, enviándolos a la papelera del sistema
send2trash.send2trash("/home/usuario/Prueba.txt")
```

Python también permite recorrer un directorio completo, con todos sus subdirectorios y
archivos:

```python linenums="1"
import os

directorio: str = "/home/usuario/Escritorio"

for actual, subdirectorios, archivos in os.walk(directorio):
    print(f"Directorio actual: {actual}")
    print("Subdirectorios:")

    for subdirectorio in subdirectorios:
        print(f"\t{subdirectorio}")

    print("Archivos:")

    for archivo in archivos:
        print(f"\t{archivo}")
```

### Manipulación de archivos CSV y JSON

Los archivos CSV (_Comma Separated Values_) son un formato utilizado por Excel y otros
programas de bases de datos. Son útiles para la manipulación de datos, aunque solo
contienen el contenido en crudo, sin imágenes, macros ni formato visual.

En Python se trabaja con el módulo `csv` incluido en la biblioteca estándar. Otras
bibliotecas a considerar para la manipulación de datos son Pandas, Openpyxl o la
interfaz de programación de aplicaciones (_Application Programming Interface_, API) de
Google Sheets para Python.

```python linenums="1"
import csv

# Apertura del archivo y lectura de su contenido como lista de filas
with open("example.csv", encoding="utf-8") as datos:
    lineas_datos: list[list[str]] = list(csv.reader(datos))

correos: list[str] = []

# La primera fila contiene los encabezados, por lo que se omite
for linea in lineas_datos[1:]:
    if linea[3] not in correos:
        correos.append(linea[3])

for numero, correo in enumerate(correos):
    print(f"{numero} : {correo}")
```

Para escribir en un archivo CSV se emplea `csv.writer`, que permite añadir filas
individuales con `writerow` o varias a la vez con `writerows`:

```python linenums="1"
import csv

# El parámetro delimiter indica el carácter que separa una columna de otra
with open("archivo_prueba.csv", mode="w", newline="") as archivo_salida:
    csv_escribir = csv.writer(archivo_salida, delimiter=",")
    csv_escribir.writerow(["a", "b", "c"])
    csv_escribir.writerows([["1", "2", "3"], ["4", "5", "6"]])

# El modo "a" añade la información al final del archivo existente
with open("archivo_prueba.csv", mode="a", newline="") as archivo_salida:
    csv_escribir = csv.writer(archivo_salida)
    csv_escribir.writerow(["Nombre", "Apellido", "Correo"])
    csv_escribir.writerows(
        [
            ["Daniel", "BC", "correo1@example.com"],
            ["Clara", "RA", "correo2@example.com"],
        ]
    )
```

Para trabajar con archivos JSON (_JavaScript Object Notation_) se utiliza el módulo
`json` de la biblioteca estándar:

```python linenums="1"
import json

json_string: str = '{"Nombre":"Antonio", "Apellidos":"Adrian"}'
obj: dict[str, str] = json.loads(json_string)

print(f"Nombre: {obj['Nombre']} \nApellidos: {obj['Apellidos']}")
```

También es posible obtener datos en formato JSON desde una dirección web mediante el
paquete externo `requests`, que debe instalarse previamente en el entorno virtual:

```python linenums="1"
import requests

r = requests.get("https://api.example.com/data", timeout=10)
print(r.json())
```

### Compresión de archivos

El módulo `zipfile` permite crear, leer y extraer archivos comprimidos en formato ZIP
directamente desde Python:

```python linenums="1"
import zipfile

# Creación de archivos de prueba
with open("nuevo_archivo.txt", "w+") as f:
    f.write("Esto es solo un ejemplo de introducción de texto")

with open("nuevo_archivo2.txt", "w+") as f:
    f.write("Un poquito más de texto")

# Creación del archivo comprimido y adición de los archivos
with zipfile.ZipFile("comprimido_1.zip", "w") as archivo_comprimido:
    archivo_comprimido.write("nuevo_archivo.txt", compress_type=zipfile.ZIP_DEFLATED)
    archivo_comprimido.write("nuevo_archivo2.txt", compress_type=zipfile.ZIP_DEFLATED)

# Extracción del contenido del archivo comprimido
with zipfile.ZipFile("comprimido_1.zip", "r") as zip_obj:
    zip_obj.extractall("contenido_extraido")
```

## Expresiones regulares

Las **expresiones regulares** son secuencias de caracteres que definen patrones de
búsqueda dentro de cadenas de texto. En Python, el módulo `re` proporciona todas las
herramientas necesarias para trabajar con ellas de forma eficiente, permitiendo buscar,
extraer, reemplazar y validar texto según patrones específicos.

### Búsqueda de patrones

El módulo `re` ofrece tres funciones para localizar texto. `search()` encuentra la
primera coincidencia de un patrón, `findall()` devuelve todas las coincidencias como
lista y `finditer()` devuelve un iterador con objetos que contienen información
detallada sobre cada coincidencia:

```python linenums="1"
import re

texto: str = "El número del agente es 111-111-1111"
patron: str = "número"

# Localiza la primera coincidencia y devuelve un objeto con su posición
busqueda = re.search(patron, texto)

# Muestra el índice de inicio de la palabra
print(busqueda.start())

# Muestra el índice de finalización de la palabra
print(busqueda.end())

# Para encontrar todas las coincidencias se utiliza findall
texto2: str = "Mi número favorito es el número 8"
busqueda2: list[str] = re.findall("número", texto2)
print(busqueda2)

# Para conocer los índices de cada aparición se utiliza finditer
print("La palabra 'número' está en los siguientes índices:")

for palabra in re.finditer("número", texto2):
    print(f"\t{palabra.span()}")

# El método group() devuelve el texto coincidente
print("\nLa palabra 'número' está en los siguientes índices:")

for palabra in re.finditer("número", texto2):
    print(f"\t{palabra.group()} -> {palabra.span()}")
```

### Sintaxis de patrones

Los patrones en expresiones regulares utilizan secuencias especiales para representar
tipos de caracteres. Por ejemplo, `\d` representa cualquier dígito. Es importante
utilizar el prefijo `r` (_raw string_) para evitar que Python interprete las barras
invertidas como secuencias de escape:

```python linenums="1"
import re

texto: str = "Mi número de teléfono es 11 11 11 111"

# El prefijo r indica a Python que la cadena es un patrón en crudo
numero = re.search(r"\d{2} \d{2} \d{2} \d{3}", texto)
print(numero.group())

# Los paréntesis delimitan grupos, que se tratan en detalle más adelante
numero_grupos = re.compile(r"(\d{2}) (\d{2}) (\d{2}) (\d{3})")
resultado = re.search(numero_grupos, texto)

# Acceso a un grupo específico del resultado
print(resultado.group(4))
```

Las expresiones regulares también permiten buscar alternativas mediante el operador `|`
(o lógico), así como definir patrones basados en la posición o el contexto de los
caracteres:

```python linenums="1"
import re

texto: str = "Tengo una coneja que se llama Misifu"

busq1 = re.search(r"coneja|perro", texto)
print(busq1.group())

texto2: str = "Tengo un perro que se llama Tom"

busq2 = re.search(r"coneja|perro", texto2)
print(busq2.group())

texto_gatos: str = "The cat in the hat sat there"

# El punto representa cualquier carácter, de modo que el patrón localiza
# secuencias de tres caracteres terminadas en 'at'
terminadas_at: list[str] = re.findall(r".at", texto_gatos)
print(terminadas_at)

# Exclusión de caracteres específicos, en este caso los dígitos
frase_con_numeros: str = "there are 3 numbers 34 inside 5 this sentence."
print(re.findall(r"[^\d]+", frase_con_numeros))

# Eliminación de los signos de puntuación
frase_con_puntuacion: str = "This is a string! But it has punctuation. How can we remove it?"
limpia: str = " ".join(re.findall(r"[^!.? ]+", frase_con_puntuacion))
print(limpia)

# Búsqueda de palabras que comparten un prefijo común
texto_gato: str = "Hello, would you like some catfish?"
texto_siesta: str = "Hello, would you like to take a catnap?"
print(re.search(r"cat(fish|nap|claw)", texto_gato).group())
print(re.search(r"cat(fish|nap|claw)", texto_siesta).group())
```

### Clases de caracteres y cuantificadores

Las clases de caracteres permiten definir conjuntos de caracteres que se desea buscar.
Se delimitan con corchetes `[]` y admiten rangos, negaciones y combinaciones:

```python linenums="1"
import re

notas: str = "ACAAAABCBCBAA"

# Buscar todas las calificaciones B
print(re.findall("B", notas))

# Buscar calificaciones A o B
print(re.findall("[AB]", notas))

# Buscar combinaciones AB o AC
print(re.findall("[A][B-C]", notas))

# Expresión equivalente utilizando el operador de alternancia
print(re.findall("AB|AC", notas))

# Negar un conjunto para obtener todo lo que no sea A
print(re.findall("[^A]", notas))

# El símbolo ^ fuera de los corchetes ancla la búsqueda al inicio de la cadena, por
# lo que el resultado es una lista vacía al comenzar la cadena por A
print(re.findall("^[^A]", notas))
```

Los cuantificadores especifican cuántas veces debe aparecer un patrón para considerarse
una coincidencia. La sintaxis básica es `e{m,n}`, donde `e` es la expresión, `m` el
mínimo de repeticiones y `n` el máximo:

```python linenums="1"
import re

notas: str = "ACAAAABCBCBAA"

# Secuencias de entre 2 y 10 letras A consecutivas
print(re.findall("A{2,10}", notas))

# Pares de letras A consecutivas
print(re.findall("A{1,1}A{1,1}", notas))

# Con un único valor, el cuantificador exige exactamente ese número de repeticiones
print(re.findall("A{2}", notas))
```

Es importante tener en cuenta que no se deben incluir espacios dentro de las llaves del
cuantificador, ya que `"A{2, 2}"` devuelve un resultado vacío.

El metacarácter `\w` representa cualquier carácter alfanumérico o el guion bajo, es
decir, el conjunto `[a-zA-Z0-9_]`, ampliado por defecto a las letras y los dígitos de
Unicode. El asterisco `*` indica cero o más repeticiones. El siguiente ejemplo extrae
los encabezados de un artículo de Wikipedia en el que cada encabezado va seguido de
`[edit]`:

```python linenums="1"
import re

with open("datasets/ferpa.txt", "r") as archivo:
    wiki: str = archivo.read()

# Buscar palabras seguidas de [edit] indicando un límite de caracteres
print(re.findall(r"[\w]{1,100}\[edit\]", wiki))

# Expresión equivalente sin límite superior mediante el asterisco
print(re.findall(r"[\w]*\[edit\]", wiki))

# Extraer solo los títulos, descartando la etiqueta [edit]
for titulo in re.findall(r"[\w ]*\[edit\]", wiki):
    print(re.split(r"\[", titulo)[0])
```

### Grupos

Los grupos permiten hacer coincidir diferentes patrones simultáneamente y referirse a
ellos de forma independiente. Se definen con paréntesis:

```python linenums="1"
import re

# La variable wiki contiene el texto leído en el ejemplo anterior

# Agrupar el título y la etiqueta [edit] por separado
print(re.findall(r"([\w ]*)(\[edit\])", wiki))

# Iterar sobre los resultados con finditer
for item in re.finditer(r"([\w ]*)(\[edit\])", wiki):
    print(item.groups())

# Acceder a un grupo específico, teniendo en cuenta que el grupo 0 es la
# coincidencia completa
for item in re.finditer(r"([\w ]*)(\[edit\])", wiki):
    print(item.group(1))
```

Los grupos pueden etiquetarse con nombres mediante la sintaxis `(?P<nombre>...)`, lo que
permite acceder a los resultados como un diccionario:

```python linenums="1"
for item in re.finditer(r"(?P<title>[\w ]*)(?P<edit_link>\[edit\])", wiki):
    print(item.groupdict()["title"])
```

### _Look-ahead_ y _look-behind_

Estas construcciones, denominadas conjuntamente **aserciones de anchura cero**, permiten
exigir que un patrón aparezca en una posición determinada sin incluirlo en el texto
capturado. El _look-ahead_ comprueba lo que viene inmediatamente después y se escribe
`(?=...)`, mientras que el _look-behind_ comprueba lo que precede y se escribe
`(?<=...)`. Ambas admiten la forma negativa, `(?!...)` y `(?<!...)`, que exige que el
patrón **no** aparezca en esa posición.

El siguiente ejemplo captura los títulos que van seguidos de `[edit]` sin arrastrar la
etiqueta al resultado:

```python linenums="1"
import re

for item in re.finditer(r"(?P<title>[\w ]+)(?=\[edit\])", wiki):
    print(item.group("title"))
```

El _look-behind_ opera en sentido contrario. En el ejemplo siguiente se extraen los
importes que van precedidos del símbolo del euro, sin incluir el símbolo en la
coincidencia:

```python linenums="1"
import re

texto: str = "El teclado cuesta €49 y el ratón €25, con 15 unidades en stock"

# Solo se capturan los números precedidos por el símbolo del euro
print(re.findall(r"(?<=€)\d+", texto))

# Imprime: ['49', '25']

# La forma negativa captura los números que NO van precedidos por el símbolo
print(re.findall(r"(?<!€)\b\d+", texto))

# Imprime: ['15']
```

!!! note "Longitud fija en el _look-behind_"

    El módulo `re` exige que la expresión de un _look-behind_ tenga una longitud fija,
    por lo que `(?<=€|USD)` no es válido y produce un error de compilación del patrón.
    El _look-ahead_ no tiene esa restricción. Cuando se necesita una anchura variable
    hay que recurrir al paquete externo `regex`, que sí la admite.

También es posible repartir un patrón largo en varias líneas mediante la bandera
`re.VERBOSE`, que ignora los espacios en blanco no escapados y admite comentarios dentro
del propio patrón. Por esa razón, los espacios que forman parte de la búsqueda deben
escribirse como `\ `:

```python linenums="1"
patron: str = r"""
(?P<title>.*)          # Nombre de la universidad
(–\ located\ in\ )     # Indicación de localización
(?P<city>\w*)          # Ciudad
(,\ )                  # Separador
(?P<state>\w*)         # Estado
"""

with open("datasets/universidades.txt", "r") as archivo:
    universidades: str = archivo.read()

for item in re.finditer(patron, universidades, re.VERBOSE):
    print(item.groupdict())
```

Para más información sobre expresiones regulares, se puede consultar la
[documentación oficial](https://docs.python.org/3/library/re.html) y utilizar
herramientas como [regex101](https://regex101.com/) para depurar patrones.

El capítulo de [librerías](section_3_libraries.md) recopila paquetes de terceros que
amplían lo visto aquí, y el de [entornos virtuales](section_1_environments.md) describe
cómo declararlos como dependencia del proyecto.
