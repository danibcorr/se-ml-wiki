---
authors: Daniel Bazo Correa
description: Automatización con Git Hooks y soluciones a escenarios frecuentes en Git.
title: Hooks y casos prácticos
---

Este capítulo cubre los dos mecanismos que completan el uso eficaz de Git más allá de
las estrategias de ramificación. Primero los Git Hooks, que permiten automatizar
comprobaciones dentro del flujo de trabajo, y después un catálogo de soluciones a las
situaciones que surgen con más frecuencia durante la gestión del historial de un
repositorio.

## Bibliografía

- Git Hooks. (s.f.). _Git Hooks_. <https://githooks.com/>
- pre-commit. (s.f.). _A framework for managing and maintaining multi-language
  pre-commit hooks_. <https://pre-commit.com/>

## Git Hooks

Los **Git Hooks** son una funcionalidad integrada en Git que permite automatizar tareas
y aplicar políticas a lo largo del flujo de trabajo. Gracias a ellos, Git puede ejecutar
acciones en momentos clave del proceso de desarrollo, lo que contribuye a asegurar la
calidad del código y el cumplimiento de las políticas específicas del proyecto.

### Funcionamiento

Los _hooks_ son _scripts_ que se ejecutan automáticamente en respuesta a eventos
concretos dentro de Git, como antes o después de realizar un _commit_, un _push_ o un
_merge_.

Para utilizarlos es necesario crear _scripts_ en el directorio `.git/hooks`, ubicado en
la raíz del repositorio. Al crear un nuevo repositorio, Git proporciona una serie de
_hooks_ de ejemplo que pueden modificarse según las necesidades del proyecto.

Estos _scripts_ deben ser ejecutables y llevar el nombre del evento que los activa, como
`pre-commit`, `pre-push` o `post-merge`. Los permisos de ejecución se conceden con el
comando `chmod`:

```bash linenums="1"
chmod +x .git/hooks/pre-commit
```

Una vez ubicados en el directorio correcto y con los permisos necesarios, Git ejecuta
automáticamente estos _scripts_ cuando se produce el evento correspondiente.

El desarrollo y la administración de _hooks_ se apoyan en dos pautas que aseguran su
eficacia y mantienen un flujo de trabajo ordenado. En primer lugar, los _hooks_ deben
ser **rápidos y confiables**, de manera que su ejecución no interfiera con la
productividad del equipo ni genere demoras en los procesos habituales de desarrollo. En
segundo lugar, conviene **evitar que realicen cambios automáticos en el código** sin la
aprobación explícita del desarrollador, ya que estas modificaciones pueden provocar
conflictos, errores inesperados o dificultades en la integración.

