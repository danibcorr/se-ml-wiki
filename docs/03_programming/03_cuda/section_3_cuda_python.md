---
authors: Daniel Bazo Correa
description:
    Aceleración de aplicaciones Python en GPU con Numba y CuPy, desde la compilación JIT
    hasta los kernels personalizados y la memoria compartida.
title: CUDA en Python
---

Este capítulo describe cómo acelerar código de Python en la GPU. La mayor parte está
dedicada a Numba, con el que se recorren la compilación _just-in-time_, la vectorización
de funciones, la escritura de _kernels_ personalizados, las operaciones atómicas, la
coalescencia de los accesos a memoria y el uso de la memoria compartida. El capítulo
cierra con CuPy, que aborda el mismo problema desde el extremo opuesto, ofreciendo la
interfaz de NumPy ya trasladada a la GPU. Los conceptos de arquitectura y de indexación
que se dan por conocidos se desarrollan en los capítulos de
[fundamentos](section_1_fundamentals.md) y de [CUDA en C](section_2_cuda_c.md).

## Bibliografía

- Numba. (s.f.). _Numba Documentation_. <https://numba.pydata.org/>
- CuPy. (s.f.). _CuPy: NumPy & SciPy for GPU_. <https://cupy.dev/>
- NVIDIA y Universidad de Málaga. (s.f.). _Deep Learning Institute - UMA_.
  <http://nvidiadli.uma.es/index.php/es/certificaciones-nvidia>

## Numba

### Fundamentos

**Numba** es un compilador JIT (_Just-In-Time_) que acelera el cálculo numérico en
Python, tanto en CPU como en GPU. A diferencia de otros enfoques, compila funciones
individuales y no la aplicación completa, de modo que no sustituye al intérprete de
Python.

La aceleración se consigue generando una implementación especializada para el tipo
concreto de los datos recibidos, en lugar de recurrir al tipado dinámico que Python
emplea por defecto. Al ser _just-in-time_, la compilación se produce en la primera
invocación de la función, momento en el que el compilador ya conoce los tipos de los
argumentos. Numba se centra en los tipos numéricos, esto es, en enteros, flotantes y
números complejos. Su mejor rendimiento se obtiene al trabajar con _arrays_ de
**NumPy**, la biblioteca que aporta a Python el _array_ multidimensional homogéneo y las
operaciones vectorizadas sobre él, y que constituye la base numérica de todo el
ecosistema científico del lenguaje.

Presenta también limitaciones, entre las que destaca que no admite Pandas ni buena parte
de los objetos de Python que no son numéricos.

### Funcionamiento interno

Cuando se invoca una función decorada con `@jit` o `@njit`, el compilador de Numba
traduce el código de Python a código máquina para el tipo específico de los datos
recibidos. Numba conserva además la función original en el atributo `.py_func`, lo que
permite invocarla sin compilar para comparar resultados:

```python linenums="1"
import math

from numba import jit

@jit
def hipotenusa(x: float, y: float) -> float:
    """
    Calcula la hipotenusa de un triángulo rectángulo evitando
    desbordamientos.

    Args:
        x: Longitud del primer cateto.
        y: Longitud del segundo cateto.

    Returns:
        La longitud de la hipotenusa.
    """

    x = abs(x)
    y = abs(y)
    menor = min(x, y)
    mayor = max(x, y)

    # El caso (0, 0) se trata por separado para no dividir entre cero
    if mayor == 0.0:
        return 0.0

    razon = menor / mayor

    return mayor * math.sqrt(1 + razon * razon)

# Ejecución de la versión compilada con Numba
hipotenusa(3.0, 4.0)

# Ejecución de la función original de Python
hipotenusa.py_func(3.0, 4.0)
```

El cálculo se organiza dividiendo el cateto menor por el mayor, en lugar de elevar ambos
al cuadrado directamente, para evitar el desbordamiento cuando los valores son grandes.
Esa división exige tratar de forma explícita el caso en el que ambos catetos son cero.

!!! note "Cuándo no compensa recurrir a Numba"

    Si ya existe una implementación optimizada de la operación, habitualmente en NumPy o
    en SciPy, esta suele resultar más rápida que una función compilada con Numba, que
    además introduce la sobrecarga de la compilación inicial. Numba aporta valor sobre
    todo en bucles explícitos y en lógica que no se puede expresar de forma vectorizada.

El proceso interno de compilación se resume en el siguiente diagrama.

