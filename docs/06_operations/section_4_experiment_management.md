---
authors: Daniel Bazo Correa
description:
    Versionado de datos, definición de pipelines y seguimiento de experimentos con DVC y
    MLflow.
title: Seguimiento de experimentos
---

!!! warning

    El contenido de esta página no ha sido revisado ni corregido, por lo que puede
    estar incompleto, contener errores o presentar información desactualizada. Además,
    es posible que esté desordenado, carezca de una estructura clara o incluya notas
    copiadas directamente.

Este capítulo aborda el versionado de datos, la definición de _pipelines_ de aprendizaje
automático, el seguimiento de experimentos y la validación de datos y modelos, con DVC y
MLflow como herramientas de referencia.

## Bibliografía

- DVC. (s.f.). _Data Version Control Documentation_. <https://dvc.org/doc>
- MLflow. (s.f.). _MLflow Documentation_. <https://mlflow.org/docs/latest/index.html>
- Wolf, A. y Fursov, I. (s.f.). _ML in Production: From Data Scientist to ML Engineer_
  \[Curso\]. Udemy. <https://www.udemy.com/course/ml-in-production/>

## Introducción

**MLOps**, o _Machine Learning Operations_, designa el conjunto de prácticas y
herramientas que permiten desarrollar, desplegar y mantener modelos de aprendizaje
automático en producción. Su ámbito combina la ingeniería de software, la computación en
la nube y la administración de sistemas, y su objetivo es que los modelos resulten
eficaces, escalables y sostenibles a lo largo del tiempo.

Un sistema de este tipo se articula en torno a varios componentes. En el núcleo se
encuentra el modelo, que representa la solución entrenada sobre un conjunto de datos.
Ese modelo se ejecuta sobre una infraestructura que abarca desde servicios en la nube
hasta servidores locales o los propios dispositivos, en el caso de la inferencia _on
edge_. Una API expone el modelo para atender peticiones y devolver predicciones, y los
mecanismos de seguimiento y monitorización garantizan la calidad y el rendimiento del
servicio.

La diferencia fundamental respecto al desarrollo de software convencional es que el
comportamiento del sistema no depende solo del código, sino también de los datos con los
que se entrena y de los hiperparámetros de cada ejecución. Reproducir un resultado exige
por tanto versionar los tres elementos de forma conjunta, y ese es precisamente el
problema que resuelven las herramientas descritas en este capítulo.

**DVC** (_Data Version Control_) es una herramienta especializada en el versionado de
datos, la gestión de _pipelines_ de aprendizaje automático, el seguimiento de
experimentos y el intercambio de artefactos. El contenido se estructura en módulos
progresivos que abarcan desde la configuración básica hasta las técnicas de colaboración
y despliegue.

## Versionamiento de datos y almacenamiento remoto

### Integración de Git y DVC

Este módulo establece los fundamentos de cómo DVC complementa a Git para gestionar y
versionar conjuntos de datos y modelos de gran tamaño, manteniendo estos artefactos
fuera del repositorio de código principal. La arquitectura resultante aprovecha las
fortalezas de ambas herramientas: Git gestiona el código fuente y los metadatos ligeros,
mientras DVC se especializa en el manejo de archivos binarios pesados.

### Configuración inicial del entorno de trabajo

El flujo de trabajo comienza estableciendo la infraestructura básica mediante la
inicialización de ambos sistemas de control. El comando `git init` crea un nuevo
repositorio Git en el directorio de trabajo, seguido inmediatamente por `dvc init`, que
establece la estructura necesaria para el funcionamiento de DVC dentro del mismo
directorio. Esta secuencia prepara el entorno para una gestión dual: Git controlará las
versiones del código y la configuración, mientras DVC administrará los datos y modelos.

La obtención inicial de datos se realiza mediante `dvc get <URL_registro> <ruta>`, un
comando que actúa como una capa de abstracción de alto nivel sobre herramientas
tradicionales como `wget` o `curl`. Este comando permite descargar conjuntos de datos
desde registros DVC externos, funcionando de manera análoga a gestores de paquetes en
otros ecosistemas de desarrollo. Por ejemplo, puede descargarse un conjunto de datos de
36 MB desde un registro público de DVC sin necesidad de gestionar manualmente URLs
complejas o protocolos de transferencia.

### Mecanismo de seguimiento de archivos grandes

Git presenta limitaciones arquitectónicas fundamentales cuando se enfrenta a archivos de
gran tamaño: su modelo de almacenamiento interno no está optimizado para datos binarios,
y el versionado de archivos grandes resulta en repositorios excesivamente pesados y
lentos. DVC resuelve esta problemática mediante una estrategia de indirección: rastrea
archivos de metadatos ligeros mientras mantiene los archivos pesados en una ubicación
separada.

El comando `dvc add data/data.xml` inicia el seguimiento de un archivo grande. Esta
operación genera dos artefactos críticos. Primero, crea el archivo `data.xml.dvc`, un
archivo de metadatos ligero que contiene información esencial sobre el archivo original,
incluyendo su hash criptográfico y su ubicación relativa. Segundo, añade automáticamente
una entrada al archivo `.gitignore` del directorio correspondiente, instruyendo a Git
para que ignore completamente el archivo de datos real. Esta configuración asegura que
el archivo pesado nunca se incorpore accidentalmente al repositorio Git.

El proceso de versionado subsecuente utiliza comandos Git estándar. Se ejecuta
`git add data/.gitignore` para versionar la configuración de archivos ignorados, seguido
de `git add data.xml.dvc` para incorporar el archivo de metadatos al repositorio.
Finalmente, `git commit -m "Mensaje"` consolida estos cambios en el historial de Git. El
archivo `.dvc` versionado contiene únicamente una ruta relativa y un hash criptográfico
que vincula inequívocamente al dato original, pero el dato real permanece completamente
fuera del repositorio Git.

