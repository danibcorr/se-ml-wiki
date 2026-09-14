---
authors: Daniel Bazo Correa
description:
    Automatización de tareas con Make, incluyendo la sintaxis de reglas, las variables,
    las funciones avanzadas y las buenas prácticas de organización.
title: Makefile
---

Este capítulo introduce Make como herramienta de automatización de tareas y describe la
sintaxis de las reglas, el uso de variables, las funciones y directivas avanzadas, y las
buenas prácticas de organización de un proyecto.

## Bibliografía

- Chase, C. (s.f.). _Makefile Tutorial_. <https://makefiletutorial.com/>
- Wolf, A. y Fursov, I. (s.f.). _ML in Production: From Data Scientist to ML Engineer_
  \[Curso\]. Udemy. <https://www.udemy.com/course/ml-in-production/>

## Introducción

<figure markdown="span">
  ![Logo de Make](../../assets/img/docs/logos/makefile-logo.png)
  <figcaption>Logo de Make.</figcaption>
</figure>

Un **_Makefile_** es un archivo de configuración utilizado por la herramienta `make` que
permite automatizar procesos de compilación, ejecución y gestión de proyectos de
software. Se emplea de forma generalizada en sistemas basados en GNU/Linux y puede
contener comandos escritos en [Bash](section_1_bash.md).

Su principal aportación frente a una colección de _scripts_ independientes es que reúne
todas las tareas de un proyecto en un único punto de entrada y establece relaciones de
dependencia entre ellas, de modo que una tarea puede exigir la ejecución previa de
otras.

!!! note "Herramientas que invocan los ejemplos"

    Las recetas de este capítulo llaman a uv como gestor de entornos de Python, y a
    través de él a `pre-commit`, `ruff` y `pytest`. Make se limita a orquestar esas
    órdenes, de modo que los ejemplos se entienden sin conocerlas. Su instalación y su
    configuración en el archivo `pyproject.toml` se describen en el capítulo de
    [entornos virtuales de
    Python](../../03_programming/01_python/section_1_environments.md).

## Sintaxis básica

### Estructura de una regla

Una regla define el proceso de construcción de un objetivo (_target_) a partir de sus
prerrequisitos. La estructura básica es la siguiente:

```makefile linenums="1"
targets: prerequisites
	comando
	comando
	comando
```

El campo _targets_ contiene los nombres de los archivos o tareas que se generarán,
separados por espacios, aunque habitualmente se define un único _target_ por regla. Los
_prerequisites_ son los archivos o dependencias necesarios para generar el _target_,
también separados por espacios. Los comandos son las instrucciones que se ejecutan para
construir el _target_ y cada uno de ellos debe comenzar obligatoriamente con un carácter
de tabulación, nunca con espacios.

???+ example "Regla sin prerrequisitos"

    ```makefile linenums="1"
    setup:
    	@echo "Installing dependencies..."
    	@uv sync --all-groups
    	@uv run pre-commit install
    	@echo "✅ Dependencies installed."
    ```

    En este ejemplo, `setup` es el _target_ que representa la tarea a realizar. Al no
    tener prerrequisitos, se ejecuta siempre que se invoque. Los comandos precedidos por
    `@` se ejecutan sin mostrar la línea del comando en la salida, de modo que
    únicamente aparece su resultado.

    Al ejecutar `make setup`, Make procesa los comandos de forma secuencial y, en este
    caso, sincroniza las dependencias con uv e instala los _hooks_ de `pre-commit`.

???+ example "Regla con prerrequisitos"

    ```makefile linenums="1"
    pipeline: setup clean lint test
    	@echo "✅ Pipeline complete."
    ```

    En este caso, `pipeline` depende de varios _targets_ previos. Make ejecuta cada
    prerrequisito en orden antes de ejecutar el comando propio del _target_.

### Comentarios

Los comentarios se escriben utilizando el símbolo `#`. No afectan a la ejecución del
archivo y sirven para documentar el propósito de las reglas o de los comandos.