<figure markdown="span">
  ![Proceso de compilación de Numba](../../assets/img/docs/cuda/cuda-python-numba-compilation.png)
  <figcaption>Proceso interno de compilación de una función con Numba.</figcaption>
</figure>

Para inspeccionar el resultado de la inferencia de tipos se utiliza el método
`.inspect_types()`, que imprime el código fuente anotado con los tipos deducidos:

```python linenums="1"
hipotenusa.inspect_types()
```

### Decoradores

Numba ofrece varios decoradores para la compilación y optimización de funciones:

| Decorador                       | Descripción                                                                                                                                                           |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `@jit`                          | Compila la función en modo _nopython_. Desde la versión 0.59 de Numba es equivalente a `@njit`.                                                                       |
| `@njit` = `@jit(nopython=True)` | Compila prescindiendo por completo del intérprete de Python, lo que ofrece el mejor rendimiento. Falla de forma explícita si algún tipo u operación no está admitido. |
| `@njit(parallel=True)`          | Compila el código para ejecutarse en varios hilos, y aprovecha la paralelización cuando las operaciones lo permiten.                                                  |
| `@njit(fastmath=True)`          | Habilita optimizaciones matemáticas que relajan la conformidad con IEEE 754, con lo que se gana velocidad a costa de precisión.                                       |

Los decoradores pueden combinarse. Por ejemplo, `@njit(parallel=True, fastmath=True)`
prescinde del intérprete, paraleliza el código y admite una menor precisión numérica
para maximizar la velocidad de ejecución.

!!! note "Desaparición del _object mode_"

    En versiones antiguas de Numba, `@jit` recurría al _object mode_ cuando encontraba
    una construcción que no sabía compilar: mantenía los objetos de Python sin
    especializar y la aceleración era escasa o nula, pero el programa seguía
    funcionando. Ese comportamiento resultaba difícil de diagnosticar, porque el código
    se ejecutaba sin avisar de que no se había optimizado.

    A partir de la versión 0.59 el modo _nopython_ es el único disponible y `@jit` se
    comporta igual que `@njit`, de modo que una construcción no admitida produce un
    `TypingError` en lugar de una degradación silenciosa. En código nuevo conviene
    emplear `@njit`, que hace explícita esa intención.

???+ example "Compilación JIT con `@njit`"

    La función recorre dos vectores en paralelo y calcula la media de los pares en los
    que el valor del primero es mayor o igual que uno y el del segundo menor o igual que
    cinco.

    ```python linenums="1"
    import numpy as np
    from numba import njit

    @njit
    def media_de_pares(
        lista1: np.ndarray, lista2: np.ndarray, num_filas: int
    ) -> list[float]:
        """
        Calcula la media de los pares de valores que cumplen un rango
        determinado.

        Args:
            lista1: Primer vector de valores.
            lista2: Segundo vector de valores.
            num_filas: Número de posiciones que se desean recorrer.

        Returns:
            Las medias de los pares que satisfacen las condiciones
                establecidas.
        """

        resultados = []

        for fila in range(num_filas):
            if (lista1[fila] >= 1) and (lista2[fila] <= 5):
                # La media se calcula de forma explícita, ya que Numba solo
                # admite np.mean sobre arrays y no sobre listas de Python
                resultados.append((lista1[fila] + lista2[fila]) / 2)

        return resultados

    lista1: np.ndarray = np.array([1, 2, 3])
    lista2: np.ndarray = np.array([4, 5, 6])
    resultado: list[float] = media_de_pares(lista1, lista2, len(lista1))

    # Imprime: [2.5, 3.5]
    print(resultado)
    ```

### Vectorización para GPU

El hardware de la GPU está diseñado para el paralelismo de datos, de modo que el
_throughput_ máximo se obtiene cuando se aplica la misma operación a muchos elementos de
forma simultánea. Las funciones universales de NumPy (_ufuncs_) hacen precisamente eso,
aplicar una operación a cada elemento de un _array_, lo que las convierte en candidatas
naturales a ejecutarse en el dispositivo.

???+ example "Función vectorizada para CPU"

    El decorador `@vectorize` sin argumentos genera una _ufunc_ que se ejecuta en la
    CPU.

    ```python linenums="1"
    import numpy as np
    from numba import vectorize

    @vectorize
    def suma_diez(num: int) -> int:
        """
        Suma diez al elemento recibido.

        Args:
            num: Valor sobre el que se aplica la operación.

        Returns:
            El valor incrementado en diez unidades.
        """

        return num + 10

    numeros: np.ndarray = np.arange(10)
    resultado: np.ndarray = suma_diez(numeros)

    # Imprime: [10 11 12 13 14 15 16 17 18 19]
    print(resultado)
    ```