Un concepto fundamental que debe enfatizarse es que DVC no constituye estrictamente un
sistema de control de versiones autónomo. Git realiza el control de versiones efectivo
de los metadatos, y DVC extiende esa funcionalidad para manejar datos y binarios
grandes, específicamente modelos entrenados, que se mantienen fuera del repositorio Git
pero cuyas versiones quedan registradas a través de los archivos de metadatos
versionados por Git.

### Configuración y operación del almacenamiento remoto

Para facilitar la colaboración y el intercambio de datos entre equipos, los archivos
pesados deben residir en una ubicación externa accesible denominada almacenamiento
remoto o remote. La configuración de este almacenamiento se realiza mediante el comando
`dvc remote add -d <nombre> <tipo>://<ID>`. Por ejemplo,
`dvc remote add -d storage gdrive://<ID>` configura una carpeta específica de Google
Drive como almacenamiento remoto predeterminado de DVC, donde el flag `-d` designa este
remote como la ubicación predeterminada para operaciones de sincronización. DVC admite
múltiples tipos de almacenamiento, incluyendo buckets de Amazon S3, Google Cloud
Storage, Azure Blob Storage, servidores SSH, o incluso directorios locales en sistemas
de archivos compartidos.

La configuración del remote se almacena en el archivo de configuración de DVC, que debe
ser versionado mediante `git commit` para que todos los colaboradores del proyecto
compartan la misma configuración de almacenamiento. Una vez configurado, el comando
`dvc push` carga los datos grandes rastreados por DVC al almacenamiento remoto
especificado, estableciendo una copia centralizada accesible para todo el equipo.

La operación inversa se realiza mediante `dvc pull`, que descarga los datos desde el
almacenamiento remoto a la máquina local. Este comando utiliza la información contenida
en el archivo `.dvc` versionado por Git para determinar exactamente qué archivos deben
descargarse y desde dónde, coordinando automáticamente el acceso al almacenamiento
remoto configurado. El flujo resultante es análogo al modelo push/pull de Git, pero
aplicado a artefactos de datos en lugar de código fuente.

### Navegación temporal en el historial de datos

Una funcionalidad poderosa de este sistema integrado es la capacidad de acceder a
versiones históricas específicas de los datos. El proceso combina comandos de ambas
herramientas de forma coordinada. Primero se utiliza `git checkout <commit_id>` para
retroceder el repositorio a un commit anterior específico. Esta operación cambia la
versión del archivo `.dvc` presente en el directorio de trabajo, recuperando los
metadatos que describen la versión de datos correspondiente a ese momento en el
historial del proyecto.

Posteriormente se ejecuta `dvc checkout`, sin argumentos adicionales. DVC lee el hash
criptográfico contenido en el archivo `.dvc` ahora presente en el directorio de trabajo
y recupera la versión exacta de datos correspondiente. Esta recuperación se realiza
primero consultando la caché local de DVC, y si la versión solicitada no está disponible
localmente, se descarga desde el almacenamiento remoto. Por ejemplo, este mecanismo
permite recuperar un archivo de datos de 36 MB correspondiente a un experimento antiguo,
incluso si la versión actual del mismo archivo ha crecido a 72 MB, garantizando
reproducibilidad completa de experimentos históricos.

## Creación y reproducción de pipelines de ML

### Fundamentos de pipelines

Las pipelines de DVC constituyen un mecanismo para encadenar sistemáticamente todos los
pasos computacionales de un proyecto de Machine Learning, desde la preparación inicial
de datos hasta el entrenamiento final del modelo. El objetivo fundamental es asegurar la
reproducibilidad completa del proceso y mantener un registro explícito de todas las
transformaciones aplicadas a los datos. A diferencia de scripts aislados ejecutados
manualmente, una pipeline DVC formaliza las dependencias entre etapas, permitiendo que
el sistema determine automáticamente qué cálculos deben repetirse cuando cambian las
entradas.

### Estructura y definición de etapas

Una pipeline se compone de etapas individuales que pueden definirse mediante el comando
`dvc run` o editando directamente el archivo de configuración `dvc.yaml`. La segunda
opción proporciona mayor control y claridad cuando se gestionan pipelines complejas con
múltiples etapas interdependientes.

Cada etapa requiere la especificación de cuatro componentes esenciales. El **comando**
define el script de Python o programa que debe ejecutarse, especificado con su ruta
completa y argumentos necesarios, por ejemplo `python src/train.py`. Las
**dependencias** (`deps`) enumeran explícitamente todos los archivos necesarios para la
ejecución de la etapa, incluyendo tanto los scripts de código fuente (archivos `.py`)
como los archivos de entrada de datos. Esta especificación permite a DVC detectar cuándo
ha cambiado cualquier entrada y determinar si la etapa debe re-ejecutarse. Las
**salidas** (`outs`) identifican los archivos generados por la etapa, que pueden ser
archivos binarios como modelos serializados (`model.pickle`) o conjuntos de datos
transformados (`data_prepared.csv`). Finalmente, los **parámetros** (`params`)
referencian hiperparámetros almacenados en el archivo `params.yaml`, utilizando notación
de punto para especificar valores anidados, como `train.n_estimators` para acceder al
número de estimadores en la sección de entrenamiento.

El encadenamiento de etapas ocurre de forma natural cuando la salida de una etapa se
especifica como dependencia de la siguiente. Por ejemplo, si la etapa `prepare` genera
el archivo `data_prepared.csv` como salida, y este mismo archivo se lista como
dependencia de la etapa `featurize`, DVC establece automáticamente la relación de
dependencia entre ambas etapas. Esta estructura permite que DVC construya un grafo
acíclico dirigido (Directed Acyclic Graph o DAG) que representa el flujo completo del
proyecto.

### Reproducción inteligente y optimización computacional

El comando central para ejecutar la pipeline completa es `dvc repro`. Este comando
analiza el archivo `dvc.yaml`, construye el grafo de dependencias y ejecuta todas las
etapas en el orden correcto determinado por las dependencias especificadas. Sin embargo,
la funcionalidad más valiosa de `dvc repro` es su capacidad de reproducción inteligente:
el sistema solo vuelve a ejecutar las etapas que han cambiado o cuyas dependencias o
parámetros han sido modificados.