???+ example "Comentarios en un _Makefile_"

    ```makefile linenums="1"
    # Esta regla instala las dependencias del proyecto
    setup:
    	@uv sync --all-groups
    ```

### Variables

Las variables permiten almacenar y reutilizar valores, lo que facilita la
personalización de comandos y rutas sin necesidad de repetir información.

???+ example "Definir variables"

    ```makefile linenums="1"
    PATH_TESTS ?= tests
    PATH_PROJECT_ROOT ?= .

    test:
    	@echo "Running tests..."
    	@uv run pytest $(PATH_TESTS) --maxfail=1 --durations=0
    	@echo "✅ Tests complete."
    ```

    La sintaxis `?=` asigna un valor por defecto a la variable, lo que permite
    sobrescribirla en el momento de la invocación. En la regla `test`, el comando
    utiliza `$(PATH_TESTS)` para referenciar el directorio de pruebas, de modo que basta
    con indicar otro valor en la llamada para ejecutar la batería sobre un directorio
    diferente:

    ```bash linenums="1"
    make test PATH_TESTS=./tests/unit
    ```

#### Variables automáticas

Make proporciona variables automáticas que permiten referirse de manera dinámica a los
_targets_ y a los prerrequisitos sin necesidad de escribirlos explícitamente en cada
regla.

| Variable | Descripción                                               |
| -------- | --------------------------------------------------------- |
| `$@`     | Nombre del _target_ actual.                               |
| `$<`     | Primer prerrequisito de la regla.                         |
| `$^`     | Todos los prerrequisitos de la regla.                     |
| `$*`     | Parte del nombre (_stem_) que coincide con el patrón `%`. |
| `$(@D)`  | Directorio del _target_ actual.                           |
| `$(@F)`  | Nombre del archivo del _target_ actual.                   |

???+ example "Variables automáticas"

    ```makefile linenums="1"
    output.zip: processed_data/input1.csv processed_data/input2.csv
    	zip $@ $^
    ```

    En este caso, `$@` representa el _target_ actual (`output.zip`) y `$^` contiene
    todos los prerrequisitos (`processed_data/input1.csv processed_data/input2.csv`).

#### Variables específicas de objetivo y patrones

Make permite definir variables que se aplican únicamente a determinados objetivos o
patrones de archivos, lo que posibilita configuraciones particulares sin afectar al
resto del proyecto.

???+ example "Variables por objetivo"

    ```makefile linenums="1"
    # Se añade una opción de optimización para la generación de archivos intermedios
    %.txt: PYTHONFLAGS += --optimize

    # Regla para procesar archivos .csv y convertirlos en .txt
    %.txt: %.csv
    	python3 process_data.py $(PYTHONFLAGS) $< $@
    ```

    El patrón `%.txt: %.csv` indica que cualquier archivo con extensión `.csv` puede
    convertirse en un archivo `.txt` homónimo. La variable específica `PYTHONFLAGS` se
    amplía con `--optimize` exclusivamente cuando el _target_ construido responde al
    patrón `%.txt`. En la regla, `$<` representa el archivo de entrada y `$@` el de
    salida.

## Funciones avanzadas

### Manipulación de cadenas de texto

Make ofrece un conjunto de funciones integradas que facilitan la manipulación de cadenas
de texto y de listas, lo que resulta especialmente útil para transformar nombres de
archivos y gestionar dependencias de forma dinámica.

#### Función `subst`

La función `subst` reemplaza todas las ocurrencias de un texto por otro dentro de una
cadena.

!!! note "Sintaxis"

    ```makefile linenums="1"
    $(subst from,to,text)
    ```

    Donde `from` es el texto a reemplazar, `to` es el texto de reemplazo y `text` es la
    cadena sobre la que se realiza la búsqueda.

???+ example "Sustitución con `subst`"

    ```makefile linenums="1"
    SOURCES = file1.cpp file2.cpp file3.cpp
    OBJECTS = $(subst .cpp,.o,$(SOURCES))
    ```

    La función reemplaza `.cpp` por `.o` en la lista de archivos, lo que genera
    `file1.o file2.o file3.o`.