???+ example "Función vectorizada para GPU"

    La misma operación se traslada al dispositivo indicando el destino y la firma de
    tipos.

    ```python linenums="1"
    import numpy as np
    from numba import vectorize

    @vectorize(["int64(int64, int64)"], target="cuda")
    def suma_elementos(x: int, y: int) -> int:
        """
        Suma elemento a elemento los dos valores recibidos.

        Args:
            x: Primer operando.
            y: Segundo operando.

        Returns:
            La suma de ambos operandos.
        """

        return x + y

    a: np.ndarray = np.array([1, 2, 3, 4, 5])
    b: np.ndarray = np.array([10, 20, 30, 40, 50])
    resultado: np.ndarray = suma_elementos(a, b)

    # Imprime: [11 22 33 44 55]
    print(resultado)
    ```

Para la GPU se especifica el destino con `target="cuda"` junto con la firma de tipos de
la función, donde los tipos indicados entre paréntesis corresponden a los argumentos y
el que aparece fuera de ellos al valor de retorno. La firma es obligatoria en este caso
porque la compilación para el dispositivo debe producirse antes de disponer de los
datos.

Internamente Numba compila un _kernel_ CUDA que aplica la operación en paralelo sobre
todos los elementos de entrada, reserva memoria en la GPU para las entradas y la salida,
copia los datos al dispositivo, ejecuta el _kernel_ con las dimensiones adecuadas al
tamaño de las entradas, copia el resultado de vuelta y lo devuelve como un _array_ de
NumPy.

Para obtener un rendimiento adecuado en la GPU conviene tener presentes varias
consideraciones:

- **Tamaño de las entradas**: Los _arrays_ deben ser suficientemente grandes, del orden
  de miles de elementos como mínimo, para que el paralelismo compense la sobrecarga de
  la transferencia.
- **Intensidad aritmética**: El cálculo debe realizar suficiente trabajo por elemento
  para justificar el envío de los datos al dispositivo.
- **Encadenamiento de operaciones**: Ejecutar varias operaciones consecutivas en la GPU
  permite amortizar el coste de la copia de datos entre una y otra.
- **Tipos de datos**: Conviene emplear los tipos más pequeños que resulten suficientes.
  NumPy utiliza 64 bits por defecto, por lo que el parámetro `dtype` o el método
  `ndarray.astype()` permiten reducir la precisión a 32 bits cuando resulta apropiado.

## _Kernels_ personalizados

La jerarquía de ejecución en CUDA agrupa los hilos en bloques, y los bloques en una
malla (_grid_). Para escribir _kernels_ personalizados se utiliza el decorador
`@cuda.jit`, que a diferencia de `@vectorize` no devuelve valores, sino que escribe el
resultado en un argumento de salida:

```python linenums="1"
import numpy as np
from numba import cuda

@cuda.jit
def suma_vectores(x: np.ndarray, y: np.ndarray, salida: np.ndarray) -> None:
    """
    Suma elemento a elemento dos vectores y escribe el resultado en el
    vector de salida.

    Args:
        x: Primer vector de entrada.
        y: Segundo vector de entrada.
        salida: Vector de salida donde se almacena el resultado.
    """

    idx = cuda.grid(1)

    # Guarda de límites: el número de hilos lanzados puede superar el de datos
    if idx < salida.size:
        salida[idx] = x[idx] + y[idx]

n: int = 4096
x: np.ndarray = np.arange(n).astype(np.int32)
y: np.ndarray = np.ones_like(x)

# Copia de los datos al dispositivo y reserva del vector de salida
d_x = cuda.to_device(x)
d_y = cuda.to_device(y)
d_salida = cuda.device_array_like(d_x)

hilos_por_bloque: int = 128
bloques_por_malla: int = 32

suma_vectores[bloques_por_malla, hilos_por_bloque](d_x, d_y, d_salida)
cuda.synchronize()
print(d_salida.copy_to_host())
```