Esta optimización computacional se fundamenta en dos archivos complementarios. El
archivo `dvc.yaml` es legible por humanos y contiene la definición completa de la
pipeline en formato YAML estructurado. Este archivo se versiona con Git y sirve como
documentación ejecutable del flujo de trabajo. El archivo `dvc.lock`, generado
automáticamente por DVC, no está diseñado para lectura humana y contiene hashes
criptográficos que representan las versiones exactas de todas las dependencias y salidas
de cada etapa. DVC consulta este archivo para determinar si una etapa necesita ser
reproducida o si puede utilizar resultados previamente calculados de la caché.

Cuando se ejecuta `dvc repro`, el sistema compara los hashes actuales de todos los
archivos de entrada y parámetros con los hashes almacenados en `dvc.lock`. Si todos los
hashes coinciden, DVC omite la ejecución de la etapa, mostrando el mensaje `skipping` en
la salida de consola. Esta funcionalidad resulta especialmente valiosa en pipelines
complejas con etapas computacionalmente costosas: solo las etapas realmente afectadas
por un cambio específico se re-ejecutan, ahorrando potencialmente horas de cómputo en
proyectos grandes.

### Visualización y comprensión del flujo de trabajo

El comando `dvc dag` proporciona una visualización textual del grafo acíclico dirigido
que representa la pipeline. Esta visualización muestra todas las etapas del pipeline y
las relaciones de dependencia entre ellas mediante una representación gráfica ASCII. La
salida de `dvc dag` facilita la depuración de pipelines complejas, permitiendo
identificar rápidamente cuellos de botella, dependencias circulares accidentales, o
etapas huérfanas que no están conectadas al flujo principal. Esta herramienta resulta
particularmente útil cuando se colabora en equipo, ya que proporciona una visión de alto
nivel del proceso completo sin necesidad de analizar manualmente los archivos de
configuración.

## Seguimiento y comparación de experimentos

### Arquitectura de evaluación y seguimiento

Este módulo aborda la problemática de comparar sistemáticamente los resultados de
diferentes experimentos de Machine Learning, incluyendo métricas cuantitativas,
parámetros de configuración y visualizaciones cualitativas, a través de diferentes
commits o ramas del repositorio Git. La capacidad de realizar estas comparaciones de
forma automatizada resulta fundamental para la toma de decisiones informadas sobre qué
variantes de modelos merecen ser promovidas a producción.

La implementación comienza añadiendo una etapa final denominada `evaluate` a la pipeline
de DVC. Esta etapa ejecuta un script que calcula métricas de rendimiento sobre un
conjunto de datos de validación o prueba y genera archivos estructurados conteniendo
tanto valores numéricos como datos para visualizaciones.

### Especificación de métricas y visualizaciones

DVC distingue entre dos tipos de salidas especializadas que reciben tratamiento especial
para facilitar la comparación. Las **métricas** (`metrics`) son archivos que contienen
valores numéricos escalares representando el rendimiento del modelo. Estos archivos
típicamente utilizan formato JSON para permitir la estructuración jerárquica de
múltiples métricas. Por ejemplo, un archivo `scores.json` podría contener el Área Bajo
la Curva ROC (AUC) para problemas de clasificación binaria, junto con métricas
adicionales como precisión, recall y F1-score. La designación de un archivo como métrica
en el `dvc.yaml` indica a DVC que estos valores deben ser rastreados y estar disponibles
para comandos de comparación.

Los **gráficos** (`plots`) son archivos que contienen arreglos de datos estructurados
que DVC puede renderizar como visualizaciones interactivas. Por ejemplo, un archivo
`prc.json` podría contener tres arreglos paralelos representando valores de precisión,
recall y threshold a lo largo de diferentes puntos de operación del clasificador. DVC
puede procesar estos datos para generar automáticamente curvas de precisión-recuperación
(Precision-Recall Curves) que permiten evaluación visual del rendimiento del modelo a
través de diferentes configuraciones.

### Metodología de comparación experimental

El flujo de trabajo para experimentación sistemática se estructura alrededor del
concepto de "instantáneas" guardadas mediante commits de Git. El proceso comienza
estableciendo una línea base: se ejecuta la pipeline completa con una configuración
inicial, se verifican los resultados, y se guarda el estado completo mediante
`git commit`. Este commit establece un punto de referencia contra el cual se compararán
experimentos futuros.

Para realizar un nuevo experimento, se modifican los parámetros relevantes en el archivo
`params.yaml`. Por ejemplo, se podría cambiar el número de estimadores de un Random
Forest de 100 a 200, o modificar la profundidad máxima de los árboles. Tras guardar
estos cambios, se ejecuta `dvc repro`, que detecta el cambio en los parámetros y
re-ejecuta todas las etapas afectadas. En este punto, el directorio de trabajo contiene
los resultados del nuevo experimento, pero no se ha realizado ningún commit. Esta
separación permite utilizar comandos de comparación para evaluar el impacto del cambio
antes de decidir si vale la pena preservarlo en el historial.

### Comandos de comparación y análisis

DVC proporciona tres comandos especializados para comparar diferentes aspectos de los
experimentos. El comando `dvc params diff` presenta un resumen tabular mostrando cómo
han cambiado los hiperparámetros almacenados en `params.yaml` desde el último commit.
Esta tabla incluye típicamente tres columnas: el nombre del parámetro, su valor en el
commit de referencia y su valor en el estado actual del directorio de trabajo. Esta
comparación resulta esencial para documentar exactamente qué se modificó entre
experimentos.

El comando `dvc metrics diff` muestra cómo han cambiado las métricas numéricas desde el
último commit. La salida no solo presenta los valores absolutos de cada métrica en ambos
estados, sino que también calcula y muestra el cambio relativo, indicando explícitamente
si el cambio representa una mejora o degradación del rendimiento. Por ejemplo, si el AUC
aumentó de 0.85 a 0.87, el comando mostraría no solo ambos valores sino también el
incremento de 0.02 o 2.35%, facilitando la evaluación rápida del impacto del
experimento.