#### Función `patsubst`

La función `patsubst` realiza sustituciones basadas en patrones y utiliza el comodín `%`
para obtener una mayor flexibilidad.

!!! note "Sintaxis"

    ```makefile linenums="1"
    $(patsubst pattern,replacement,text)
    ```

    Donde `pattern` es el patrón que deben cumplir las palabras, `replacement` es el
    patrón de sustitución y `text` es la lista de palabras sobre la que se opera. El
    comodín `%` captura en `pattern` la parte variable del nombre y la reproduce en
    `replacement`.

???+ example "Sustitución de patrones"

    ```makefile linenums="1"
    SOURCES = file1.cpp file2.cpp file3.cpp
    OBJECTS = $(patsubst %.cpp,%.o,$(SOURCES))
    ```

    Este ejemplo produce el mismo resultado que `subst`, pero la sintaxis basada en
    patrones permite expresar transformaciones más complejas, ya que solo actúa sobre
    las palabras que coinciden por completo con el patrón.

#### Funciones `filter` y `filter-out`

Estas funciones permiten filtrar listas de elementos. La función `filter` conserva
únicamente las palabras que coinciden con un patrón determinado, mientras que
`filter-out` elimina aquellas que coinciden.

!!! note "Sintaxis"

    ```makefile linenums="1"
    $(filter pattern...,text)
    $(filter-out pattern...,text)
    ```

    Donde `pattern...` es uno o varios patrones separados por espacios y `text` es la
    lista de palabras que se desea filtrar.

???+ example "Filtrado de listas"

    ```makefile linenums="1"
    SOURCES = file1.c file2.cpp file3.h
    C_FILES = $(filter %.c,$(SOURCES))
    ```

    La función `filter` selecciona únicamente los archivos con extensión `.c`, lo que
    resulta en `file1.c`.

#### Función `foreach`

La función `foreach` permite iterar sobre una lista y aplicar una transformación a cada
elemento.

!!! note "Sintaxis"

    ```makefile linenums="1"
    $(foreach var,list,text)
    ```

    Donde `var` es el nombre de la variable temporal que toma el valor de cada palabra,
    `list` es la lista que se recorre y `text` es la expresión que se evalúa en cada
    iteración y cuyos resultados se concatenan.

???+ example "Iteración con `foreach`"

    ```makefile linenums="1"
    DIRS = dir1 dir2 dir3
    CLEAN_DIRS = $(foreach d,$(DIRS),$(d)/clean)
    ```

    Este ejemplo genera la lista `dir1/clean dir2/clean dir3/clean`. Conviene evitar
    nombres de variable que coincidan con funciones integradas, como `dir` o `notdir`,
    para que la expansión no resulte ambigua.

#### Función `if`

La función `if` permite evaluar una condición y devolver un valor u otro en función del
resultado.

!!! note "Sintaxis"

    ```makefile linenums="1"
    $(if condition,then-part[,else-part])
    ```

    Donde `condition` se considera cierta cuando su expansión no está vacía, `then-part`
    es el valor devuelto en ese caso y `else-part`, de carácter opcional, es el valor
    devuelto en caso contrario.

???+ example "Condicional con `if`"

    ```makefile linenums="1"
    USE_DEBUG = yes
    CFLAGS = $(if $(USE_DEBUG),-g,-O2)
    ```

    Si `USE_DEBUG` tiene un valor no vacío, se asigna `-g` para habilitar la depuración.
    En caso contrario se utiliza `-O2` para optimizar el resultado.

### Directivas

Las directivas controlan el flujo de ejecución, la inclusión de archivos externos y
otras configuraciones avanzadas del proceso de construcción.

#### `include`

La directiva `include` incorpora el contenido de otros _Makefiles_ dentro de uno
principal, lo que facilita la organización modular de proyectos grandes.

