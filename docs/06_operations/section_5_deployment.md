---
authors: Daniel Bazo Correa
description:
    APIs de inferencia, estrategias de despliegue de modelos, ONNX Runtime y
    optimización para distintos entornos de hardware.
title: Despliegue y distribución
---

!!! warning

    El contenido de esta página no ha sido revisado ni corregido, por lo que puede
    estar incompleto, contener errores o presentar información desactualizada. Además,
    es posible que esté desordenado, carezca de una estructura clara o incluya notas
    copiadas directamente.

Este capítulo aborda el despliegue de modelos de aprendizaje automático mediante APIs,
las estrategias que permiten publicar una versión nueva con el mínimo riesgo, la
optimización con ONNX Runtime y la distribución eficiente de modelos en distintos
entornos de hardware.

## Bibliografía

- ONNX. (s.f.). _ONNX - Open Neural Network Exchange_. <https://onnx.ai/>
- ONNX Runtime. (s.f.). _ONNX Runtime_. <https://onnxruntime.ai/>
- Deeplizard. (2022). _Everything You Want to Know About ONNX_ \[Vídeo\]. YouTube.
  <https://www.youtube.com/watch?v=cK5AyawZSUI>
- Wolf, A. y Fursov, I. (s.f.). _ML in Production: From Data Scientist to ML Engineer_
  \[Curso\]. Udemy. <https://www.udemy.com/course/ml-in-production/>

## APIs de inferencia

En el contexto de MLOps, los modelos entrenados se exponen habitualmente como servicios
independientes a través de APIs. Este enfoque sigue la arquitectura de microservicios,
donde cada componente (entrenamiento, inferencia, procesamiento de datos) se desarrolla
y despliega de forma independiente, lo que mejora la escalabilidad, la flexibilidad y el
mantenimiento.

Las APIs pueden operar de forma **síncrona** (el cliente espera la respuesta antes de
continuar) o **asíncrona** (el cliente envía la petición y recibe la respuesta
posteriormente). Las APIs asíncronas resultan especialmente útiles cuando el tiempo de
inferencia es elevado o cuando se necesita gestionar múltiples peticiones concurrentes.

Las operaciones fundamentales de una API REST siguen el patrón CRUD:

| Operación  | Método HTTP | Descripción           |
| :--------- | :---------- | :-------------------- |
| Crear      | `POST`      | Crea un nuevo recurso |
| Leer       | `GET`       | Obtiene un recurso    |
| Actualizar | `PUT`       | Modifica un recurso   |
| Eliminar   | `DELETE`    | Elimina un recurso    |

## Estrategias de despliegue

Publicar una versión nueva de un modelo no consiste en sustituir el artefacto anterior
de forma abrupta. Existen varias estrategias que reducen el riesgo de la operación al
limitar el número de usuarios expuestos y al mantener siempre una vía de retorno.

El **_gradual ramp-up_** incrementa de forma progresiva el tráfico dirigido al nuevo
modelo, lo que permite observar su comportamiento con carga real y detener el proceso
ante cualquier anomalía. El **_rollback_** es el mecanismo complementario y consiste en
revertir al modelo anterior cuando el nuevo no cumple las expectativas. El **_canary
deployment_** asigna al principio un porcentaje reducido del tráfico a la versión nueva
y lo amplía solo si demuestra ser estable. El **_blue-green deployment_** mantiene dos
entornos paralelos, uno activo y otro en preparación, e intercambia su papel cuando la
versión nueva está lista, lo que hace que la vuelta atrás sea inmediata.

Junto a estas técnicas, el **modo sombra** (_shadow mode_) ejecuta el modelo nuevo con
tráfico real y registra sus predicciones sin exponerlas al usuario, lo que permite
compararlas con las del modelo en producción sin ningún riesgo. Las **pruebas A/B** van
un paso más allá y miden el impacto real sobre las métricas de producto comparando
grupos de usuarios expuestos a versiones distintas.