El comando `dvc plots diff` genera visualizaciones comparativas interactivas. Para
métricas como curvas de precisión-recuperación, el comando produce un gráfico HTML donde
la curva correspondiente al último commit aparece típicamente en azul, mientras que la
curva del estado actual del directorio de trabajo se muestra en naranja o otro color
contrastante. Esta visualización permite evaluar cualitativamente cómo ha cambiado el
comportamiento del modelo a través de diferentes puntos de operación, revelando
trade-offs que no son evidentes en métricas escalares agregadas.

Un aspecto importante de esta metodología es que el comando `git commit` constituye el
mecanismo fundamental para guardar experimentos en el historial. Cada commit preserva no
solo el código y los parámetros, sino también, mediante DVC, las versiones exactas de
los datos y modelos utilizados. Este sistema proporciona trazabilidad completa,
permitiendo recuperar y reproducir cualquier experimento histórico. DVC está
desarrollando mecanismos para permitir commits más ligeros que se almacenan localmente
en una caché experimental sin necesidad de saturar el historial de Git, pero el modelo
fundamental de versionado mediante commits Git permanece como la práctica estándar
actual.

## Acceso y uso compartido de archivos

### Contexto y motivación

Este módulo aborda una problemática frecuente en el despliegue y uso de modelos de
Machine Learning: cómo acceder a artefactos rastreados por DVC, específicamente datos o
modelos entrenados, en diferentes contextos de uso sin necesidad de clonar completamente
el repositorio Git ni configurar todo el entorno de desarrollo. Esta capacidad resulta
particularmente valiosa en entornos de producción, notebooks compartidos, o scripts de
análisis exploratorio donde se requiere acceso rápido a versiones específicas de modelos
o datos.

### Descubrimiento de artefactos disponibles

Antes de acceder a archivos específicos, resulta útil conocer qué artefactos están
disponibles en un proyecto DVC. El comando `dvc list <dirección_repo_git>` proporciona
esta funcionalidad, mostrando todos los archivos rastreados por DVC asociados con el
repositorio Git especificado. La dirección del repositorio puede ser una URL de GitHub,
GitLab, o cualquier servicio de hosting de Git compatible.

Los resultados del comando listan tanto los archivos de datos reales como sus archivos
de metadatos correspondientes. Por ejemplo, la salida podría mostrar `data.xml` (el
archivo de datos de 36 MB) junto con `data.xml.dvc` (su archivo de metadatos). Esta
visualización permite a los usuarios identificar exactamente qué artefactos están
disponibles para descarga antes de decidir qué método de acceso utilizar.

### Métodos de obtención y sus características distintivas

DVC proporciona tres métodos principales para obtener datos o modelos, cada uno
optimizado para diferentes casos de uso y con diferentes implicaciones en términos de
rastreo de procedencia y gestión de versiones.

#### DVC Get (descarga simple sin seguimiento)

El comando `dvc get <repo_url> <ruta_archivo>` descarga una copia del artefacto
especificado directamente al directorio de trabajo actual. Este método funciona de
manera análoga a descargar un archivo mediante `wget` o `curl`, pero con la ventaja de
que DVC maneja automáticamente la autenticación con el almacenamiento remoto y la
localización del archivo correcto basándose en los metadatos del repositorio.

La característica distintiva de este método es que no rastrea la fuente del archivo
descargado. Una vez completada la descarga, no existe ninguna conexión formal entre el
archivo local y su origen en el proyecto DVC. Esta aproximación resulta útil cuando se
necesita simplemente una copia del archivo para uso inmediato y no existe intención de
mantenerlo sincronizado con actualizaciones futuras. Casos de uso típicos incluyen
descargar un modelo pre-entrenado para análisis exploratorio de una sola vez, o extraer
un conjunto de datos para un proyecto completamente independiente.

#### DVC Import (descarga con seguimiento de procedencia)

El comando `dvc import <repo_url> <ruta_archivo>` ofrece una funcionalidad más
sofisticada. Además de descargar el archivo solicitado, crea automáticamente un archivo
`.dvc` en el espacio de trabajo local. Este archivo de metadatos es crucial porque
establece una conexión formal con el proyecto fuente, registrando tanto la URL de origen
como el hash criptográfico de la versión específica descargada.

Esta conexión persistente habilita funcionalidades avanzadas de seguimiento. El comando
`dvc update` puede ejecutarse posteriormente para verificar si existe una versión más
reciente del archivo en el proyecto fuente. Si el archivo original ha sido actualizado
en el repositorio remoto, `dvc update` puede descargar automáticamente la nueva versión,
manteniendo sincronizado el archivo local con su fuente. Este método resulta ideal para
situaciones donde se consume un modelo o dataset producido por otro equipo, y se desea
recibir automáticamente mejoras o correcciones cuando se publiquen nuevas versiones. El
archivo `.dvc` generado debe versionarse con Git en el proyecto consumidor,
estableciendo un registro formal de la dependencia externa.

#### DVC Python API (acceso programático efímero)

La API de Python de DVC proporciona el método más flexible para integración en código.
Mediante `import dvc.api` seguido de `with dvc.api.open(...)`, se puede cargar un
artefacto directamente en la memoria del proceso Python sin descargarlo permanentemente
al sistema de archivos. El archivo existe exclusivamente en memoria durante la ejecución
del script y se descarta automáticamente cuando el contexto `with` finaliza.

Esta aproximación resulta ideal para entornos de producción o contenedores de inferencia
donde no se desea o no se puede mantener copias locales persistentes de modelos. Por
ejemplo, un servicio web de inferencia puede utilizar `dvc.api.open()` en su código de
inicialización para cargar el modelo entrenado directamente desde el almacenamiento
remoto DVC, utilizar el modelo para predicciones durante el ciclo de vida del proceso, y
descartar la copia en memoria cuando el servicio se detiene. Esta estrategia minimiza el
uso de almacenamiento local, simplifica la gestión de dependencias y facilita la
actualización de modelos: simplemente se reinicia el servicio para que cargue la versión
más reciente disponible en el almacenamiento remoto.