El prefijo `d_` de los nombres es una convención habitual para distinguir los _arrays_
que residen en el dispositivo de los que residen en el _host_. La guarda
`idx < salida.size` es necesaria siempre que el número de hilos lanzados no coincida
exactamente con el número de elementos, y conviene incluirla incluso cuando sí coincide,
para que el _kernel_ siga siendo correcto al cambiar la configuración de ejecución.

El tamaño de bloque sigue las mismas reglas descritas en el capítulo de
[CUDA en C](section_2_cuda_c.md). En cuanto al tamaño de la malla, debe garantizar que
la GPU quede completamente ocupada, y un buen punto de partida consiste en lanzar entre
dos y cuatro veces el número de multiprocesadores del dispositivo.

### _Grid stride loop_

El patrón _grid stride loop_ permite trabajar con conjuntos de datos mayores que el
número total de hilos lanzados, al tiempo que preserva la coalescencia de la memoria
global, que se trata más adelante en este mismo capítulo, ya que en cada paso los hilos
consecutivos de un _warp_ acceden a posiciones contiguas:

```python linenums="1"
import numpy as np
from numba import cuda

@cuda.jit
def suma_vectores_con_salto(
    x: np.ndarray, y: np.ndarray, salida: np.ndarray
) -> None:
    """
    Suma dos vectores recorriendo los datos con el patrón grid stride
    loop.

    Args:
        x: Primer vector de entrada.
        y: Segundo vector de entrada.
        salida: Vector de salida donde se almacena el resultado.
    """

    inicio = cuda.grid(1)
    salto = cuda.gridsize(1)

    for i in range(inicio, x.shape[0], salto):
        salida[i] = x[i] + y[i]
```

Igual que en su equivalente en C, la condición del bucle cumple la función de la guarda
de límites, de modo que el patrón es correcto para cualquier combinación de tamaño de
malla y volumen de datos.

### Funciones de dispositivo

Además de escribir _kernels_ completos, `@cuda.jit` admite el parámetro `device=True`,
que restringe la invocación de la función a código que ya se ejecuta en la GPU. La
función deja entonces de ser un punto de entrada y pasa a ser una pieza reutilizable
desde el interior de un _kernel_ o de una _ufunc_:

```python linenums="1"
import math

from numba import cuda, vectorize

@cuda.jit(device=True)
def polares_a_cartesianas(rho: float, theta: float) -> tuple[float, float]:
    """
    Convierte unas coordenadas polares en coordenadas cartesianas.

    Args:
        rho: Radio de la coordenada polar.
        theta: Ángulo de la coordenada polar en radianes.

    Returns:
        Las componentes x e y de la coordenada cartesiana equivalente.
    """

    x = rho * math.cos(theta)
    y = rho * math.sin(theta)

    return x, y

@vectorize(["float32(float32, float32, float32, float32)"], target="cuda")
def distancia_polar(
    rho1: float, theta1: float, rho2: float, theta2: float
) -> float:
    """
    Calcula la distancia euclídea entre dos puntos expresados en
    coordenadas polares.

    Args:
        rho1: Radio del primer punto.
        theta1: Ángulo del primer punto en radianes.
        rho2: Radio del segundo punto.
        theta2: Ángulo del segundo punto en radianes.

    Returns:
        La distancia euclídea entre ambos puntos.
    """

    x1, y1 = polares_a_cartesianas(rho1, theta1)
    x2, y2 = polares_a_cartesianas(rho2, theta2)

    return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5
```

## Operaciones atómicas y condiciones de carrera

Una **condición de carrera** se produce cuando varios hilos acceden sin sincronización a
la misma posición de memoria y al menos uno de ellos escribe en ella. El caso más
habitual es el ciclo de lectura, modificación y escritura: dos hilos leen el mismo
valor, ambos calculan el incremento sobre ese valor y el segundo sobrescribe el
resultado del primero, con lo que una de las dos actualizaciones se pierde.

Para evitarlo, cada hilo debe escribir en una posición única, no debe emplearse el mismo
_array_ como entrada y como salida, y cuando varios hilos deban actualizar una posición
compartida hay que recurrir a operaciones atómicas, que resuelven la lectura, la
modificación y la escritura como una unidad indivisible.