| Estrategia          | Exposición inicial           | Vuelta atrás                        |
| ------------------- | ---------------------------- | ----------------------------------- |
| _Gradual ramp-up_   | Porcentaje creciente         | Reducción del tráfico dirigido.     |
| _Canary deployment_ | Porcentaje reducido y fijo   | Retirada de la versión candidata.   |
| _Blue-green_        | Ninguna hasta el intercambio | Intercambio inverso de entornos.    |
| _Shadow mode_       | Ninguna, solo registro       | No aplica, no hay tráfico expuesto. |

Estas estrategias solo resultan efectivas si el despliegue está automatizado. Una vez
finalizado el entrenamiento, el _pipeline_ descrito en el capítulo de
[CI/CD](section_2_ci_cd.md) ejecuta las pruebas de calidad del modelo, empaqueta el
artefacto, lo publica en un entorno de preproducción y lanza las pruebas posteriores al
despliegue sin intervención manual.

!!! note "Diseño para minimizar el coste del fallo"

    Un modelo comete errores por definición, de modo que el diseño del producto debe
    limitar el impacto de una predicción incorrecta. Entre las opciones habituales se
    encuentran la incorporación de una persona en el bucle de decisión para los escenarios
    de riesgo elevado, el uso de métricas ponderadas por coste que hagan visibles los
    errores más caros y la configuración de umbrales conservadores que señalen los casos
    dudosos en lugar de resolverlos automáticamente.

## Motivación

<figure markdown="span">
  ![Logo de ONNX](../assets/img/docs/logos/onnx-logo.png)
  <figcaption>Logo de ONNX</figcaption>
</figure>

El ecosistema del aprendizaje profundo se caracteriza por una gran fragmentación en los
frameworks utilizados para el desarrollo de modelos, como TensorFlow, Keras, PyTorch,
Caffe y MXNet, además de versiones privadas de distintas empresas. La migración entre
estos frameworks no siempre es sencilla, especialmente considerando la evolución de las
tendencias tecnológicas, lo que puede llevar a la obsolescencia o la falta de soporte de
determinadas herramientas.

Además, el despliegue de modelos de aprendizaje profundo está condicionado por la
compatibilidad con el hardware disponible. Por ejemplo, las tarjetas gráficas de NVIDIA
utilizan CUDA, mientras que Intel ofrece oneAPI. Algunos proveedores de nube, como GCP,
permiten el uso de hardware especializado, como las TPUs.