### Acceso a versiones específicas y ramas alternativas

Los tres métodos descritos admiten especificación de revisiones concretas o ramas
alternativas del repositorio fuente. Mediante argumentos adicionales, se puede solicitar
una versión histórica específica de un archivo (referenciando su commit SHA de Git) o
acceder a archivos que existen en ramas experimentales que no han sido fusionadas a la
rama principal. Esta flexibilidad permite escenarios avanzados como comparar el
rendimiento de diferentes versiones de un modelo, o implementar sistemas A/B testing
donde diferentes instancias de un servicio utilizan variantes del modelo desde ramas
distintas del proyecto fuente.

## Prácticas con DVC

## Integración continua, gestión de datos y modelado

Esta guía presenta una introducción completa a las prácticas de MLOps (Machine Learning
Operations), enfocándose en la automatización de flujos de trabajo mediante herramientas
de integración continua, gestión de datos y versionado de modelos. El contenido se
estructura en cinco tutoriales progresivos que cubren desde conceptos fundamentales
hasta técnicas avanzadas de implementación.

## Integración continua

### Objetivo y contexto

Este tutorial introduce el concepto de Integración Continua (CI) en el contexto
específico de proyectos de Machine Learning y Data Science. La Integración Continua es
una práctica originaria del ámbito DevOps que establece un puente entre las
modificaciones en el código fuente y la obtención de retroalimentación inmediata
mediante pruebas automatizadas. En el contexto de ML, este enfoque permite evaluar de
manera sistemática cómo cada cambio en el código afecta al rendimiento y comportamiento
del modelo final.

La implementación práctica se realiza mediante **GitHub Actions**, un sistema de
automatización que permite a GitHub ejecutar tareas de forma autónoma cada vez que se
detectan cambios en el repositorio. Complementariamente se utiliza **CML (Continuous
Machine Learning)**, un proyecto específicamente diseñado para incorporar los principios
de Integración Continua en el ámbito de la ciencia de datos y el aprendizaje automático.

### Conceptos fundamentales

La arquitectura de CI para ML se basa en varios componentes interrelacionados. El
**Runner** constituye una máquina administrada por GitHub que ejecuta los flujos de
trabajo definidos por el usuario. Estos flujos de trabajo, conocidos como **workflows**,
especifican las secuencias de operaciones que deben realizarse automáticamente ante
determinados eventos.

Un aspecto metodológico esencial es el **Git Flow**, una práctica de ramificación que
complementa naturalmente la CI. Bajo este esquema, toda experimentación se desarrolla en
ramas (branches) independientes, mientras que la rama principal se reserva
exclusivamente para código considerado listo para producción. Esta separación permite
mantener la estabilidad del código productivo mientras se experimentan múltiples
variantes del modelo de forma paralela.

### Configuración e implementación práctica

La implementación comienza con un proyecto de referencia que utiliza un conjunto de
datos sobre calidad de vinos. El script de entrenamiento (`train.py`) implementa un
modelo Random Forest Regressor que genera como salidas un archivo de métricas
(`metrics.txt`) conteniendo el coeficiente de determinación R-cuadrado, junto con dos
visualizaciones: un gráfico de importancia de características y un análisis de residuos.

El primer paso técnico consiste en crear la estructura de directorios
`.github/workflows/` dentro del repositorio, donde se alojará el archivo de
configuración YAML (por ejemplo, `cml.yaml`). Este archivo define el nombre descriptivo
del workflow, especifica el disparador (trigger) que activará su ejecución—típicamente
cada evento `push` al repositorio—y establece el entorno de ejecución, incluyendo el
sistema operativo y, opcionalmente, un contenedor Docker. En este caso se recomienda
utilizar un contenedor oficial de CML, aunque el sistema admite contenedores
personalizados según las necesidades específicas del proyecto.

Los pasos de ejecución (jobs) se estructuran secuencialmente. Primero se instalan las
dependencias mediante `pip install requirements.txt`, posteriormente se ejecuta el
script de entrenamiento con `python train.py`, y finalmente se pueden visualizar las
métricas generadas mediante comandos como `cat metrics.txt`.

### Mejora del sistema de reportes con CML

La funcionalidad básica se extiende significativamente al integrar capacidades avanzadas
de CML para la generación de reportes visuales. El objetivo es presentar tanto las
métricas numéricas como las visualizaciones gráficas directamente en el contexto del
Pull Request (PR), facilitando así la revisión y toma de decisiones.

Para implementar esta funcionalidad, se modifica el workflow para crear un archivo de
reporte en formato Markdown (`report.md`). Las métricas, que inicialmente se imprimían
en la terminal, se redirigen a este archivo. Las visualizaciones se incorporan mediante
funciones especializadas de CML. El comando
`cml publish feature_importance.png --md >> report.md` procesa y publica el gráfico de
importancia de características, mientras que
`cml publish residuals.png --md -H dataviz >> report.md` realiza la misma operación con
el análisis de residuos, añadiendo un encabezado específico. Finalmente,
`cml send-comment report.md` envía el reporte completo como comentario al Pull Request
correspondiente.

El resultado de esta configuración es un flujo de trabajo completamente automatizado:
cuando un desarrollador realiza un push y abre un PR, el runner ejecuta automáticamente
el workflow definido, CML procesa las salidas del modelo y publica un informe
comprehensivo que incluye métricas cuantitativas y visualizaciones cualitativas. Esta
información presentada directamente en el contexto del PR facilita enormemente la
evaluación del impacto de los cambios propuestos y permite tomar decisiones informadas
sobre la fusión del código.

## Gestión de datos de gran volumen

### Problemática y solución

Este tutorial aborda una limitación fundamental de Git: su inadecuación para gestionar
conjuntos de datos de gran tamaño, típicamente aquellos en el rango de 100 MB a 100 GB.
Aunque Git es excepcionalmente eficiente para el versionado de código fuente, su
arquitectura interna no está optimizada para archivos binarios grandes, lo que resulta
en repositorios excesivamente pesados y tiempos de clonado prolongados.