???+ example "Incluir otros _Makefiles_"

    ```makefile linenums="1"
    include config.mk
    ```

    Make inserta el contenido de `config.mk` en el punto donde aparece la directiva, de
    modo que las variables y reglas definidas en él quedan disponibles a partir de esa
    posición.

#### `VPATH`

La directiva `VPATH` especifica directorios adicionales donde Make buscará los archivos
necesarios cuando no los encuentre en el directorio actual.

???+ example "Búsqueda con `VPATH`"

    ```makefile linenums="1"
    VPATH = src:include
    ```

    Make busca primero en `src` y después en `include` para localizar los archivos
    requeridos.

#### `.PHONY`

La directiva `.PHONY` declara objetivos que no corresponden a archivos reales del
sistema de archivos. Esto evita conflictos en caso de que exista un archivo con el mismo
nombre que el _target_ y garantiza que la regla se ejecute siempre que se invoque.

???+ example "Objetivos `.PHONY`"

    ```makefile linenums="1"
    .PHONY: setup \
    	clean_cache_temp_files clean_notebooks \
    	lint code_check check_dead_code security \
    	test \
    	doc mlflow \
    	train_pipeline inference_pipeline \
    	profile_train profile_inference \
    	pipeline all
    ```

    Todos los _targets_ se declaran como objetivos ficticios, ya que representan tareas
    y no archivos reales. Esto resulta especialmente importante en proyectos donde los
    nombres de los _targets_ podrían coincidir con directorios existentes, como `test` o
    `doc`.

#### `.DEFAULT_GOAL`

La directiva `.DEFAULT_GOAL` define qué _target_ se ejecuta cuando se invoca `make` sin
argumentos.

???+ example "Objetivo por defecto"

    ```makefile linenums="1"
    .DEFAULT_GOAL := pipeline
    ```

    Con esta configuración, ejecutar `make` sin argumentos equivale a ejecutar
    `make pipeline`.

#### `.DELETE_ON_ERROR`

La directiva `.DELETE_ON_ERROR` indica que Make debe eliminar el archivo del _target_ si
alguno de sus comandos falla durante la ejecución, lo que evita la presencia de archivos
incompletos o corruptos en el sistema.

???+ example "Eliminación de objetivos incompletos"

    ```makefile linenums="1"
    .DELETE_ON_ERROR:

    output.csv: input.csv
    	python3 process_data.py $< $@
    ```

    Si el _script_ de procesamiento termina con un error después de haber creado
    `output.csv` de forma parcial, Make elimina el archivo resultante, de modo que la
    siguiente invocación vuelve a construirlo desde el principio.

### Condicionales

Los _Makefiles_ admiten estructuras condicionales que permiten adaptar las reglas según
diferentes entornos o configuraciones del proyecto.

!!! note "Sintaxis"

    ```makefile linenums="1"
    ifeq (arg1,arg2)
        acción
    else
        acción
    endif
    ```

    La directiva `ifeq` compara dos argumentos y ejecuta la primera rama cuando
    coinciden. La directiva `ifdef` evalúa en cambio si una variable se encuentra
    definida, con independencia de su valor.

???+ example "Condicionales `ifeq` e `ifdef`"

    ```makefile linenums="1"
    ifeq ($(USE_DEBUG),yes)
        CFLAGS = -g
    else
        CFLAGS = -O2
    endif

    ifdef VERBOSE
        OUTPUT = --verbose
    else
        OUTPUT =
    endif
    ```

    En el primer bloque, `ifeq` asigna `-g` cuando la variable `USE_DEBUG` tiene el
    valor `yes` y `-O2` en caso contrario. En el segundo bloque, `ifdef` añade la opción
    `--verbose` únicamente cuando la variable `VERBOSE` se encuentra definida.

### Macros y funciones personalizadas

Make permite definir macros que agrupan varios comandos bajo un nombre reutilizable, lo
que mejora la legibilidad y reduce la duplicación de código en _Makefiles_ extensos.

!!! note "Sintaxis"

    ```makefile linenums="1"
    define nombre_de_macro
        comandos
    endef
    ```