Existen diversas herramientas para la optimización de modelos en fase de inferencia.
Entre ellas, [TensorRT](https://developer.nvidia.com/tensorrt) optimiza modelos para su
ejecución en GPU, [OpenVINO](https://docs.openvino.ai/2024/index.html) mejora la
inferencia en procesadores, GPUs y NPUs de Intel, y
[JetPack SDK](https://developer.nvidia.com/embedded/jetpack) está diseñado para
dispositivos edge de NVIDIA, como las Jetson. Para entornos embebidos, TensorFlow
proporciona [LiteRT](https://ai.google.dev/edge/litert), lo que antes era Tensorflow
Lite. Sin embargo, la interoperabilidad sigue siendo un desafío, ya que comprender y
utilizar cada una de estas herramientas puede ser costoso y demandar una considerable
inversión de tiempo.

**ONNX (Open Neural Network Exchange)** busca resolver estos problemas facilitando la
interoperabilidad entre modelos de aprendizaje automático, independientemente del
framework en el que hayan sido desarrollados. ONNX proporciona una representación
intermedia de los modelos, reduciendo la cantidad de dependencias necesarias para su
ejecución. Al exportar un modelo a ONNX, no es necesario importar bibliotecas
específicas como PyTorch o TensorFlow, lo que simplifica el despliegue y optimización en
distintos entornos de hardware.

ONNX cuenta con el respaldo de la Fundación Linux y un sólido ecosistema de soporte por
parte de grandes proveedores de la nube como AWS, Azure y GCP. No solo es compatible con
frameworks de aprendizaje profundo, sino también con herramientas de aprendizaje
automático clásico, a través de ONNX-ML, que facilita la integración con bibliotecas
como Scikit-Learn.

Además, ONNX se integra con **Azure**, permitiendo la creación de pipelines para la
gestión de datasets, entrenamiento de modelos y descarga de modelos entrenados.
Posteriormente, estos modelos pueden ser desplegados en dispositivos edge o en la nube
mediante técnicas como la contenerización con Docker.

## Funcionamiento

ONNX representa los modelos mediante un grafo de computación, en el cual cada nodo
corresponde a una operación matemática y cada arista define la relación entre dichas
operaciones. Este formato es compatible con diversos tipos de datos estándar, incluyendo
tensores y tipos no tensoriales, como enteros, flotantes y booleanos, entre otros.

El archivo de modelo en ONNX contiene información esencial, como la versión del modelo,
metadatos y un grafo de flujo de datos de computación acíclico. Dentro del grafo, se
especifican las entradas y salidas del modelo, la lista de nodos de computación y el
nombre del propio grafo. Además, se incluyen definiciones de operadores, parámetros y
tipos de datos utilizados en el modelo.

El estándar ONNX define un conjunto de operadores que permiten mapear las
funcionalidades de los frameworks de alto nivel con su propia representación. Existe una
tabla de operadores compatibles con cada librería, como la que relaciona TensorFlow y
Keras con ONNX, disponible en
[GitHub](https://github.com/onnx/tensorflow-onnx/blob/main/support_status.md). Además,
ONNX permite la creación de operadores personalizados para extender su funcionalidad.

Para facilitar la visualización de los grafos de computación generados, se dispone de la
herramienta [**Netron**](https://netron.app/), que forma parte del ecosistema de ONNX.

### Versionado en ONNX

El versionado en ONNX se estructura en tres niveles:

- **IR Version (Intermediate Representation Version):** Define el formato del archivo y
  la estructura del modelo dentro de ONNX.
- **Opset Version (Operator Set Version):** Indica el conjunto de operadores compatibles
  con el modelo, asegurando compatibilidad con diferentes versiones del framework.
- **Operator Version:** Especifica la versión de cada operador individual dentro del
  conjunto de operadores, lo que permite gestionar cambios en su funcionalidad sin
  afectar la compatibilidad general del modelo.

## ONNX Runtime

ONNX Runtime es un motor de inferencia optimizado para la ejecución eficiente de modelos
en formato ONNX en diversos entornos de hardware, incluyendo la nube y dispositivos
edge. Proporciona una capa de abstracción sobre el hardware utilizado y permite la
integración con bibliotecas de aceleración específicas mediante los **Execution
Providers (EP)**. Soporta completamente la especificación de ONNX, garantizando la
interoperabilidad entre diferentes frameworks y herramientas, y asegura la
compatibilidad retroactiva con modelos creados en versiones anteriores.

Este motor está diseñado para ofrecer alto rendimiento mediante estrategias de
optimización y aceleración, así como una ejecución híbrida que prioriza el uso de
hardware acelerado siempre que esté disponible. En caso de incompatibilidad, el modelo
se ejecuta en la CPU de manera eficiente. Además, ONNX Runtime es una solución portátil
y compatible con múltiples sistemas operativos y plataformas de hardware, permitiendo la
integración con aceleradores personalizados y entornos de ejecución optimizados.

Otro aspecto destacado es su extensibilidad, ya que admite la incorporación de módulos
personalizados para mejorar la funcionalidad y el rendimiento. Gracias a estas
características, ONNX Runtime se posiciona como una solución flexible y eficiente para
la inferencia de modelos en una amplia variedad de entornos y dispositivos.

### Funcionamiento

Para optimizar la ejecución de los modelos, ONNX Runtime realiza una partición del grafo
de computación, dividiéndolo en subgrafos que pueden ejecutarse en diferentes
**Execution Providers (EP)**, lo que permite aprovechar distintas plataformas de
hardware y ejecutar operaciones en paralelo dentro del grafo. Esta optimización se lleva
a cabo en varios niveles:

1. **Partición del grafo:** Se identifican y dividen las secciones del modelo que pueden
   ejecutarse en distintos EP.
2. **Aplicación de transformaciones generales:** Se realizan modificaciones en el grafo
   como inserción de conversiones de tipo (_cast insertion_) o copias de memoria (_mem
   copy insertion_).
3. **Transformaciones generales independientes del EP:** Se aplican optimizaciones que
   no dependen de un hardware específico.
4. **Transformaciones específicas del EP:** Se ajusta el modelo para aprovechar al
   máximo las capacidades de hardware especializadas, como TPU, GPU o FPGA.

Los **Execution Providers (EP)** permiten la integración de bibliotecas específicas de
aceleración de hardware, facilitando la optimización de la inferencia en diversas
plataformas. Además, la interfaz `GetCapability()` asigna nodos o subgrafos del modelo
ONNX a la biblioteca del **Execution Provider** compatible, permitiendo una ejecución
optimizada en distintos tipos de hardware, como CPU, GPU, FPGA y NPU. Más información
sobre los EP está disponible en la
[documentación oficial](https://onnxruntime.ai/docs/execution-providers/).

### Integración y configuración

Los desarrolladores pueden crear e integrar sus propios EPs para ejecutar modelos en
soluciones de aceleración personalizadas. Además, ONNX Runtime permite construir
paquetes con cualquier combinación de EPs, siempre que las bibliotecas necesarias estén
disponibles. Métodos como `get_providers`, `get_provider_options` y `set_providers`
permiten configurar y cambiar los EPs de manera eficiente.

A continuación, se muestra un fragmento de código que inicializa una sesión de
inferencia con un modelo ONNX, estableciendo un orden de prioridad en los EPs
(preferencia por CUDA sobre CPU):

```py linenums="1"
import onnxruntime as ort

session = ort.InferenceSession("modelo.onnx", providers=["CUDAExecutionProvider", "CPUExecutionProvider"])
inputs = {session.get_inputs()[0].name: datos_entrada}
resultados = session.run(None, inputs)
```

También es posible modificar la prioridad de los EPs para utilizar solo el proveedor de
CPU:

```py linenums="1"
session.set_providers(["CPUExecutionProvider"])
```

## Modelos preentrenados

ONNX proporciona un repositorio de modelos preentrenados denominado
[**ONNX Model Zoo**](https://onnx.ai/models/), que incluye modelos de visión
computacional, procesamiento de lenguaje natural (NLP) y audio, entre otros.

## Olive (ONNX LIVE)

Para mejorar aún más la optimización de modelos ONNX, se dispone de
[**Olive (ONNX LIVE)**](https://github.com/microsoft/OLive), una herramienta diseñada
para optimizar modelos ONNX para su ejecución eficiente en la nube o en dispositivos
_edge_. Dado un modelo y un hardware objetivo, Olive selecciona y aplica las técnicas de
optimización más adecuadas para generar un modelo optimizado, teniendo en cuenta
restricciones como precisión y latencia.

Entre sus principales beneficios se encuentran la automatización del proceso de
optimización, eliminando la necesidad de pruebas manuales, y una amplia variedad de
técnicas avanzadas de compresión, ajuste fino (_fine-tuning_) y compilación. Dispone de
una interfaz de línea de comandos (CLI), flujos de trabajo estructurados para gestionar
la transformación y optimización de modelos, y soporte para la compilación de
adaptadores LoRA. Además, ofrece integración con plataformas como **Hugging Face** y
**Azure AI**. Un mecanismo de caché integrado permite mejorar la productividad al
almacenar y reutilizar optimizaciones previas, reduciendo el tiempo de cómputo en
experimentaciones repetitivas.

Una vez que el modelo se encuentra desplegado y atendiendo peticiones, su rendimiento
deja de depender exclusivamente del código y comienza a verse afectado por la evolución
de los datos reales. El capítulo de
[monitorización y observabilidad](section_6_monitoring.md) describe cómo vigilar ese
comportamiento y cuándo conviene reentrenar.