???+ example "Contador compartido con operación atómica"

    Todos los hilos lanzados incrementan un único contador, por lo que el valor final
    coincide con el número total de hilos.

    ```python linenums="1"
    import numpy as np
    from numba import cuda

    @cuda.jit
    def contador_atomico(contador: np.ndarray) -> None:
        """
        Incrementa de forma atómica un contador compartido por todos
        los hilos.

        Args:
            contador: Vector de un único elemento que almacena el
                contador global.
        """

        cuda.atomic.add(contador, 0, 1)

    contador: np.ndarray = np.zeros(1, dtype=np.int32)
    d_contador = cuda.to_device(contador)

    # Se lanzan 4 bloques de 128 hilos, por lo que el valor esperado es 512
    contador_atomico[4, 128](d_contador)

    resultado: np.ndarray = d_contador.copy_to_host()

    # Imprime: Valor final del contador: 512
    print(f"Valor final del contador: {resultado[0]}")
    ```

## Coalescencia de memoria

La coalescencia de los accesos a memoria es un factor determinante en el rendimiento.
Los bloques de hilos se dividen en _warps_ de 32 hilos, y el subsistema de memoria no
sirve posiciones individuales, sino segmentos completos. Cuanto más contiguas sean las
posiciones que solicitan los hilos de un mismo _warp_, menos segmentos hay que traer y
mayor es el ancho de banda aprovechado.

Las tres figuras siguientes comparan dos formas de calcular el índice global sobre los
mismos datos. En la primera, el índice se obtiene como
`threadIdx.x + blockIdx.x * blockDim.x`, de modo que los hilos consecutivos del _warp_
acceden a posiciones consecutivas y todo el contenido del segmento transferido se
utiliza.

<figure markdown="span">
  ![Los cuatro hilos de un warp acceden a las posiciones 0 a 3, contiguas en memoria](../../assets/img/docs/cuda/cuda-python-numba-coalescing-1.png)
  <figcaption>Con un índice contiguo, los hilos del <em>warp</em> solicitan posiciones consecutivas y se aprovecha todo el segmento transferido.</figcaption>
</figure>

Al quedar todas las posiciones dentro de un mismo segmento, la petición del _warp_ se
resuelve en una única transacción. Este es el caso que se denomina acceso perfectamente
coalescente.

<figure markdown="span">
  ![Las cuatro posiciones solicitadas quedan dentro de un único segmento de memoria](../../assets/img/docs/cuda/cuda-python-numba-coalescing-2.png)
  <figcaption>Las cuatro posiciones caen en un único segmento, por lo que el acceso se resuelve en una sola transacción.</figcaption>
</figure>

La tercera figura muestra el resultado de intercambiar los factores del índice y
calcularlo como `blockIdx.x + blockDim.x * threadIdx.x`. Cada hilo salta entonces a una
posición separada de la anterior, el _warp_ necesita tantos segmentos como hilos, y la
mayor parte de los datos que se transfieren, marcados en rojo, no llegan a utilizarse.

<figure markdown="span">
  ![Los cuatro hilos acceden a las posiciones 0, 4, 8 y 12, y obligan a transferir cuatro segmentos de los que solo se usa un valor cada uno](../../assets/img/docs/cuda/cuda-python-numba-coalescing-3.png)
  <figcaption>Con un índice a saltos, cada hilo cae en un segmento distinto y la mayor parte de los datos transferidos se desperdicia.</figcaption>
</figure>

La diferencia entre ambos casos no está en el resultado, que es idéntico, sino en el
número de transacciones necesarias para obtenerlo. De ahí que el orden en que se asocian
las dimensiones del hilo a los índices de los datos sea una decisión de rendimiento y no
de estilo.

### Trabajo con matrices en 2D

CUDA permite organizar los hilos en mallas bidimensionales, que en Numba se consultan
con `cuda.grid(2)`:

```python linenums="1"
import numpy as np
from numba import cuda

@cuda.jit
def indices_2d(matriz: np.ndarray) -> None:
    """
    Escribe en cada posición de la matriz un valor derivado de sus
    índices 2D.

    Args:
        matriz: Matriz de salida sobre la que escribe cada hilo.
    """

    # La dimensión x se asocia a la columna, de forma que los hilos
    # consecutivos de un warp escriban en posiciones contiguas
    col, fila = cuda.grid(2)

    if fila < matriz.shape[0] and col < matriz.shape[1]:
        matriz[fila, col] = fila + col / 10

matriz: np.ndarray = np.zeros((4, 4))
d_matriz = cuda.to_device(matriz)
indices_2d[(2, 2), (2, 2)](d_matriz)
resultado: np.ndarray = d_matriz.copy_to_host()

# Imprime una matriz cuyo elemento (fila, columna) vale fila + columna / 10
print(resultado)
```