???+ example "Macro personalizada"

    ```makefile linenums="1"
    define compile_rule
    	$(CC) $(CFLAGS) -c $< -o $@
    endef

    %.o: %.c
    	$(call compile_rule)
    ```

    El bloque `define` almacena el cuerpo de la macro sin evaluarlo. La función `call`
    expande dicho cuerpo en el punto de la invocación, momento en el que las variables
    automáticas `$<` y `$@` toman los valores correspondientes al _target_ que se está
    construyendo. De este modo, una única definición sirve para todas las reglas que
    compilan archivos objeto.

## Buenas prácticas

El uso adecuado de los _Makefiles_ mejora la legibilidad del proyecto y facilita su
mantenimiento a largo plazo. A continuación se describen las prácticas más
recomendables.

### Organización

Conviene estructurar el _Makefile_ de forma que resulte fácil de leer y de mantener. Las
directivas y las variables deben situarse al inicio del archivo, seguidas de comentarios
claros que expliquen el propósito de cada sección. En proyectos de gran tamaño resulta
aconsejable dividir el _Makefile_ en módulos mediante la directiva `include`. Por
último, los _targets_ relacionados deben agruparse de forma lógica y ordenarse de lo más
general a lo más específico.

???+ example "_Makefile_ completo"

    ```makefile linenums="1"
    .PHONY: setup clean lint test pipeline all

    .DEFAULT_GOAL := pipeline

    # --- Variables ---
    PATH_RESEARCH ?= src
    PATH_TESTS ?= tests
    PATH_PROJECT_ROOT ?= .

    # --- Configuración ---
    setup:
    	@echo "Installing dependencies..."
    	@uv sync --all-groups
    	@uv run pre-commit install
    	@echo "✅ Dependencies installed."

    # --- Limpieza ---
    clean:
    	@echo "Cleaning cache and temporary files..."
    	@find . -type d -name __pycache__ -exec rm -rf {} +
    	@find . -type d -name .pytest_cache -exec rm -rf {} +
    	@find . -type d -name .mypy_cache -exec rm -rf {} +
    	@find . -type f \( -name '*.pyc' -o -name '*.pyo' \) -delete
    	@echo "✅ Clean complete."

    # --- Calidad de código ---
    lint:
    	@echo "Running lint checks..."
    	@uv run ruff format $(PATH_PROJECT_ROOT)
    	@uv run ruff check --fix $(PATH_PROJECT_ROOT)
    	@echo "✅ Linting complete."

    # --- Tests ---
    test:
    	@echo "Running tests..."
    	@uv run pytest $(PATH_TESTS) --maxfail=1 --durations=0
    	@echo "✅ Tests complete."

    # --- Pipelines ---
    pipeline: setup clean lint
    	@echo "✅ Pipeline complete."

    all: pipeline test
    	@echo "✅ All tasks complete."
    ```

    El archivo declara en primer lugar los objetivos ficticios y el _target_ por
    defecto, a continuación las variables de rutas y, finalmente, los _targets_
    agrupados por responsabilidad. Los _targets_ `pipeline` y `all` no ejecutan trabajo
    propio, sino que componen los anteriores a través de sus prerrequisitos.

### Depuración

Existen varias técnicas útiles para depurar un _Makefile_ cuando su comportamiento no es
el esperado. La ejecución en seco mediante la opción `-n` muestra qué comandos se
ejecutarían sin llevarlos a cabo realmente:

```bash linenums="1"
make -n
```

La opción `-d` proporciona una salida detallada sobre cómo Make procesa las reglas y
resuelve las dependencias, lo que resulta especialmente útil para comprender por qué un
_target_ se reconstruye o se considera actualizado:

```bash linenums="1"
make -d
```

Con la automatización cubierta, el módulo de programación desarrolla los lenguajes que
estas recetas invocan, empezando por los
[entornos virtuales de Python](../../03_programming/01_python/section_1_environments.md)
y las herramientas de calidad que aquí se han encadenado.