**DVC (Data Version Control)** surge como solución complementaria a Git. Mientras Git
continúa gestionando el código fuente y los archivos de configuración, DVC se
especializa en el seguimiento y versionado de datos, almacenándolos en ubicaciones
externas denominadas **DVC Remotes**. Estos almacenamientos remotos pueden adoptar
diversas formas: desde sistemas de almacenamiento local hasta buckets de servicios en la
nube como Amazon S3 o Google Cloud Storage, e incluso carpetas en servicios como Google
Drive.

La arquitectura de DVC proporciona una capa de abstracción de alto nivel que simplifica
operaciones complejas. Los usuarios interactúan con comandos intuitivos como `dvc pull`
y `dvc push`, análogos a los comandos Git correspondientes, sin necesidad de gestionar
manualmente URLs complejas o credenciales de acceso específicas para cada servicio de
almacenamiento.

### Implementación del flujo de trabajo

La configuración inicial requiere la inicialización de DVC dentro del repositorio
mediante el comando `dvc init`. Posteriormente se establece la conexión con el
almacenamiento remoto. Tomando como ejemplo Google Drive, el comando
`dvc remote add -d my_remote <ID_de_Google_Drive>` configura el almacenamiento remoto
predeterminado, donde el ID corresponde al identificador único de la carpeta de Drive
destinada a alojar los datos.

Una vez configurado el entorno, el flujo de trabajo se estructura en dos canales
paralelos. Cuando se genera o modifica un conjunto de datos de gran tamaño, el comando
`dvc add data.csv` añade el archivo al sistema de seguimiento de DVC. Esta operación no
incorpora el archivo de datos al repositorio Git; en su lugar, DVC crea un archivo
metadatos con extensión `.dvc` (en este caso, `data.csv.dvc`) que contiene información
sobre el archivo original, incluyendo su hash, tamaño y ubicación en el almacenamiento
remoto.

Este archivo de metadatos, al ser de tamaño reducido y contener únicamente texto, se
gestiona perfectamente con Git mediante los comandos habituales: `git add data.csv.dvc`,
`git commit` y `git push`. Paralelamente, el conjunto de datos real se transfiere al
almacenamiento remoto mediante `dvc push`. En el caso de servicios como Google Drive, la
primera ejecución requerirá un proceso de autenticación mediante navegador para conceder
los permisos necesarios.

### Recuperación de datos en entornos distribuidos

La verdadera utilidad de este sistema se manifiesta cuando un colaborador necesita
trabajar con el proyecto desde una máquina diferente. Tras clonar el repositorio Git de
manera convencional, el usuario no dispondrá inicialmente de los conjuntos de datos
grandes—solo tendrá acceso a los archivos `.dvc` que los referencian. La ejecución de
`dvc pull` activa el mecanismo de sincronización: DVC analiza los archivos de metadatos
presentes en el repositorio, identifica los datos correspondientes en el almacenamiento
remoto y los descarga automáticamente a la ubicación local esperada.

Este sistema garantiza que el repositorio Git mantenga un tamaño manejable mientras
todos los colaboradores pueden acceder de forma transparente a los datos necesarios.
Además, DVC preserva el histórico completo de versiones de los datos, permitiendo
recuperar cualquier versión anterior de un conjunto de datos simplemente cambiando a un
commit específico de Git y ejecutando `dvc checkout`.

## Seguimiento y comparación automatizada de modelos

### Objetivo y metodología

Este tutorial establece un sistema para comparar automáticamente el rendimiento de
modelos a través de diferentes ramas de Git, permitiendo evaluar objetivamente si un
experimento en desarrollo supera al modelo actualmente en producción. La comparación se
fundamenta en el uso de **DVC Pipelines**, un mecanismo que encadena programáticamente
todas las etapas del proyecto, desde la adquisición de datos hasta el entrenamiento y
evaluación del modelo.

La implementación de pipelines formales garantiza reproducibilidad: las métricas
generadas por diferentes versiones del modelo provienen exactamente del mismo proceso
estandarizado, eliminando variables confusoras que podrían surgir de diferencias en el
preprocesamiento de datos o en los procedimientos de evaluación. Esta estandarización es
esencial para realizar comparaciones válidas y significativas entre experimentos.

### Definición y estructura de pipelines

Un pipeline DVC se estructura como una secuencia de etapas (stages), cada una con
dependencias de entrada claramente definidas y salidas específicas. Las etapas se
definen mediante comandos `dvc run` o mediante la edición directa del archivo de
configuración `dvc.yaml`.

La estructura típica de un pipeline de ML comprende tres etapas fundamentales. La etapa
de **obtención de datos** (`get data`) especifica como dependencia el script responsable
de la adquisición (por ejemplo, `getdata.py`) y declara como salida el archivo de datos
crudos (`data_raw.csv`). La etapa de **procesamiento** (`process`) depende tanto del
script de procesamiento (`processdata.py`) como de los datos crudos generados en la
etapa anterior, produciendo como salida los datos preprocesados (`data_processed.csv`).
Finalmente, la etapa de **entrenamiento** (`train`) requiere el script de entrenamiento
y los datos procesados, generando como salidas las visualizaciones del modelo y,
crucialmente, un archivo de métricas especial.

La definición de la etapa de entrenamiento incorpora consideraciones específicas para
entornos de CI. El argumento `--metrics metrics.json` designa el archivo de salida como
un archivo de métricas, permitiendo a DVC realizar operaciones especializadas de
comparación. El flag adicional `--cache false` resulta esencial en contextos de CI:
evita que el pipeline dependa de cachés locales que podrían no estar disponibles o ser
inconsistentes entre diferentes runners de GitHub Actions.

### Integración con GitHub Actions

La reproducción del pipeline se realiza mediante el comando `dvc repro`, que analiza el
grafo de dependencias, determina qué etapas requieren actualización y las ejecuta en el
orden correcto. Una vez validado el pipeline, se confirman los cambios tanto en el
archivo de configuración DVC como en las definiciones de etapas mediante los comandos
Git habituales.