!!! warning "Orden de las dimensiones en una malla 2D"

    `cuda.grid(2)` devuelve las coordenadas del hilo en el orden `(x, y)`. Dado que los
    _arrays_ de NumPy se almacenan por filas, asociar la dimensión `x` a la columna es
    lo que produce accesos coalescentes: los hilos consecutivos de un _warp_ difieren en
    `x` y por tanto recorren posiciones contiguas en memoria.

    Invertir esa correspondencia y usar `x` como índice de fila es un error frecuente.
    El resultado sigue siendo correcto, pero cada hilo del _warp_ accede a una fila
    distinta y el acceso se serializa en tantas transacciones como hilos, lo que
    desperdicia la mayor parte del ancho de banda disponible.

## Memoria compartida

La **memoria compartida** reside en un área _on-chip_ del dispositivo. Su tamaño es
limitado, pero ofrece un ancho de banda muy superior al de la memoria global. La
comparten todos los hilos de un mismo bloque y no persiste una vez que el _kernel_
termina.

Los casos de uso habituales incluyen almacenar datos que se leen varias veces, acumular
resultados parciales para realizar una única escritura coalescente y reorganizar datos
antes de escribirlos en memoria global.

En Numba la memoria compartida se reserva con `cuda.shared.array`, cuyo tamaño debe ser
conocido en tiempo de compilación, y la sincronización entre los hilos de un bloque se
realiza con `cuda.syncthreads()`.

???+ example "Inversión de un vector con memoria compartida"

    Cada hilo copia su elemento a la memoria compartida y, tras la sincronización, lee
    la posición simétrica dentro del bloque.

    ```python linenums="1"
    import numpy as np
    from numba import cuda, types

    @cuda.jit
    def invierte_con_memoria_compartida(
        vector: np.ndarray, invertido: np.ndarray
    ) -> None:
        """
        Invierte el orden de un vector de cuatro elementos usando
        memoria compartida.

        Args:
            vector: Vector de entrada con los valores originales.
            invertido: Vector de salida donde se escriben los valores
                invertidos.
        """

        temporal = cuda.shared.array(4, dtype=types.int32)

        # El índice global localiza el dato y el local indexa la memoria
        # compartida, que es privada de cada bloque
        idx = cuda.grid(1)
        tid = cuda.threadIdx.x

        temporal[tid] = vector[idx]

        # Se espera a que todos los hilos del bloque hayan escrito en memoria compartida
        cuda.syncthreads()

        invertido[idx] = temporal[3 - tid]

    vector: np.ndarray = np.arange(4).astype(np.int32)
    invertido: np.ndarray = np.zeros_like(vector)
    d_vector = cuda.to_device(vector)
    d_invertido = cuda.to_device(invertido)
    invierte_con_memoria_compartida[1, 4](d_vector, d_invertido)

    # Imprime: [3 2 1 0]
    print(d_invertido.copy_to_host())
    ```

    La memoria compartida se indexa siempre con el identificador local del hilo dentro
    del bloque, `cuda.threadIdx.x`, y nunca con el índice global que devuelve
    `cuda.grid(1)`. Emplear el índice global funcionaría solo con un único bloque, y
    produciría accesos fuera de los límites en cuanto se lanzase más de uno.

???+ example "Transposición de una matriz con _tiling_"

    El _kernel_ divide la matriz en bloques o _tiles_, técnica conocida como _tiling_, y
    carga cada uno en memoria compartida con accesos coalescentes para escribirlo
    transpuesto también de forma coalescente. Sin ese paso intermedio, una de las dos
    operaciones tendría que recorrer la memoria a saltos.

    ```python linenums="1"
    import numpy as np
    from numba import cuda, types

    @cuda.jit
    def transpone_por_bloques(a: np.ndarray, transpuesta: np.ndarray) -> None:
        """
        Transpone una matriz por bloques apoyándose en memoria
        compartida.

        Args:
            a: Matriz de entrada que se desea transponer.
            transpuesta: Matriz de salida donde se escribe el
                resultado.
        """

        bloque = cuda.shared.array((32, 32), dtype=types.float32)

        a_col = cuda.blockIdx.x * cuda.blockDim.x + cuda.threadIdx.x
        a_fila = cuda.blockIdx.y * cuda.blockDim.y + cuda.threadIdx.y

        # Carga coalescente del bloque de datos en memoria compartida
        bloque[cuda.threadIdx.y, cuda.threadIdx.x] = a[a_fila, a_col]
        cuda.syncthreads()

        t_col = cuda.blockIdx.y * cuda.blockDim.y + cuda.threadIdx.x
        t_fila = cuda.blockIdx.x * cuda.blockDim.x + cuda.threadIdx.y

        # Escritura coalescente leyendo el bloque transpuesto desde memoria compartida
        transpuesta[t_fila, t_col] = bloque[cuda.threadIdx.x, cuda.threadIdx.y]
    ```

    El _kernel_ presupone que las dimensiones de la matriz son múltiplos de 32 y que los
    bloques se lanzan con esa misma forma. Para dimensiones arbitrarias es necesario
    añadir guardas de límites en la carga y en la escritura.

