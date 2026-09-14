---
authors: Daniel Bazo Correa
description:
    Fundamentos de Bash como intérprete de comandos y lenguaje de scripting, con su
    sintaxis, estructuras de control y técnicas de automatización.
title: Bash
---

Este capítulo presenta Bash como intérprete de comandos y lenguaje de _scripting_, y
cubre su sintaxis básica, las estructuras de control disponibles y las técnicas
habituales de automatización de tareas.

## Bibliografía

- Pelado Nerd. (2021). _TODOS deberían aprender BASH - Bash PARTE 1_ \[Vídeo\]. YouTube.
  <https://www.youtube.com/watch?v=4_ub6614dwY>

## Introducción

<figure markdown="span">
  ![Logo de Bash](../../assets/img/docs/logos/bash-logo.png)
  <figcaption>Logo de Bash.</figcaption>
</figure>

**Bash** (_Bourne-Again Shell_) es un intérprete de comandos y un lenguaje de
programación ampliamente utilizado en sistemas basados en Unix. Fue desarrollado por
Brian Fox para el proyecto GNU y publicado en 1989 como una evolución del _Bourne Shell_
(`sh`). Su disponibilidad prácticamente universal en distribuciones de GNU/Linux lo
convierte en la herramienta de referencia para automatizar tareas del sistema.

## Sintaxis

### Estructura de un _script_

Todo programa en Bash comienza con la línea `#!/bin/bash`, conocida como _shebang_. Esta
directiva indica al sistema operativo qué intérprete debe utilizar para ejecutar los
comandos contenidos en el archivo, con independencia del entorno desde el que se
invoque.

Los _scripts_ de Bash emplean por convención la extensión `.sh`, lo que facilita su
identificación dentro del sistema de archivos.

A continuación del _shebang_ se añaden las instrucciones que definen las acciones a
realizar, como mostrar mensajes en la terminal, invocar otros _scripts_ o ejecutar
tareas específicas del sistema.

???+ example "Hola mundo"

    Para ilustrar el proceso, se crea un _script_ básico denominado `script.sh` que
    muestra un mensaje en la terminal:

    ```bash linenums="1"
    #!/bin/bash

    echo "Hola mundo"
    ```

Un _script_ recién creado no dispone de permisos de ejecución, lo que impide su
invocación directa. Para que el sistema lo reconozca como un programa ejecutable es
necesario otorgarle dichos permisos con el comando `chmod`.

???+ example "Conceder permisos de ejecución"

    La opción `+x` de `chmod` añade el permiso de ejecución al archivo:

    ```bash linenums="1"
    chmod +x script.sh
    ```

    Una vez concedido el permiso, el _script_ puede invocarse directamente:

    ```bash linenums="1"
    ./script.sh
    ```

!!! note "Fundamentos de Linux"

    El modelo de permisos de archivos y el resto de utilidades básicas del sistema se
    describen en el capítulo de [fundamentos de
    Linux](../../01_operative_systems/01_linux/section_1_fundamentals.md).

### Argumentos

Bash permite pasar parámetros a un _script_ en el momento de su invocación. Dentro del
_script_, estos argumentos se referencian mediante el símbolo `$` seguido de un número
que indica su posición.

???+ example "Argumentos posicionales"

    ```bash linenums="1"
    #!/bin/bash

    echo "Hola $1"
    echo "Adiós $2"
    ```

    En este caso, `$1` corresponde al primer argumento proporcionado y `$2` al segundo.
    La variable `$0` contiene siempre el nombre del propio _script_, mientras que `$#`
    almacena el número total de argumentos recibidos.

### Variables

Las variables en Bash se asignan de forma directa, sin necesidad de declarar su tipo
previamente. El nombre de la variable se escribe seguido del signo `=` y del valor
deseado, sin espacios entre ellos.

???+ example "Declarar variables"

    ```bash linenums="1"
    #!/bin/bash

    nombre="Daniel"
    echo "Mi nombre es $nombre"
    ```

    También es posible almacenar el resultado de la ejecución de un comando del sistema
    en una variable mediante la sintaxis `$(comando)`:

    ```bash linenums="1"
    #!/bin/bash

    resultado=$(comando)
    ```

### Entrada del usuario

Para capturar datos introducidos durante la ejecución de un _script_ se utiliza el
comando `read`, que detiene la ejecución hasta que se proporciona un valor y se pulsa la
tecla _Enter_.

???+ example "Leer la entrada del usuario"

    ```bash linenums="1"
    #!/bin/bash

    echo "¿Cuál es tu nombre?"
    read nombre
    echo "Tu nombre es $nombre"
    ```

### Operaciones aritméticas

Las operaciones aritméticas se evalúan dentro de la construcción `$(( ))`, que permite
realizar cálculos con números enteros.

???+ example "Operaciones aritméticas"

    ```bash linenums="1"
    #!/bin/bash

    echo $((5 + 5))
    ```

Los operadores aritméticos disponibles son los siguientes:

| Operador | Descripción                    |
| -------- | ------------------------------ |
| `+`      | Suma.                          |
| `-`      | Resta.                         |
| `*`      | Multiplicación.                |
| `/`      | División entera.               |
| `%`      | Módulo o resto de la división. |

### Condiciones

Las estructuras condicionales se construyen mediante el comando `if`, que evalúa una
expresión y ejecuta un bloque de código en función del resultado. La expresión se
delimita con corchetes, ya sea `[ ]`, la forma heredada de POSIX, o `[[ ]]`, la
construcción propia de Bash.

???+ example "Condicional `if`/`else`"

    ```bash linenums="1"
    #!/bin/bash

    if [ "$1" == "Dani" ] || [ "$1" == "Paco" ]; then
        echo "Hola $1"
    elif [ "$1" == "Jorge" ]; then
        echo "Bienvenido"
    else
        echo "Intruso"
    fi
    ```

La comparación de cadenas de texto emplea los siguientes operadores:

| Operador | Descripción                                |
| -------- | ------------------------------------------ |
| `=`      | Igual a, en su forma compatible con POSIX. |
| `==`     | Igual a, en la forma propia de Bash.       |
| `!=`     | Distinto de.                               |
| `-z`     | La cadena está vacía.                      |
| `-n`     | La cadena no está vacía.                   |

La comparación de números enteros utiliza operadores específicos, ya que los símbolos
`<` y `>` se interpretan como redirecciones dentro de `[ ]`:

| Operador | Descripción        |
| -------- | ------------------ |
| `-eq`    | Igual a.           |
| `-ne`    | Distinto de.       |
| `-gt`    | Mayor que.         |
| `-ge`    | Mayor o igual que. |
| `-lt`    | Menor que.         |
| `-le`    | Menor o igual que. |

La comprobación del estado del sistema de archivos se apoya en un tercer grupo de
operadores, imprescindibles antes de leer o escribir sobre una ruta:

| Operador | Descripción                         |
| -------- | ----------------------------------- |
| `-e`     | La ruta existe.                     |
| `-f`     | La ruta es un archivo regular.      |
| `-d`     | La ruta es un directorio.           |
| `-r`     | La ruta tiene permiso de lectura.   |
| `-x`     | La ruta tiene permiso de ejecución. |

Por último, los operadores lógicos permiten combinar varias condiciones. El operador
`&&` expresa la conjunción lógica (AND), el operador `||` expresa la disyunción lógica
(OR) y el operador `!` invierte el resultado de una expresión (NOT).

!!! note "Preferencia por los dobles corchetes"

    Dentro de `[ ]`, la conjunción y la disyunción se expresan con `-a` y `-o`, opciones
    consideradas obsoletas. La construcción `[[ ]]` admite directamente `&&` y `||`, no
    requiere entrecomillar las variables y permite comparar cadenas de forma
    lexicográfica con `<` y `>`, por lo que constituye la opción recomendada en Bash.
    Para comparar números también puede emplearse `(( ))`, donde los operadores
    aritméticos habituales recuperan su significado matemático.

### Bucles

Bash ofrece varias estructuras de repetición. El bucle `for` resulta adecuado cuando se
conoce de antemano el conjunto de valores sobre los que iterar.

???+ example "Bucle `for`"

    ```bash linenums="1"
    #!/bin/bash

    for i in 1 2 3; do
        echo $i
    done
    ```

El bucle `while` se emplea cuando la repetición debe continuar mientras se cumpla una
condición determinada, lo que lo hace idóneo para situaciones en las que el número de
iteraciones depende de una variable o del resultado de una operación.

???+ example "Bucle `while`"

    ```bash linenums="1"
    #!/bin/bash

    i=1
    while [ $i -le 5 ]; do
        echo $i
        (( i++ ))
    done
    ```

Dentro de cualquier bucle, el comando `break` finaliza la ejecución del bucle de forma
inmediata, mientras que `continue` salta directamente a la siguiente iteración sin
ejecutar el resto del cuerpo.

???+ example "Uso de `break` y `continue`"

    ```bash linenums="1"
    #!/bin/bash

    i=0
    max_value=5

    while [ $i -le $max_value ]; do
        if [ $(( i % 2 )) -eq 0 ]; then
            echo "Valor par $i encontrado, continúo."
            (( i++ ))
            continue
        else
            echo "Valor impar $i encontrado, termino."
            break
        fi
    done
    ```

### Funciones

Las funciones permiten encapsular bloques de código reutilizables, lo que favorece la
modularidad, la legibilidad y el mantenimiento de los _scripts_. Una vez definida, una
función se invoca escribiendo su nombre, sin paréntesis.

???+ example "Definir una función"

    ```bash linenums="1"
    #!/bin/bash

    function funcion() {
        echo "Esta es una función de prueba"
    }

    funcion
    ```