El workflow de GitHub Actions para comparación de modelos (`train.yaml`) implementa una
secuencia específica de pasos. Tras la instalación de dependencias, ejecuta `dvc repro`
para regenerar todas las salidas del modelo. Posteriormente realiza `git fetch` para
asegurar que el runner tiene acceso al historial completo del repositorio, incluyendo la
rama de referencia (típicamente `master` o `main`).

El componente central es el comando
`dvc metrics diff --show-markdown master >> report.md`, que realiza una comparación
cuantitativa entre las métricas del experimento actual y las de la rama principal. DVC
analiza los archivos de métricas de ambas ramas, calcula las diferencias y formatea los
resultados como una tabla Markdown. Esta tabla presenta no solo los valores absolutos de
cada métrica, sino también el cambio relativo, facilitando la identificación rápida de
mejoras o degradaciones en el rendimiento del modelo. Las visualizaciones se incorporan
mediante `cml publish`, y el reporte completo se envía al Pull Request con
`cml send-comment report.md`.

El resultado final es un sistema de revisión altamente informado: cuando un científico
de datos experimenta con un nuevo algoritmo—por ejemplo, sustituyendo un modelo
existente por Quadratic Discriminant Analysis—y abre un PR, el reporte automatizado
presenta una tabla comparativa que muestra exactamente cómo cada métrica de rendimiento
ha cambiado respecto a la implementación actual en producción. Esta información objetiva
y cuantitativa sustenta decisiones fundamentadas sobre la fusión de cambios.

## Hardware personalizado con runners auto-alojados

### Motivación y arquitectura

Los runners predeterminados de GitHub Actions operan sobre máquinas basadas en CPU que,
si bien son adecuadas para muchos workflows, resultan insuficientes para tareas de ML
que requieren aceleración por GPU. Este tutorial presenta la configuración de **runners
auto-alojados** (self-hosted runners): máquinas de propiedad del usuario que ejecutan
los workflows, permitiendo aprovechar hardware especializado como GPUs dedicadas.

Un runner auto-alojado puede ser cualquier máquina con recursos computacionales
suficientes, desde estaciones de trabajo locales hasta instancias de computación en la
nube como AWS EC2 equipadas con GPUs. La arquitectura mantiene la coordinación y
orquestación en GitHub mientras la ejecución real ocurre en el hardware propio del
usuario.

### Requisitos previos y configuración de seguridad

La implementación requiere tres componentes técnicos en la máquina designada como
runner: una GPU compatible con NVIDIA, Docker instalado y configurado correctamente, y
NVIDIA Docker (nvidia-docker2), que permite a los contenedores Docker acceder a las
capacidades de la GPU. La verificación del hardware se realiza mediante `nvidia-smi`,
que debe mostrar información detallada de la GPU disponible.

El aspecto de seguridad se gestiona mediante un **Token de Acceso Personal (PAT)**
generado desde la configuración de desarrollador de GitHub. Este token debe poseer los
permisos de `repo` (acceso completo al repositorio) y `workflow` (capacidad de modificar
workflows), estableciendo la autorización necesaria para que el runner se comunique con
GitHub y reciba instrucciones de workflows.

Las **etiquetas de runner** (runner labels) constituyen el mecanismo de direccionamiento
que especifica qué máquina debe ejecutar cada workflow. Al asignar etiquetas específicas
al runner auto-alojado (por ejemplo, `cml` y `gpu`), los workflows pueden solicitar
explícitamente estos recursos especializados.

### Implementación técnica del runner

La ejecución del runner se realiza mediante un comando Docker que inicia el servicio en
segundo plano. El comando incorpora varios parámetros de configuración críticos mediante
variables de entorno. El argumento `--gpus all` expone todas las GPUs de la máquina host
al contenedor Docker. La variable `RUNNER_IDLE_TIMEOUT` especifica el tiempo de
inactividad que el runner tolerará antes de apagarse automáticamente, optimizando el uso
de recursos. `RUNNER_LABELS` define las etiquetas que identificarán este runner,
`RUNNER_REPO` especifica la URL del repositorio de GitHub al que se asocia el runner, y
`REPO_TOKEN` proporciona el PAT generado previamente para autenticación.

El modo `--detach` permite que el contenedor continúe ejecutándose en segundo plano,
manteniendo el runner activo y listo para recibir trabajos incluso después de cerrar la
sesión de terminal.

### Configuración de workflows para GPU

La adaptación de un workflow para utilizar el runner personalizado requiere
modificaciones específicas en el archivo YAML de definición. La sección `runs-on`, que
tradicionalmente especificaría un runner administrado por GitHub (como `ubuntu-latest`),
se reemplaza por `self-hosted`. Adicionalmente, se añade la especificación de etiquetas
mediante `tags: [cml, gpu]`, asegurando que el workflow solo se ejecute en runners que
posean estas etiquetas específicas.

Los pasos del workflow pueden entonces aprovechar directamente la GPU disponible. Un
workflow de prueba típico ejecuta `nvidia-smi` para verificar la disponibilidad del
hardware y utiliza CML para publicar la salida en un Pull Request. El uso de
delimitadores de bloque de código en el reporte Markdown preserva el formato de la
salida de `nvidia-smi`, manteniendo su legibilidad.

Cuando se activa el workflow, GitHub identifica mediante las etiquetas que se requiere
un runner con GPU, enruta el trabajo al runner auto-alojado correspondiente, y la
ejecución ocurre en el hardware personalizado. El reporte resultante confirma no solo la
ejecución exitosa sino también las especificaciones exactas del hardware utilizado,
proporcionando documentación completa del entorno de ejecución.

## Pruebas automatizadas

### Filosofía y flujo de trabajo

Este tutorial introduce el concepto de pruebas automatizadas específicamente adaptadas
al contexto de Machine Learning. El flujo de trabajo típico en ciencia de datos
involucra desarrollo y experimentación local, frecuentemente en entornos interactivos
como Jupyter notebooks, seguido de la selección del "modelo favorito" para su
integración en el repositorio principal. Sin embargo, el éxito local no garantiza
reproducibilidad en otros entornos.