## CuPy

**CuPy** es una biblioteca de Python que acelera el cálculo numérico ejecutándolo en la
GPU. Ofrece una API que reproduce la de NumPy, lo que permite migrar código existente
con muy pocos cambios, en muchos casos limitados a la sentencia de importación.

```python linenums="1"
import cupy as cp
import numpy as np

a: cp.ndarray = cp.array([1, 2, 3, 4, 5])
b: cp.ndarray = cp.array([6, 7, 8, 9, 10])

# La suma se calcula en la GPU y el resultado se transfiere después a la CPU
c: cp.ndarray = a + b
c_numpy: np.ndarray = cp.asnumpy(c)

# Imprime: [ 7  9 11 13 15]
print(c_numpy)
```

Las transferencias entre el _host_ y el dispositivo son explícitas. `cp.asarray` sube un
_array_ de NumPy a la GPU y `cp.asnumpy` baja el resultado, mientras que las operaciones
intermedias permanecen en el dispositivo. Conviene agrupar el mayor número posible de
operaciones antes de bajar los datos, ya que cada transferencia tiene un coste fijo que
puede dominar el tiempo total.

???+ example "Reducciones y álgebra lineal con la interfaz de NumPy"

    Las funciones de reducción y de álgebra lineal replican la firma de sus equivalentes
    en NumPy, de modo que el mismo código sirve para ambas bibliotecas cambiando la
    importación.

    ```python linenums="1"
    import cupy as cp
    import numpy as np

    # Los datos se generan directamente en la GPU
    matriz: cp.ndarray = cp.arange(9, dtype=cp.float32).reshape(3, 3)

    # Reducciones: el resultado permanece en el dispositivo
    print(cp.asnumpy(matriz.sum()))
    print(cp.asnumpy(matriz.mean(axis=0)))

    # Producto de matrices en la GPU
    producto: cp.ndarray = matriz @ matriz.T
    print(cp.asnumpy(producto))

    # Un array de NumPy se sube al dispositivo con cp.asarray
    en_host: np.ndarray = np.ones((3, 3), dtype=np.float32)
    en_dispositivo: cp.ndarray = cp.asarray(en_host)
    print(cp.asnumpy(matriz + en_dispositivo))
    ```

???+ example "_Kernel_ elemento a elemento con `cp.ElementwiseKernel`"

    Cuando una operación no tiene equivalente en NumPy, CuPy permite definirla sin salir
    de Python. El primer argumento declara los parámetros de entrada, el segundo los de
    salida y el tercero el cuerpo, escrito en CUDA C.

    ```python linenums="1"
    import cupy as cp

    diferencia_cuadrados = cp.ElementwiseKernel(
        "float32 x, float32 y",
        "float32 z",
        "z = (x - y) * (x + y)",
        "diferencia_cuadrados",
    )

    a: cp.ndarray = cp.arange(5, dtype=cp.float32)
    b: cp.ndarray = cp.full(5, 2.0, dtype=cp.float32)

    # Imprime: [-4. -3.  0.  5. 12.]
    print(cp.asnumpy(diferencia_cuadrados(a, b)))
    ```

En comparación con Numba, CuPy resulta más adecuado para operar sobre matrices y
realizar cálculos a gran escala, precisamente por esa equivalencia con NumPy, y permite
portar código existente con un esfuerzo mínimo. Numba, por su parte, encaja mejor cuando
hay que acelerar funciones y bucles concretos mediante compilación JIT, tanto en CPU
como en GPU, y cuando se necesita control explícito sobre la organización de los hilos y
sobre el uso de la memoria compartida.