Las funciones reciben argumentos con la misma notación posicional que los _scripts_, de
modo que `$1` y `$2` se refieren a los parámetros de la llamada y no a los del _script_
principal. La palabra reservada `local` restringe el ámbito de una variable al cuerpo de
la función, lo que evita que las asignaciones internas sobrescriban variables globales.
La instrucción `return` devuelve un código de salida numérico, mientras que el resultado
textual de una función se obtiene mediante `echo` combinado con la sustitución de
comandos.

???+ example "Argumentos, variables locales y valores de retorno"

    ```bash linenums="1"
    #!/bin/bash

    function saludar() {
        local nombre=$1
        echo "Hola $nombre"
    }

    # Captura del texto devuelto por la función
    mensaje=$(saludar "Daniel")
    echo $mensaje
    ```

### Control de errores

Por defecto, un _script_ de Bash continúa su ejecución aunque uno de sus comandos falle,
lo que puede provocar que se realicen operaciones sobre un estado inconsistente.

La opción `set -e` modifica este comportamiento y hace que el _script_ finalice de forma
inmediata en cuanto cualquier comando devuelve un código de salida distinto de cero. De
este modo, la ejecución se detiene en el primer error en lugar de propagarlo a las
instrucciones posteriores.

???+ example "Detención ante el primer error"

    ```bash linenums="1"
    #!/bin/bash
    set -e

    # Este comando falla y detiene el script en este punto
    cp archivo_inexistente.txt /tmp/
    echo "Esto nunca se ejecuta"
    ```

Existen situaciones en las que `set -e` no detiene el _script_ aunque un comando falle.
Los comandos evaluados dentro de una condición `if` o `while` no interrumpen la
ejecución, ya que su código de salida forma parte de la propia evaluación de la
condición. Tampoco lo hacen los comandos encadenados mediante los operadores `||` o
`&&`, puesto que el fallo se contempla dentro de la lógica de la expresión. Del mismo
modo, un comando ejecutado en una _subshell_ no detiene el _script_ principal cuando el
error se gestiona de forma externa.

En la práctica, `set -e` suele combinarse con otras opciones que refuerzan la robustez
del _script_:

```bash linenums="1"
#!/bin/bash
set -euo pipefail
```

Donde:

- `-e`: Finaliza el _script_ ante cualquier error.
- `-u`: Genera un error si se utiliza una variable no definida.
- `-o pipefail`: Propaga al conjunto de un _pipeline_ el fallo de cualquiera de sus
  comandos. Sin esta opción únicamente se considera el código de salida del último
  comando.

## Automatización

La combinación de las estructuras anteriores permite automatizar tareas complejas. En el
desarrollo de proyectos, esta automatización resulta fundamental para garantizar la
reproducibilidad y la eficiencia del entorno de trabajo.

!!! note "Herramientas ajenas a Bash"

    El siguiente ejemplo invoca uv, el gestor de entornos de Python que se describe en
    el capítulo de [entornos
    virtuales](../../03_programming/01_python/section_1_environments.md). Aquí solo
    interesa como orden externa cuya disponibilidad el _script_ comprueba antes de
    usarla.

???+ example "_Script_ de configuración de entorno"

    El siguiente _script_ ilustra cómo preparar un entorno de desarrollo completo,
    verificando e instalando las herramientas necesarias y gestionando las dependencias
    tanto del sistema como de Python:

    ```bash linenums="1"
    #!/bin/bash
    set -e

    # Verificación e instalación de la herramienta uv
    echo "Checking for uv..."
    if ! command -v uv &> /dev/null; then
        echo "Installing uv..."
        curl -LsSf https://astral.sh/uv/install.sh | sh > /dev/null 2>&1
        echo "✅ uv installed."
    else
        echo "✅ uv already installed."
    fi

    # Instalación de dependencias del sistema
    echo "Installing system dependencies..."
    sudo apt-get update > /dev/null 2>&1
    sudo apt-get install -y build-essential > /dev/null 2>&1
    echo "✅ System dependencies installed."

    # Instalación de dependencias de Python
    echo "Installing Python dependencies with uv..."
    uv sync > /dev/null 2>&1
    echo "✅ Python dependencies installed."
    ```

    El _script_ ejecuta de manera secuencial varias operaciones clave. En primer lugar,
    verifica si la herramienta uv se encuentra instalada en el sistema y, en caso
    contrario, procede a su instalación de forma silenciosa. La redirección
    `> /dev/null 2>&1` descarta tanto la salida estándar como los mensajes de error, lo
    que evita que aparezcan en la terminal durante la ejecución.

    Posteriormente actualiza los repositorios del sistema e instala los paquetes de
    compilación esenciales mediante `apt-get`, también de manera silenciosa. Por último,
    invoca al gestor de dependencias uv para instalar las dependencias de Python, con
    lo que el entorno de desarrollo queda completamente operativo.

Cuando el número de tareas automatizadas crece, mantenerlas como _scripts_
independientes dificulta su descubrimiento y su encadenamiento. En ese punto resulta
conveniente agrupar las tareas en un único punto de entrada mediante
[Makefile](section_2_makefile.md), que aporta un catálogo de objetivos y un sistema de
dependencias entre ellos.