El valor fundamental de ejecutar pruebas en un entorno de CI radica en la
estandarización y documentación. El entorno de CI proporciona un registro histórico
inmutable de las condiciones exactas—sistema operativo, versiones de paquetes,
configuración de hardware, contenedor Docker—que permitieron la ejecución exitosa del
modelo. Esta documentación automática resulta invaluable cuando surgen problemas de
reproducibilidad semanas o meses después del desarrollo inicial.

### Implementación de pruebas básicas

La prueba automatizada más fundamental para un modelo de ML no evalúa necesariamente su
calidad estadística, sino su capacidad operativa básica: verificar que el modelo carga
correctamente, puede procesar datos de entrada y completar el proceso de inferencia sin
errores de ejecución.

La estructura típica comprende dos scripts complementarios. El script de entrenamiento
(`train.py`) genera datos sintéticos apropiados para el problema, ajusta el modelo (por
ejemplo, utilizando Lasso regression) y serializa el modelo entrenado mediante pickle.
El script de prueba (`test.py`) implementa una lógica mínima: deserializa el modelo
guardado, genera un conjunto de datos sintéticos con la estructura esperada y ejecuta la
inferencia. La ausencia de excepciones durante este proceso constituye el criterio de
éxito.

### Configuración del workflow de pruebas

El workflow de GitHub Actions para pruebas se configura con un disparador en cada
`push`, asegurando que toda modificación al código sea inmediatamente validada. Los
pasos del workflow son deliberadamente simples: instalación de dependencias mediante
`pip install requirements.txt` y ejecución del script de prueba con `python test.py`.

### Ciclo de desarrollo, debugging y validación

El verdadero valor de las pruebas automatizadas se manifiesta durante el ciclo iterativo
de desarrollo. Cuando un científico de datos entrena un nuevo modelo localmente—por
ejemplo, cambiando de un algoritmo anterior a Lasso—y realiza commit y push a una rama
experimental, el workflow de prueba se activa automáticamente.

Los fallos en las pruebas proporcionan retroalimentación inmediata sobre problemas de
portabilidad. Un primer tipo de fallo común surge de dependencias faltantes: si el test
falla porque el módulo `scikit-learn` no está disponible en el entorno de CI, la
solución requiere actualizar el archivo `requirements.txt` para incluir la dependencia
omitida. Un segundo tipo de fallo se relaciona con incompatibilidades de datos: si el
test espera datos con 11 características pero el modelo fue entrenado con 10, el error
indica una inconsistencia entre el código de entrenamiento y el de prueba. La resolución
requiere modificar el script de entrenamiento, reentrenar el modelo con la estructura de
datos correcta y realizar push de la versión actualizada.

La validación exitosa se manifiesta mediante el "check verde" en el Pull Request: una
indicación visual de que todas las pruebas pasaron satisfactoriamente. Este check no
solo confirma que el código funciona en el entorno estandarizado de CI, sino que también
establece un punto de referencia documentado en el historial del proyecto. Cualquier
persona que revise el PR o examine el historial en el futuro puede consultar los logs
del workflow para comprender exactamente bajo qué condiciones el modelo funcionó
correctamente, facilitando tanto la depuración de problemas futuros como la reproducción
de resultados.

## Validación de datos y de modelos

Las pruebas descritas verifican que el código y el modelo funcionan en un entorno
reproducible, pero no dicen nada sobre la calidad de los datos que alimentan el
_pipeline_ ni sobre el comportamiento del modelo en los distintos segmentos de la
población. Ambos aspectos se cubren con dos familias de comprobaciones adicionales que
conviene automatizar en el mismo _pipeline_ de integración continua.

### Contratos y calidad de los datos

Un **contrato de datos** (_data contract_) es una prueba que verifica que los datos
cumplen el esquema esperado en cuanto a nombres de atributos, tipos de valores y
restricciones admitidas. Actúa como una promesa formal entre quien produce los datos y
quien los consume, de modo que un cambio en el origen se detecta antes de propagarse al
entrenamiento.

Sobre esa base se añaden las pruebas de calidad, que validan la exactitud, la
completitud, la consistencia, la representatividad y la actualidad de los datos
empleados tanto en el entrenamiento como en la inferencia. Un tercer grupo son las
pruebas de privacidad, que comprueban que los conjuntos entregados al modelo no
contienen información personal identificable, como nombres, números de identificación o
direcciones.

!!! note "Versionado integral"

    La reproducibilidad exige versionar más que el código. Alcanza a los conjuntos de
    datos, los parámetros del modelo, los archivos de configuración e incluso las
    semillas del generador de números aleatorios utilizadas durante el entrenamiento.

### Pruebas de modelo y métricas estratificadas

Las pruebas de modelo verifican las métricas globales, la invarianza frente a
dimensiones irrelevantes y las expectativas direccionales, es decir, que un cambio
conocido en una variable de entrada mueva la predicción en el sentido esperado. A ellas
se añaden las **métricas estratificadas**, que segmentan el conjunto de validación por
dimensiones de interés y calculan las métricas en cada segmento en lugar de una única
cifra agregada.

???+ example "Estratificación oculta"

    La precisión global de un modelo puede mejorar un uno por ciento mientras la precisión
    correspondiente a un país concreto cae cincuenta puntos. Una única métrica agregada
    oculta ese comportamiento, mientras que el cálculo por segmentos, ya sea por variable
    objetivo, por región o por cualquier otra dimensión relevante, lo hace visible antes
    de que el modelo llegue a producción.

Cuando el entrenamiento completo resulta costoso, un _smoke test_ ejecuta el _pipeline_
íntegro con un conjunto de datos mínimo, del orden de unas pocas decenas de muestras, y
devuelve el resultado en uno o dos minutos. Esta comprobación no evalúa la calidad
estadística del modelo, sino que detecta errores de integración antes de invertir horas
de cómputo, lo que evita el patrón de lanzar un entrenamiento completo únicamente para
comprobar si el código se ejecuta.

## MLflow