!!! note "Compartir _hooks_ entre el equipo"

    El directorio `.git/hooks` no se versiona, por lo que los _hooks_ definidos de forma
    manual no se propagan al clonar el repositorio. Para compartirlos existen
    herramientas como [pre-commit](https://pre-commit.com/), que declaran los _hooks_ en
    un archivo versionado y los instalan con un único comando.

### Tipos de Git Hooks

A continuación se presentan los _hooks_ de desarrollo más comunes. Si bien existen
_hooks_ para acciones posteriores al envío de código, como notificaciones o despliegues
a producción, se recomienda gestionar dichas tareas a través de herramientas de CI/CD
como GitHub Actions o GitLab CI/CD. De este modo, los _hooks_ se reservan para
validaciones locales, lo que optimiza el uso de los _runners_ remotos.

!!! note "Herramientas que invocan los ejemplos"

    Los _hooks_ que siguen llaman a uv, el gestor de entornos de Python, y a través de
    él a `ruff` y `pytest`. El capítulo de [entornos
    virtuales](../../03_programming/01_python/section_1_environments.md) describe su
    instalación y su configuración.

#### `pre-commit`

Se ejecuta antes de registrar un _commit_. Resulta útil para verificar el formato del
código, ejecutar pruebas unitarias, validar los mensajes de _commit_ o evitar errores
ortográficos.

???+ example "Verificar el estilo del código con Ruff"

    El siguiente _hook_ comprueba el formato del código con Ruff únicamente cuando la
    rama activa es `main`, y bloquea el _commit_ si detecta desviaciones de estilo:

    ```bash linenums="1"
    #!/bin/bash

    # Obtener la rama actual
    branch_name=$(git rev-parse --abbrev-ref HEAD)

    # Omitir la comprobación fuera de la rama main
    if [ "$branch_name" != "main" ]; then
        echo "Skipping Ruff: current branch is not 'main'."
        exit 0
    fi

    # Comprobar el formato sin modificar los archivos
    uv run ruff format --check .

    # Bloquear el commit si la comprobación ha fallado
    if [ $? -ne 0 ]; then
        echo "Style errors detected. Commit blocked."
        exit 1
    fi

    echo "Style check passed."
    ```

#### `pre-push`

Se ejecuta antes de enviar cambios a un repositorio remoto. Se emplea para evitar
_pushes_ en ramas protegidas o para ejecutar la batería de pruebas antes de publicar los
cambios.

???+ example "Ejecutar las pruebas antes de un push"

    El siguiente _hook_ sincroniza las dependencias del entorno con uv y ejecuta las
    pruebas con Pytest, de modo que el _push_ solo se completa si todas ellas se
    superan:

    ```bash linenums="1"
    #!/bin/bash

    # Comprobar que uv está disponible en el sistema
    if ! command -v uv &> /dev/null; then
        echo "Error: uv is not installed. See https://docs.astral.sh/uv/."
        exit 1
    fi

    # Sincronizar el entorno con las dependencias declaradas
    echo "Syncing dependencies with uv..."
    uv sync

    # Ejecutar la batería de pruebas
    echo "Running tests with Pytest..."
    uv run pytest -v ./tests

    # Bloquear el push si las pruebas han fallado
    if [ $? -ne 0 ]; then
        echo "Error: tests failed. Push blocked."
        exit 1
    fi

    echo "Test suite passed."
    ```

## Escenarios frecuentes

En el desarrollo con Git es habitual modificar el historial de _commits_ o sincronizar
el repositorio local con el remoto. A continuación se presentan los casos de uso más
frecuentes junto con sus soluciones correspondientes.

!!! danger "Operaciones irreversibles"

    Varios de los comandos de esta sección reescriben el historial o descartan cambios
    de forma permanente, entre ellos `git reset --hard`, `git clean -fd`,
    `git branch -D` y `git push --force-with-lease`. Antes de ejecutarlos conviene
    comprobar que todo el trabajo relevante se encuentra publicado en el repositorio
    remoto, ya que los cambios locales que no lo estén se perderán sin posibilidad de
    recuperación.

### Gestionar _commits_ no firmados

La firma criptográfica de los _commits_ acredita su autoría. Cuando un repositorio exige
firmas y el historial contiene _commits_ sin firmar, el envío al remoto se rechaza.
Existen varias estrategias para resolverlo en función del estado del repositorio.

!!! note "Requisito previo"

    La firma requiere una clave registrada en la plataforma y declarada en la
    configuración local mediante `git config --global user.signingkey id_clave`. La
    opción `git config --global commit.gpgsign true` activa la firma automática de todos
    los _commits_ posteriores.

Si los _commits_ existentes son válidos en cuanto a su contenido, pero carecen de firma,
es posible recrearlos de forma que la incorporen sin alterar los cambios que contienen:

???+ example "Firmar todos los _commits_ del historial"

    ```bash linenums="1"
    git rebase --root --exec 'git commit --amend --no-edit -S'
    git push --force-with-lease
    ```

Cuando los _commits_ sin firmar no aportan contenido relevante, la alternativa consiste
en descartarlos durante un _rebase_ interactivo sobre una rama nueva:

???+ example "Reescribir el historial eliminando _commits_ no firmados"

    ```bash linenums="1"
    # Identificar los commits no firmados, marcados con la letra N
    git log --pretty="%h %G?"

    # Crear una rama limpia a partir del estado actual
    git checkout -b rama-limpia

    # Marcar con 'drop' los commits no firmados en el editor interactivo
    git rebase -i --root

    # Publicar la rama reescrita
    git push --force-with-lease origin rama-limpia
    ```

Una última alternativa consiste en restablecer la rama a un _commit_ firmado concreto,
con lo que se descartan todos los _commits_ posteriores:

???+ example "Restablecer la rama a un _commit_ firmado"

    ```bash linenums="1"
    git reset --hard commit_firmado
    git push --force-with-lease
    ```

### Sincronizar el repositorio local con el remoto

Para alinear la rama local exactamente con la remota, descartando cualquier cambio local
y los archivos sin seguimiento, se combinan tres operaciones en una sola instrucción:

???+ example "Sincronizar mediante `reset`"

    ```bash linenums="1"
    git fetch origin && git reset --hard origin/main && git clean -fd
    ```

    El comando `git fetch` actualiza las referencias remotas, `git reset --hard` sitúa
    la rama local en el mismo _commit_ que la remota y `git clean -fd` elimina los
    archivos y directorios sin seguimiento que hubieran quedado en el directorio de
    trabajo.

Cuando se prefiere partir de una copia completamente limpia procedente del servidor, la
rama local puede eliminarse y volver a crearse a partir de su equivalente remota:

???+ example "Recrear la rama local"

    ```bash linenums="1"
    git checkout main
    git branch -D nombre-rama
    git checkout -b nombre-rama origin/nombre-rama
    ```

### Eliminar ramas locales que ya no existen en el remoto

Con el tiempo, el repositorio local tiende a acumular ramas que ya no existen en el
remoto, por ejemplo tras su eliminación en el servidor una vez completada la
integración. Esta acumulación dificulta la gestión del entorno local y puede generar
confusión sobre el estado real del proyecto. Para mantener un espacio de trabajo limpio
es posible eliminar de forma automática todas las ramas locales cuyo remoto de
seguimiento ha dejado de existir:

???+ example "Eliminar ramas locales obsoletas"

    ```bash linenums="1"
    git fetch --prune && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D
    ```

    Donde:

    - **`git fetch --prune`**: Actualiza las referencias remotas y elimina las que ya no
      existen en el servidor.
    - **`git branch -vv`**: Lista las ramas locales con información detallada, incluida
      la rama remota a la que siguen (_tracking_).
    - **`grep ': gone]'`**: Filtra las ramas cuyo remoto de seguimiento ya no existe,
      que aparecen marcadas como `gone`.
    - **`awk '{print $1}'`**: Extrae únicamente el nombre de la rama local.
    - **`xargs git branch -D`**: Elimina dichas ramas del repositorio local.

Los _hooks_ descritos invocan herramientas del ecosistema de Python. Su instalación y su
configuración en el archivo `pyproject.toml` se desarrollan en el capítulo de
[entornos virtuales](../../03_programming/01_python/section_1_environments.md).
