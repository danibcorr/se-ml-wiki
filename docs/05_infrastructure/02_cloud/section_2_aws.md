---
authors: Daniel Bazo Correa
description:
    Conceptos fundamentales de Amazon Web Services, desde la infraestructura global
    hasta los servicios de cómputo, almacenamiento, redes, bases de datos, analítica,
    seguridad, gobernanza y gestión de costes.
title: Amazon Web Services (AWS)
---

!!! warning

    El contenido de esta página no ha sido revisado ni corregido, por lo que puede
    estar incompleto, contener errores o presentar información desactualizada. Además,
    es posible que esté desordenado, carezca de una estructura clara o incluya notas
    copiadas directamente.

Este capítulo recorre los servicios principales de Amazon Web Services, desde la
infraestructura global y la gestión de identidades hasta los servicios de cómputo,
almacenamiento, bases de datos, analítica y despliegue, y cierra con la seguridad, la
gobernanza del entorno y el control del gasto.

## Bibliografía

- Amazon Web Services. (s.f.). _AWS Cloud Practitioner Essentials_ \[Curso\]. AWS Skill
  Builder.
  <https://skillbuilder.aws/learn/94T2BEN85A/aws-cloud-practitioner-essentials/8D79F3AVR7>
- Amazon Web Services. (s.f.). _AWS Documentation_. <https://docs.aws.amazon.com/>

## Introducción

**Amazon Web Services** (AWS) es una plataforma de servicios en la nube (_cloud_) que
ofrece capacidad de cómputo, almacenamiento, bases de datos, redes y una amplia variedad
de herramientas bajo un modelo de pago por uso (_pay-as-you-go_). En lugar de adquirir y
mantener infraestructura física propia, las organizaciones pueden aprovisionar recursos
de forma inmediata y pagar únicamente por lo que consumen, lo que elimina la necesidad
de grandes inversiones iniciales en _hardware_.

Las principales ventajas de adoptar AWS incluyen la economía de escala, ya que los
costes se reparten entre millones de usuarios, lo que permite ofrecer precios más
competitivos que los de un centro de datos privado. La capacidad de escalar recursos de
forma elástica, tanto al alza como a la baja, garantiza que la infraestructura se adapte
en todo momento a la demanda real. La agilidad que proporciona la nube permite desplegar
nuevos entornos en cuestión de minutos, acelerando los ciclos de desarrollo y
experimentación. Además, el despliegue global resulta inmediato gracias a la extensa red
de centros de datos distribuidos por todo el mundo.

Toda operación dentro de AWS se realiza mediante llamadas a una interfaz de programación
de aplicaciones (API). Ya sea crear una instancia de cómputo, almacenar un archivo o
configurar una red, cada acción se traduce internamente en una petición API. Existen
tres formas principales de interactuar con estas API. La primera es la **AWS Management
Console**, una interfaz web gráfica que permite gestionar los servicios de forma visual
e intuitiva, especialmente útil para tareas de exploración y configuración inicial. La
segunda es la **AWS CLI** (_Command Line Interface_), una herramienta de línea de
comandos que permite automatizar operaciones y ejecutar secuencias de instrucciones de
forma reproducible. AWS proporciona además **AWS CloudShell**, una terminal integrada
directamente en la consola web que incluye la CLI preconfigurada, sin necesidad de
instalación local. La tercera vía son los **AWS SDKs** (_Software Development Kits_),
bibliotecas disponibles para múltiples lenguajes de programación que permiten integrar
los servicios de AWS directamente en el código de las aplicaciones. Un ejemplo destacado
es **Boto3**, el SDK oficial para Python, ampliamente utilizado en proyectos de ciencia
de datos e inteligencia artificial.

## Infraestructura global

La infraestructura de AWS se organiza en tres niveles jerárquicos que garantizan la
redundancia, la alta disponibilidad y la proximidad geográfica a los usuarios finales.

En el nivel superior se encuentran las **regiones**. Cada región es un área geográfica
independiente, como Europa (Irlanda) o Asia Pacífico (Tokio), que contiene múltiples
centros de datos agrupados. Las regiones están completamente aisladas entre sí, de modo
que un fallo en una región no afecta a las demás. La elección de una región adecuada
depende de varios factores. El primero es el **cumplimiento normativo (_compliance_)**,
ya que ciertas regulaciones exigen que los datos permanezcan dentro de una jurisdicción
concreta. El segundo es la **latencia**, puesto que conviene seleccionar la región más
cercana a los usuarios finales para minimizar los tiempos de respuesta. El tercero es la
**disponibilidad de servicios**, dado que no todos los servicios de AWS están
disponibles en todas las regiones y algunas funcionalidades se lanzan primero en
regiones específicas. El cuarto es el **precio**, que varía entre regiones debido a
diferencias en el coste de la energía, los impuestos y otros factores locales.

Dentro de cada región existen las **zonas de disponibilidad** (_Availability Zones_,
AZ). Cada zona de disponibilidad está compuesta por uno o varios centros de datos
físicamente separados, dotados de alimentación eléctrica, refrigeración y conectividad
de red independientes. Las zonas de disponibilidad de una misma región están
interconectadas, lo que permite diseñar arquitecturas que repliquen datos y servicios
entre varias zonas para tolerar el fallo completo de una de ellas.

Además de las regiones y las zonas de disponibilidad, AWS dispone de las denominadas
**_Edge Locations_**. Se trata de puntos de presencia distribuidos en un número de
ciudades muy superior al de las regiones, cuya función principal es acercar el contenido
a los usuarios finales. Estos puntos de presencia son la base de las redes de
distribución de contenido (_Content Delivery Network_, CDN). **Amazon CloudFront** es el
servicio CDN de AWS que almacena en caché copias del contenido en las _Edge Locations_
más cercanas al usuario, reduciendo significativamente la latencia en la entrega de
páginas web, vídeos, API y otros recursos estáticos o dinámicos.

## Modelo de responsabilidad compartida

El **_Shared Responsibility Model_** (modelo de responsabilidad compartida) define la
división de obligaciones de seguridad entre AWS y el cliente.

AWS asume la responsabilidad de la seguridad de la nube (_security of the cloud_). Esto
abarca la protección de toda la infraestructura física que sustenta los servicios: los
centros de datos, el _hardware_ de los servidores, la red global, los hipervisores y el
_software_ de virtualización. AWS se encarga del mantenimiento, la refrigeración, la
seguridad física de las instalaciones y la gestión de la red troncal que interconecta
las regiones y las zonas de disponibilidad.

El cliente, por su parte, es responsable de la seguridad en la nube (_security in the
cloud_). Esta responsabilidad incluye la gestión de los datos almacenados, la
configuración del cifrado, la administración de los accesos mediante políticas de
identidad, la configuración del sistema operativo de las instancias, la gestión de las
reglas de red y la protección de las aplicaciones desplegadas. En definitiva, todo
aquello que el cliente puede ver y configurar desde su cuenta de AWS recae bajo su
ámbito de responsabilidad.

## Gestión de identidades y accesos

El control de acceso combina dos operaciones que conviene no confundir. La
**autenticación** verifica la identidad de quien emite una petición, es decir, comprueba
que quien afirma ser un usuario determinado lo es realmente. La **autorización** decide,
una vez verificada la identidad, qué acciones puede ejecutar esa identidad y sobre qué
recursos.

**AWS Identity and Access Management** (_IAM_) es el servicio que permite controlar de
forma granular quién puede acceder a los recursos de AWS y qué acciones puede realizar
sobre ellos.

Al crear una cuenta de AWS se genera automáticamente un usuario _root_ que posee
permisos ilimitados sobre todos los recursos. Debido a su nivel de privilegio, se
recomienda encarecidamente no utilizar la cuenta _root_ para las operaciones diarias. En
su lugar, conviene crear usuarios de IAM individuales con los permisos estrictamente
necesarios para cada tarea, siguiendo el **principio de mínimo privilegio** (_least
privilege_). Sobre la cuenta _root_, y preferiblemente sobre todas las identidades con
privilegios elevados, debe activarse además la **autenticación multifactor**
(_multi-factor authentication_, MFA), que exige un segundo factor de verificación además
de la contraseña y que impide el acceso incluso si las credenciales quedan expuestas.

Los permisos en IAM se definen mediante **políticas** (_policies_), documentos en
formato JSON que especifican qué acciones están permitidas o denegadas sobre qué
recursos y bajo qué condiciones. Un usuario de IAM recién creado no puede realizar
ninguna operación, ya que IAM aplica una **denegación implícita** a toda acción que no
se haya autorizado de forma expresa. Los permisos se conceden por tanto de forma
incremental, añadiendo únicamente lo que cada tarea requiere. Estas políticas pueden
asociarse a usuarios individuales, aunque la práctica recomendada consiste en agrupar a
los usuarios en **grupos** de IAM y asignar las políticas al grupo, de modo que todos
sus miembros hereden los mismos permisos.

Los **roles** de IAM representan otro mecanismo fundamental. Un rol es una identidad con
permisos específicos que puede ser asumida temporalmente por un usuario, una aplicación
o un servicio de AWS. A diferencia de los usuarios, los roles no disponen de
credenciales permanentes. Cuando una entidad asume un rol, recibe credenciales
temporales con una duración limitada, lo que reduce el riesgo asociado a la exposición
de claves de acceso de larga duración. Los roles resultan especialmente útiles para
conceder permisos a instancias de EC2 que necesitan acceder a otros servicios de AWS, o
para permitir el acceso entre cuentas sin compartir credenciales.

## Redes

**Amazon VPC** (_Virtual Private Cloud_) es el servicio que permite crear redes
virtuales aisladas dentro de la infraestructura de AWS. Cada VPC funciona como un
entorno de red privado en el que se despliegan los recursos, con control total sobre el
rango de direcciones IP, las tablas de enrutamiento y las puertas de enlace.

Al crear una cuenta de AWS, se genera automáticamente una **VPC por defecto** en cada
región, configurada para que los recursos desplegados en ella tengan acceso a Internet
de forma inmediata. Si bien esta configuración resulta conveniente para pruebas y
prototipos, en entornos de producción es recomendable crear **VPC personalizadas** que
permitan definir con precisión qué recursos son accesibles desde Internet y cuáles
permanecen aislados.

Dentro de una VPC, los recursos se organizan en **subredes**. Las subredes públicas
están asociadas a una tabla de enrutamiento que dirige el tráfico hacia una puerta de
enlace de Internet (_Internet Gateway_), lo que permite que los recursos alojados en
ellas sean accesibles desde el exterior. Las subredes privadas, en cambio, carecen de
esta ruta, por lo que los recursos que contienen solo pueden comunicarse dentro de la
VPC o a través de mecanismos controlados como puertas de enlace NAT.

### Direccionamiento CIDR

El rango de direcciones IP de una VPC y sus subredes se define mediante la notación
**CIDR** (_Classless Inter-Domain Routing_). Una dirección IPv4 está compuesta por 32
bits. La notación CIDR indica cuántos bits son fijos (identifican la red) y cuántos son
libres (disponibles para asignar a los recursos). Por ejemplo, `10.0.0.0/24` significa
que los primeros 24 bits son fijos y los 8 restantes están disponibles, lo que permite
direcciones desde `10.0.0.0` hasta `10.0.0.255` (256 direcciones). Es importante tener
en cuenta que AWS reserva siempre 5 direcciones por subred: la dirección de red, la de
_broadcast_ y tres adicionales para uso interno.

### Conectividad con Internet y redes externas

Para que una VPC tenga acceso a Internet, es necesario asociar un **Internet Gateway**
(IGW) a la VPC y configurar la tabla de enrutamiento de las subredes públicas para
dirigir el tráfico hacia él. Las subredes privadas no disponen de esta ruta y, por
tanto, sus recursos no son accesibles directamente desde Internet.

Para conectar una VPC con redes privadas externas, como centros de datos _on-premise_ o
redes corporativas, AWS ofrece varias opciones:

| Servicio                 | Descripción                                                                                                                                                                                                                      |
| :----------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AWS Client VPN**       | Servicio completamente gestionado y elástico que permite conectar trabajadores remotos y redes _on-premise_ a AWS sin requerir _hardware_ dedicado. Escala automáticamente.                                                      |
| **AWS Site-to-Site VPN** | Establece conexiones cifradas entre redes _on-premise_ y AWS a través de Internet, con alta disponibilidad y escalado elástico.                                                                                                  |
| **AWS PrivateLink**      | Permite establecer conexiones privadas entre una VPC y servicios externos, otras VPCs u otros recursos, manteniendo el tráfico dentro de la red de AWS.                                                                          |
| **AWS Direct Connect**   | Conexión física privada y dedicada entre las instalaciones del cliente y AWS. Requiere tender fibra óptica hasta un punto de presencia de AWS, ofreciendo mayor ancho de banda y menor latencia que las conexiones por Internet. |

El **Virtual Private Gateway** es el componente del lado de AWS que permite establecer
una conexión VPN. Se asocia a una VPC y actúa como punto de terminación de los túneles
VPN, permitiendo que el tráfico procedente de la red corporativa acceda a los recursos
de la VPC de forma segura.

La diferencia práctica entre ambas familias de conexión está en el medio que utilizan.
Una VPN de sitio a sitio viaja por Internet, de modo que el ancho de banda es compartido
y el rendimiento depende del estado de la red pública, aunque su despliegue es inmediato
y no requiere obra física. Direct Connect, en cambio, proporciona un enlace privado y
dedicado, adecuado para transferencias masivas y sostenidas de datos, a costa de un
plazo de provisión mucho mayor. Ambas opciones no son excluyentes, ya que resulta
habitual configurar una VPN como camino alternativo (_fallback_) que asuma el tráfico si
el enlace dedicado deja de estar disponible.

### Enrutado global y entrega de contenido

Las conexiones anteriores resuelven la comunicación entre redes concretas, pero una
aplicación con usuarios repartidos por el mundo necesita además decidir a qué región
debe dirigirse cada petición. Esa decisión se toma en la resolución de nombres.

**Amazon Route 53** es el servicio de DNS (_Domain Name System_) gestionado de AWS. Su
función básica consiste en traducir los nombres de dominio en las direcciones IP de los
recursos que atienden el servicio, y sobre esa función se apoyan las políticas de
enrutado. La política más simple reparte las peticiones de forma rotatoria entre varios
destinos (_round robin_). La política de latencia dirige al usuario a la región que
ofrece el tiempo de respuesta más bajo. La política de geolocalización decide en función
de la ubicación del solicitante, lo que resulta útil para servir contenido adaptado a
cada país o para cumplir requisitos normativos. La política ponderada reparte el tráfico
en la proporción indicada, un mecanismo que habilita despliegues progresivos. Route 53
puede además comprobar el estado de los destinos y excluir de las respuestas los que no
responden, con lo que aporta conmutación por error a escala global.

Una vez resuelto el nombre, el contenido estático no tiene que recorrer necesariamente
toda la distancia hasta la región. Amazon CloudFront almacena copias en las _Edge
Locations_ y responde desde el punto de presencia más próximo al usuario, de modo que
solo las peticiones que no puede satisfacer con su caché llegan al origen. El recorrido
completo de una petición en una arquitectura global atraviesa por tanto Route 53, que
selecciona el destino, la _Edge Location_ de CloudFront, que sirve lo que tiene en
caché, y finalmente la región de origen, donde los recursos residen dentro de una o
varias VPC.

### Control de tráfico (Security Groups y ACL)

La seguridad a nivel de red se gestiona mediante dos mecanismos complementarios.

Los **grupos de seguridad** (_Security Groups_) actúan como cortafuegos virtuales **a
nivel de instancia**. Cada grupo de seguridad define reglas de entrada y salida que
especifican qué tipo de tráfico se permite. Por ejemplo, para un servidor web es
habitual configurar un grupo de seguridad que permita tráfico HTTP (puerto 80) y HTTPS
(puerto 443) desde cualquier origen, mientras que el acceso SSH (puerto 22) se restringe
a direcciones IP específicas. Los grupos de seguridad son **_stateful_**, lo que
significa que si se permite una conexión de entrada, la respuesta correspondiente se
permite automáticamente sin necesidad de una regla de salida explícita.

Las **listas de control de acceso de red** (_Network ACL_ o NACL) operan **a nivel de
subred**. Cada subred tiene asociada una NACL que evalúa los paquetes que entran y salen
de ella. A diferencia de los grupos de seguridad, las NACL son **_stateless_**: no
recuerdan si un paquete fue permitido previamente, por lo que cada paquete (tanto de
entrada como de salida) se evalúa de forma independiente contra las reglas definidas. Un
paquete que sale de una subred debe cumplir las reglas de salida de la NACL de origen, y
para entrar en la subred de destino debe cumplir las reglas de entrada de la NACL de
destino.

| Característica      | Security Group   | Network ACL             |
| :------------------ | :--------------- | :---------------------- |
| Nivel de aplicación | Instancia        | Subred                  |
| Estado              | _Stateful_       | _Stateless_             |
| Reglas              | Solo de permiso  | De permiso y denegación |
| Evaluación          | Todas las reglas | En orden numérico       |

## Seguridad

La gestión de identidades y el control del tráfico de red cubren quién accede y por
dónde lo hace. Queda un tercer frente, el de proteger la información almacenada y en
tránsito y detectar los comportamientos anómalos que consigan sortear las barreras
anteriores. Bajo el modelo de responsabilidad compartida, la protección de los datos
sensibles alojados en servicios como Amazon S3 o Amazon RDS recae en el cliente, de modo
que las herramientas que siguen son el instrumento con el que asumir esa
responsabilidad.

### Cifrado de datos

El cifrado en reposo protege la información almacenada frente al acceso directo al medio
físico o a un recurso mal configurado. Los _buckets_ de S3 creados actualmente aplican
cifrado de forma predeterminada, de manera que los objetos que se suben quedan cifrados
sin necesidad de configuración adicional. Los volúmenes de Amazon EBS admiten igualmente
cifrado, que puede establecerse como comportamiento por omisión de la cuenta para evitar
que un volumen nuevo quede desprotegido por descuido.

**AWS KMS** (_Key Management Service_) es el servicio que crea y administra las claves
criptográficas empleadas en esas operaciones. KMS centraliza la generación, la rotación
y el control de uso de las claves, y se integra con el resto de servicios de AWS, que
delegan en él el cifrado y el descifrado. Como el acceso a cada clave se gobierna
mediante políticas, es posible separar a quien puede leer un dato cifrado de quien puede
utilizar la clave que lo descifra.

El cifrado en tránsito protege la información mientras viaja entre el usuario y la
aplicación, y se implementa mediante certificados SSL/TLS. **AWS Certificate Manager**
(ACM) centraliza la gestión de esos certificados, incluyendo su emisión, su despliegue
en servicios como CloudFront o los balanceadores de carga, y su renovación automática,
lo que elimina una de las causas habituales de caída de un servicio web, la expiración
inadvertida de un certificado.

### Gestión de secretos

Las credenciales de una base de datos, las claves de API de servicios de terceros o los
_tokens_ de acceso no deben residir en el código ni en archivos de configuración
versionados. **AWS Secrets Manager** almacena estos secretos de forma cifrada y los
expone a la aplicación mediante una llamada a su API, de modo que el valor nunca se
escribe en el repositorio. El servicio permite además rotar los secretos de forma
automática y periódica, con lo que una credencial filtrada deja de ser válida al poco
tiempo sin necesidad de intervención manual.

### Protección frente a ataques

Los grupos de seguridad descritos anteriormente restringen el tráfico admitido por cada
instancia, pero no distinguen una petición legítima de otra malintencionada que utilice
los mismos puertos. Esa distinción corresponde a dos servicios específicos.

**AWS Shield** protege frente a ataques de denegación de servicio distribuido
(_Distributed Denial of Service_, DDoS), que buscan agotar los recursos del servicio
saturándolo con tráfico. Su nivel básico se aplica de forma automática y sin coste a
todas las cuentas, mientras que el nivel avanzado añade detección para ataques de mayor
sofisticación, protección frente a los costes derivados del escalado durante un ataque y
acceso a un equipo especializado de respuesta.

**AWS WAF** (_Web Application Firewall_) actúa en la capa de aplicación, donde
inspecciona el contenido de las peticiones HTTP y HTTPS y bloquea las que coinciden con
patrones de ataque conocidos, como la inyección de SQL o la ejecución de _scripts_ entre
sitios. Se asocia a los puntos de entrada del tráfico, entre ellos CloudFront, los
balanceadores de carga de aplicación y API Gateway, y admite reglas propias basadas en
direcciones de origen, cabeceras o límites de frecuencia de peticiones.

### Detección de amenazas e investigación

Ninguna configuración preventiva es perfecta, por lo que resulta necesario supervisar de
forma continua el entorno y disponer de un procedimiento para investigar lo que la
supervisión detecte. AWS cubre ese ciclo con cuatro servicios complementarios.

**Amazon Inspector** evalúa de forma automatizada la seguridad de las cargas de trabajo,
analizando las instancias de EC2, las imágenes de contenedor y las funciones Lambda en
busca de vulnerabilidades conocidas y de desviaciones respecto a las buenas prácticas,
como el acceso abierto a una instancia o la presencia de versiones de _software_
afectadas por un fallo publicado. Los hallazgos se presentan ordenados por gravedad,
cada uno con una descripción detallada y una recomendación concreta de corrección, y son
accesibles tanto desde la consola como a través de su API.

**Amazon GuardDuty** aporta detección inteligente de amenazas sobre la infraestructura
completa. Analiza de forma continua los flujos de metadatos de la cuenta y la actividad
de red del entorno, y combina listas de direcciones IP reconocidas como maliciosas con
detección de anomalías y aprendizaje automático para elevar la precisión de las
detecciones. Sus hallazgos incluyen los pasos de corrección recomendados, y su
publicación como evento permite encadenar una función Lambda a través de EventBridge
para aplicar la respuesta de forma automática.

**Amazon Detective** entra en juego cuando una amenaza ya se ha detectado y es necesario
determinar su causa raíz. El servicio agrega la información dispersa en varias fuentes y
la presenta como visualizaciones interactivas en una vista unificada, con las
interacciones entre recursos y usuarios situadas sobre una línea temporal configurable,
lo que permite reconstruir la secuencia de acontecimientos que condujo al incidente.

**AWS Security Hub** unifica todo lo anterior en un único lugar y formato. El servicio
agrega automáticamente los hallazgos de los servicios de seguridad de AWS y de
soluciones de terceros, los normaliza y los organiza en agrupaciones accionables
denominadas _insights_, lo que ofrece una visión conjunta del estado de seguridad y de
cumplimiento. La posibilidad de asociar acciones de corrección automática a esos
hallazgos reduce el tiempo hasta la resolución (_time to resolution_, TTR).

## Servicios de cómputo

AWS ofrece una amplia gama de servicios de cómputo (_Compute as a Service_) que se
adaptan a diferentes necesidades, desde máquinas virtuales tradicionales hasta entornos
completamente _serverless_.

### Amazon EC2

**Amazon EC2** (_Elastic Compute Cloud_) es el servicio de cómputo más fundamental de
AWS. Proporciona máquinas virtuales redimensionables, denominadas **instancias**, que
permiten ejecutar prácticamente cualquier carga de trabajo. EC2 opera bajo un modelo de
**_multitenancy_**, en el que múltiples instancias de distintos clientes comparten el
mismo _hardware_ físico subyacente. El aislamiento entre instancias lo garantiza un
hipervisor gestionado por AWS, que se encarga de asignar los recursos de CPU, memoria y
almacenamiento de forma segura y eficiente.

Para lanzar una instancia es necesario seleccionar una **AMI** (_Amazon Machine Image_),
que es una plantilla preconfigurada que define el sistema operativo, las aplicaciones
preinstaladas y la configuración inicial de la instancia. Las AMI pueden ser
proporcionadas por AWS, creadas por la comunidad, adquiridas en el _AWS Marketplace_ o
generadas por el propio usuario a partir de instancias existentes. Una misma AMI permite
crear múltiples instancias con configuraciones idénticas, lo que facilita la
reproducibilidad de los entornos.

Las instancias de EC2 se clasifican en **familias** optimizadas para distintos tipos de
carga de trabajo. Las instancias de propósito general (_General Purpose_) ofrecen un
equilibrio entre cómputo, memoria y red, y resultan adecuadas para servidores web y
entornos de desarrollo. Las instancias optimizadas para cómputo (_Compute Optimized_)
proporcionan mayor potencia de procesamiento para tareas intensivas en CPU, como el
procesamiento por lotes o el modelado científico. Las instancias optimizadas para
memoria (_Memory Optimized_) están diseñadas para cargas que requieren grandes
cantidades de RAM, como bases de datos en memoria. Las instancias de computación
acelerada (_Accelerated Computing_) incorporan aceleradores de _hardware_ como GPU para
tareas de aprendizaje profundo o renderizado gráfico. Las instancias optimizadas para
almacenamiento (_Storage Optimized_) ofrecen alto rendimiento de lectura y escritura en
disco para bases de datos distribuidas o sistemas de archivos de alto rendimiento.

La nomenclatura de las instancias sigue un patrón estandarizado. Por ejemplo, en
`t3.medium`, la letra `t` identifica la familia, el número `3` indica la generación y
`medium` especifica el tamaño, que determina la cantidad de CPU virtual y memoria
asignada. Es posible cambiar el tipo de instancia en cualquier momento para adaptarse a
nuevas necesidades de rendimiento.

Al configurar una instancia, el campo **_User Data_** permite especificar un _script_ en
Bash que se ejecuta automáticamente durante el primer arranque. Este mecanismo resulta
útil para instalar dependencias, configurar servicios o descargar código de forma
automatizada sin intervención manual.

Es importante tener en cuenta que las instancias de EC2 no ofrecen persistencia de datos
por defecto. Cuando una instancia se termina, toda la información almacenada en su
almacenamiento efímero se pierde. Por este motivo, los datos que deban conservarse deben
almacenarse en volúmenes persistentes como Amazon EBS o en servicios de almacenamiento
como Amazon S3.

### Modelos de precios

AWS ofrece varios modelos de precios para las instancias de EC2, diseñados para cubrir
distintos patrones de uso y niveles de compromiso.

El modelo **_On-Demand_** permite pagar por segundo o por hora de uso sin ningún
compromiso a largo plazo. Es la opción más flexible y resulta adecuada para cargas de
trabajo impredecibles, pruebas y prototipos, aunque también es la más costosa por unidad
de tiempo.

Los **_Savings Plans_** ofrecen descuentos significativos a cambio de un compromiso de
uso constante durante un período de uno o tres años. El compromiso se expresa en una
cantidad de gasto por hora, independientemente del tipo de instancia utilizado, lo que
proporciona flexibilidad para cambiar de familia o región.

Las **_Reserved Instances_** proporcionan descuentos similares a los _Savings Plans_,
pero vinculados a un tipo de instancia y una región específicos. El cliente puede elegir
entre pago total anticipado, pago parcial anticipado o sin pago anticipado, obteniendo
mayores descuentos cuanto mayor sea el pago inicial. Este modelo resulta idóneo para
cargas de trabajo predecibles y estables.

Las **_Spot Instances_** permiten acceder a capacidad de cómputo no utilizada con
descuentos de hasta el 90 % respecto al precio _On-Demand_. A cambio, AWS puede
interrumpir estas instancias con un aviso de dos minutos cuando necesite recuperar la
capacidad. Son especialmente útiles para tareas tolerantes a interrupciones, como
procesamiento por lotes, integración continua o análisis de datos a gran escala.

Los **_Dedicated Hosts_** proporcionan un servidor físico completo reservado para uso
exclusivo del cliente. Este modelo resulta necesario cuando existen requisitos de
licenciamiento de _software_ que exigen visibilidad sobre el _hardware_ subyacente o
cuando las regulaciones impiden compartir infraestructura física con otros usuarios.

Las **_Dedicated Instances_** son instancias que se ejecutan en _hardware_ dedicado al
cliente, pero sin la visibilidad ni el control a nivel de servidor físico que ofrecen
los _Dedicated Hosts_. Proporcionan aislamiento físico respecto a las instancias de
otros clientes.

### Contenedores

Los contenedores ofrecen una alternativa ligera a las máquinas virtuales, empaquetando
la aplicación junto con todas sus dependencias y configuraciones en una unidad portátil
y reproducible. AWS proporciona varios servicios para la gestión y ejecución de
contenedores.

**Amazon ECS** (_Elastic Container Service_) es un servicio de orquestación de
contenedores completamente gestionado que permite ejecutar, detener y administrar
contenedores _Docker_ a escala. ECS se integra de forma nativa con otros servicios de
AWS, lo que simplifica la configuración de redes, el balanceo de carga y la gestión de
permisos.

**Amazon EKS** (_Elastic Kubernetes Service_) es el servicio gestionado de _Kubernetes_
en AWS. Permite desplegar, gestionar y escalar aplicaciones contenerizadas utilizando el
estándar abierto de _Kubernetes_, lo que facilita la portabilidad entre entornos y la
adopción de herramientas del ecosistema _Kubernetes_.

**Amazon ECR** (_Elastic Container Registry_) es un registro de imágenes de contenedores
completamente gestionado, compatible con _Docker_, que permite almacenar, gestionar y
desplegar imágenes de contenedores de forma segura. El flujo de trabajo habitual
consiste en subir las imágenes a ECR y, a continuación, desplegarlas en ECS o EKS.

### Computación _serverless_

La computación _serverless_ representa un modelo en el que el desarrollador se
desentiende por completo de la gestión de la infraestructura subyacente. No es necesario
aprovisionar servidores, configurar sistemas operativos ni aplicar parches de seguridad.
AWS se encarga de todo ello, lo que permite al equipo de desarrollo centrarse
exclusivamente en el código de la aplicación. Desde la perspectiva del modelo de
responsabilidad compartida, AWS asume una mayor proporción de las responsabilidades
operativas en los servicios _serverless_.

**AWS Lambda** es el servicio _serverless_ más representativo de AWS, basado en el
paradigma de _Function as a Service_ (FaaS). El funcionamiento consiste en empaquetar el
código en una función _Lambda_, configurar uno o varios **_triggers_**
(desencadenadores) y dejar que el servicio ejecute la función automáticamente cada vez
que se produce el evento asociado. Los _triggers_ pueden ser muy variados: una petición
HTTPS a través de _API Gateway_, la subida de un archivo a Amazon S3, un mensaje en una
cola de Amazon SQS o un evento programado, entre otros.

AWS Lambda escala de forma automática replicando las instancias de la función en
respuesta a la demanda, sin intervención del usuario. El modelo de facturación se basa
exclusivamente en el número de invocaciones y en el tiempo de ejecución consumido,
medido en milisegundos, de modo que no se incurre en coste alguno cuando la función no
se ejecuta. La duración máxima de una ejecución de Lambda es de 15 minutos, lo que lo
convierte en una solución idónea para procesos basados en eventos, microservicios y
tareas de corta duración que no requieren un servidor activo de forma permanente. Lambda
soporta múltiples lenguajes de programación a través de distintos _runtimes_ y también
admite la ejecución de contenedores.

**AWS Fargate** es un motor de cómputo _serverless_ diseñado específicamente para
contenedores. Permite ejecutar contenedores en ECS o EKS sin necesidad de aprovisionar
ni gestionar instancias de EC2. Con Fargate, el usuario define los requisitos de CPU
virtual, memoria y almacenamiento para cada contenedor, y AWS se encarga de la
infraestructura subyacente. La facturación se calcula en función de los recursos
consumidos por cada contenedor.

### Otros servicios de cómputo

**AWS Elastic Beanstalk** es un servicio que simplifica el despliegue y la gestión de
aplicaciones en EC2. El desarrollador solo necesita proporcionar el código de la
aplicación y Elastic Beanstalk se encarga del aprovisionamiento de la infraestructura,
la configuración del balanceo de carga, el escalado automático y la monitorización,
manteniendo al mismo tiempo la visibilidad y el control sobre los recursos subyacentes.

**AWS Batch** es un servicio diseñado para ejecutar tareas de procesamiento por lotes
(_batch_) a gran escala. Gestiona de forma automática la infraestructura necesaria,
soporta procesamiento en paralelo y escala dinámicamente los recursos, desplegando
instancias de EC2 o _Spot Instances_ según la carga de trabajo.

**Amazon Lightsail** ofrece una experiencia simplificada para el despliegue de
aplicaciones web, sitios _WordPress_, entornos de desarrollo y pequeñas bases de datos,
con precios predecibles y una interfaz diseñada para usuarios que no requieren la
complejidad completa de EC2.

**AWS Outposts** es un servicio que extiende la infraestructura y los servicios de AWS a
las instalaciones del cliente (_on-premise_). Permite ejecutar servicios de AWS de forma
local, lo que resulta especialmente útil para cargas de trabajo que requieren baja
latencia, procesamiento local de datos o el cumplimiento de requisitos normativos que
impiden que los datos abandonen las instalaciones del cliente.

## Escalado y alta disponibilidad

Diseñar sistemas que soporten variaciones en la demanda y que continúen operando ante
fallos parciales constituye uno de los principios fundamentales de la arquitectura en la
nube.

El **escalado vertical** (_scaling up_) consiste en aumentar la capacidad de una
instancia existente, por ejemplo, migrando a un tipo de instancia con más CPU o memoria.
Aunque es sencillo de implementar, presenta un límite físico determinado por el tamaño
máximo de instancia disponible y requiere generalmente un reinicio.

El **escalado horizontal** (_scaling out_) consiste en añadir más instancias para
distribuir la carga de trabajo entre ellas. Este enfoque permite una paralelización
efectiva, no tiene un límite teórico de capacidad y resulta más resiliente, ya que el
fallo de una instancia individual no compromete la disponibilidad del servicio.

**Amazon EC2 Auto Scaling** permite automatizar el escalado horizontal de las instancias
de EC2 en función de la demanda. El servicio monitoriza métricas como la utilización de
CPU, la latencia de las peticiones o indicadores personalizados, y ajusta
automáticamente el número de instancias dentro de unos límites mínimo y máximo definidos
por el usuario. De este modo, se garantiza que siempre exista la capacidad suficiente
para atender la demanda sin incurrir en costes innecesarios durante los períodos de baja
actividad.

**Elastic Load Balancing** (ELB) es el servicio de balanceo de carga de AWS que
distribuye automáticamente el tráfico entrante entre múltiples instancias, contenedores
u otros destinos. El balanceador de carga se sitúa entre los usuarios y el grupo de
instancias, y emplea algoritmos de distribución como _round robin_, menor número de
conexiones activas (_least connections_), _hash_ de IP o menor tiempo de respuesta
(_least response time_) para repartir las peticiones de forma uniforme. Cuando EC2 Auto
Scaling lanza una nueva instancia, esta se registra automáticamente en el balanceador de
carga y comienza a recibir tráfico una vez que supera las comprobaciones de estado. ELB
también escala de forma automática para adaptarse al volumen de tráfico.

Para lograr una alta disponibilidad real, es fundamental desplegar las instancias en
**múltiples zonas de disponibilidad** dentro de una misma región. De este modo, si una
zona de disponibilidad completa experimenta un fallo, las instancias en las zonas
restantes continúan atendiendo las peticiones sin interrupción. La combinación de EC2
Auto Scaling, Elastic Load Balancing y despliegues en múltiples zonas de disponibilidad
constituye el patrón básico para construir arquitecturas tolerantes a fallos en AWS.

## Monitorización

Monitorizar una infraestructura consiste en observar el estado de sus recursos para
tomar decisiones fundamentadas sobre ella. De esa observación dependen el
dimensionamiento de la capacidad, la detección temprana de errores y su notificación al
equipo responsable, el mantenimiento de la seguridad, el control del gasto y la mejora
del rendimiento. Sin monitorización, la operación se vuelve reactiva y cada incidente se
descubre por sus consecuencias en lugar de por sus síntomas.

**Amazon CloudWatch** es el servicio de monitorización y observabilidad de AWS que
permite recopilar, visualizar y analizar métricas, registros (_logs_) y eventos de
prácticamente cualquier recurso de la plataforma. CloudWatch proporciona información en
tiempo real sobre el rendimiento de las instancias de EC2, el estado de los
balanceadores de carga, la utilización de las bases de datos y cualquier otra métrica
relevante para la operación de la infraestructura. Además de las métricas que los
servicios publican por sí mismos, la aplicación puede enviar **métricas personalizadas**
que reflejen indicadores propios del negocio.

A partir de las métricas recopiladas, es posible configurar alarmas que se activan
cuando un indicador supera o desciende por debajo de un umbral definido. Estas alarmas
pueden desencadenar acciones automáticas, como el escalado de instancias a través de EC2
Auto Scaling o el envío de notificaciones al equipo de operaciones. Los cuadros de mando
(_dashboards_) reúnen en una sola vista las métricas de servicios distintos, y la
centralización de los registros de todas las instancias en un mismo lugar evita tener
que acceder a cada máquina para diagnosticar un problema. El efecto combinado es una
reducción del tiempo medio de resolución (_mean time to resolution_, MTTR) y una mejora
del coste total de propiedad (_total cost of ownership_, TCO) de la plataforma.

Mientras CloudWatch responde a la pregunta de cómo se comportan los recursos, **AWS
CloudTrail** responde a la de quién hizo qué. Toda operación en AWS se traduce en una
llamada a una API, y CloudTrail registra cada una de esas llamadas junto con la
identidad que la originó, la fecha y la hora, los parámetros empleados y la dirección de
origen. Los registros pueden entregarse a un _bucket_ de S3 y conservarse de forma
indefinida, lo que proporciona la traza histórica necesaria para auditar la actividad de
la cuenta, demostrar el cumplimiento normativo e identificar el origen de un problema de
seguridad o de un cambio de configuración inesperado.

## Mensajería y desacoplamiento

En arquitecturas distribuidas, la comunicación directa y síncrona entre componentes
genera un acoplamiento fuerte que puede provocar fallos en cascada: si un componente
deja de responder, todos los que dependen de él se ven afectados. Para evitar este
problema, AWS ofrece servicios de mensajería que permiten una comunicación asíncrona y
desacoplada entre los distintos componentes de una aplicación.

**Amazon SQS** (_Simple Queue Service_) es un servicio de colas de mensajes
completamente gestionado. Un componente emisor deposita mensajes en la cola y un
componente receptor los consume a su propio ritmo. Los mensajes permanecen en la cola
hasta que son procesados, lo que garantiza que no se pierden aunque el receptor no esté
disponible temporalmente. Este patrón de comunicación elimina la dependencia temporal
entre emisor y receptor, aumentando la resiliencia del sistema.

**Amazon SNS** (_Simple Notification Service_) es un servicio de mensajería basado en el
modelo de publicación y suscripción (_pub/sub_). Un publicador envía un mensaje a un
**tema** (_topic_) de SNS, y el servicio se encarga de distribuirlo a todos los
suscriptores registrados, que pueden ser colas de SQS, funciones Lambda, puntos de
enlace HTTP o direcciones de correo electrónico, entre otros. SNS resulta especialmente
útil para difundir notificaciones o eventos a múltiples consumidores de forma
simultánea.

**Amazon EventBridge** es un servicio _serverless_ de bus de eventos que permite
conectar diferentes componentes de una aplicación, servicios de AWS y aplicaciones de
terceros mediante eventos. EventBridge facilita la construcción de arquitecturas
orientadas a eventos (_event-driven_) al proporcionar reglas de enrutamiento que dirigen
cada evento al destino adecuado en función de su contenido.

## Almacenamiento

AWS agrupa sus servicios de almacenamiento en tres familias que se diferencian por la
unidad mínima con la que trabajan. De esa unidad se derivan sus prestaciones, sus
protocolos de acceso y su coste, de modo que la elección condiciona el rendimiento de la
aplicación completa.

El **almacenamiento de bloques** divide la información en bloques de tamaño fijo que se
gestionan de forma individual. Modificar un archivo solo obliga a reescribir los bloques
afectados, lo que proporciona un comportamiento equivalente al de un disco físico y lo
convierte en la base de los sistemas operativos y de las bases de datos.

El **almacenamiento de objetos** trata cada elemento como una unidad indivisible formada
por los datos, un identificador único y un conjunto de metadatos. Cualquier modificación
implica reescribir el objeto completo, y el acceso se realiza mediante llamadas a una
API en lugar de mediante un sistema de archivos. Resulta idóneo para contenido que se
escribe una vez y se consulta íntegro muchas veces.

El **almacenamiento de archivos** expone una jerarquía de directorios accesible a través
de protocolos de red, lo que permite que varias máquinas trabajen simultáneamente sobre
el mismo árbol de archivos con la semántica habitual de un sistema de archivos.

La decisión entre las tres familias depende de si la información se recupera al completo
o de forma parcial, de la latencia y del rendimiento de lectura y escritura requeridos,
del número de máquinas que necesitan acceso concurrente y de la granularidad con la que
deben concederse los permisos.

### Almacenamiento de bloques

Las instancias de EC2 disponen de un almacenamiento efímero denominado **_instance
store_**, formado por volúmenes físicamente adheridos al servidor que aloja la
instancia. Ofrece un rendimiento muy elevado, pero su contenido desaparece cuando la
instancia se detiene o se termina, por lo que solo resulta adecuado para datos
temporales, cachés o resultados intermedios.

**Amazon EBS** (_Elastic Block Store_) proporciona discos virtuales persistentes que se
asocian a una instancia y cuyo ciclo de vida es independiente del de esta. Un volumen de
EBS reside en una única zona de disponibilidad, dentro de la cual AWS replica
automáticamente su contenido para garantizar durabilidad y disponibilidad. El
rendimiento se expresa en **IOPS** (_input/output operations per second_), que miden el
número de operaciones de lectura y escritura por segundo, y en el ancho de banda
sostenido del volumen. Los volúmenes respaldados por SSD favorecen las cargas
transaccionales con accesos aleatorios frecuentes, como las bases de datos, mientras que
los respaldados por HDD resultan más económicos para accesos secuenciales sobre grandes
volúmenes de datos. Tanto el tamaño como el tipo de volumen pueden modificarse sin
detener la instancia.

Las copias de seguridad de EBS se realizan mediante **_snapshots_**, capturas del estado
del volumen en un instante concreto. Los _snapshots_ son incrementales, ya que después
de la primera captura solo se almacenan los bloques que han cambiado respecto a la
anterior, lo que reduce de forma notable el tiempo y el coste de las copias. A partir de
un _snapshot_ es posible crear un volumen nuevo, incluso en otra zona de disponibilidad
o en otra región, lo que permite migrar datos y replicar entornos completos, por ejemplo
para levantar un entorno de pruebas a partir de los datos de producción. **Amazon Data
Lifecycle Manager** automatiza este ciclo mediante políticas que definen cada cuánto se
crean los _snapshots_, cuántos se conservan y cuándo se eliminan.

### Almacenamiento de objetos con Amazon S3

**Amazon S3** (_Simple Storage Service_) es el servicio de almacenamiento de objetos de
AWS. Permite almacenar y recuperar cualquier cantidad de datos, de cualquier tipo de
archivo, en cualquier momento y desde cualquier lugar. S3 organiza los datos en
**buckets** (contenedores) y cada objeto almacenado se identifica mediante una clave
única dentro de su _bucket_. El número de objetos por _bucket_ es ilimitado y el tamaño
máximo de un objeto individual es de 5 TB, si bien la carga de objetos grandes se
realiza por partes (_multipart upload_) para poder reanudarla ante un fallo de red. El
servicio ofrece una durabilidad del 99,999999999 % (once nueves) y está diseñado para
soportar prácticamente cualquier caso de uso, desde el alojamiento de sitios web
estáticos hasta el almacenamiento de copias de seguridad, _data lakes_ y contenido
multimedia.

El **versionado** puede habilitarse a nivel de _bucket_ para conservar todas las
revisiones de un mismo objeto. Con el versionado activo, una sobrescritura o un borrado
accidental no destruyen la información, ya que las versiones anteriores permanecen
accesibles. S3 puede además emitir notificaciones cuando se crea o se elimina un objeto,
lo que habilita flujos de procesamiento basados en eventos, como la invocación de una
función Lambda al subirse un archivo.

El control de acceso se articula en dos niveles complementarios. Las **políticas de
_bucket_** son documentos JSON asociados al recurso que conceden o deniegan permisos de
lectura y escritura a identidades concretas, y actúan de forma coordinada con las
políticas de IAM. Por encima de ellas, **S3 Block Public Access** funciona como un
interruptor de seguridad a nivel de cuenta o de _bucket_ que bloquea cualquier acceso
público, incluso cuando una política del _bucket_ lo permitiría de forma explícita. Esta
prevalencia es deliberada, puesto que la exposición pública involuntaria de un _bucket_
constituye uno de los errores de configuración más frecuentes y de mayor impacto.

#### Clases de almacenamiento

No toda la información se consulta con la misma frecuencia. Determinados archivos deben
estar disponibles de forma inmediata y permanente, mientras que otros se conservan
durante años por motivos normativos y se recuperan en muy raras ocasiones. Para cubrir
ese espectro, S3 ofrece varias **clases de almacenamiento** que intercambian coste por
inmediatez de acceso. Las clases se asignan objeto a objeto, de modo que un mismo
_bucket_ puede contener objetos en clases distintas.

| Clase                             | Caso de uso                                                       | Consideraciones                                                               |
| :-------------------------------- | :---------------------------------------------------------------- | :---------------------------------------------------------------------------- |
| **S3 Standard**                   | Datos de acceso frecuente y propósito general.                    | Mayor coste de almacenamiento y sin coste de recuperación.                    |
| **S3 Intelligent-Tiering**        | Datos con patrón de acceso desconocido o cambiante.               | Mueve los objetos entre niveles de forma automática por una cuota de gestión. |
| **S3 Standard-IA**                | Datos de acceso poco frecuente que deben recuperarse al instante. | Menor coste de almacenamiento y coste por recuperación.                       |
| **S3 One Zone-IA**                | Datos poco frecuentes y reproducibles, como copias secundarias.   | Reside en una sola zona de disponibilidad, por lo que tolera menos fallos.    |
| **S3 Glacier Instant Retrieval**  | Archivado con acceso ocasional en milisegundos.                   | Duración mínima de almacenamiento facturable.                                 |
| **S3 Glacier Flexible Retrieval** | Copias de seguridad consultadas una o dos veces al año.           | La recuperación tarda de minutos a horas según la modalidad elegida.          |
| **S3 Glacier Deep Archive**       | Conservación a largo plazo por requisitos normativos.             | Coste mínimo y recuperación en el orden de horas.                             |

La asignación no tiene que ser manual ni definitiva. Las **reglas de ciclo de vida**
permiten trasladar los objetos a clases más económicas a medida que envejecen y
eliminarlos cuando expira el plazo de conservación, lo que ajusta el coste al valor real
que la información conserva en cada momento.

### Almacenamiento de archivos

**Amazon EFS** (_Elastic File System_) es un sistema de archivos de red compatible con
NFS, el protocolo habitual en los sistemas Linux. Su capacidad crece y decrece de forma
automática según los archivos que se añaden o se eliminan, sin necesidad de aprovisionar
tamaño alguno. Un mismo sistema de archivos admite el acceso concurrente y de baja
latencia de múltiples instancias de EC2, contenedores o funciones Lambda, lo que lo
convierte en la opción natural para compartir código, modelos o resultados entre varios
nodos de cómputo. EFS es un servicio regional, de modo que el sistema de archivos es
accesible desde varias zonas de disponibilidad, y sus políticas de ciclo de vida
trasladan de forma automática los archivos a los que no se accede a una clase de
almacenamiento más económica.

**Amazon FSx** cubre los casos en los que se requiere un protocolo o un motor de sistema
de archivos concreto, todos ellos completamente gestionados por AWS. FSx for Windows
File Server ofrece recursos compartidos SMB integrados con Active Directory. FSx for
Lustre proporciona el rendimiento agregado que demandan la computación de altas
prestaciones y el entrenamiento de modelos, con integración directa con los datos
alojados en S3. FSx for NetApp ONTAP y FSx for OpenZFS reproducen las capacidades de
esos sistemas de archivos para las cargas que ya dependen de ellos.

### Almacenamiento híbrido y recuperación ante desastres

**AWS Storage Gateway** conecta las instalaciones propias con el almacenamiento de la
nube, de forma que las aplicaciones locales siguen utilizando sus protocolos habituales
mientras los datos residen en AWS. Sus usos más frecuentes son las copias de seguridad
de sistemas _on-premise_ y el archivado de información que se consulta muy raramente.

La variante **Amazon S3 File Gateway** expone una interfaz de archivos, accesible por
NFS o SMB, cuyo contenido se almacena realmente como objetos en S3. Los archivos
consultados con más frecuencia se mantienen en una caché local que proporciona baja
latencia, mientras que el conjunto completo permanece en S3, lo que combina la comodidad
de un recurso compartido con el coste del almacenamiento de objetos.

**AWS Elastic Disaster Recovery** aborda un problema distinto, el de la continuidad del
servicio ante una caída completa. El servicio replica de forma continua los servidores
de origen a nivel de bloque, con lo que mantiene en AWS una réplica exacta y actualizada
de cada máquina. Al reducirse al mínimo el intervalo entre el último estado replicado y
el fallo, la recuperación consiste en levantar las instancias correspondientes en
cuestión de minutos, sin necesidad de restaurar copias de seguridad completas.

### Elección del servicio de almacenamiento

La regla práctica atiende al modo en que la aplicación escribe y lee la información. Las
cargas que modifican fragmentos de archivos de forma continua, como los archivos de
datos de una base de datos o el sistema de archivos de una instancia, requieren
almacenamiento de bloques y, por tanto, volúmenes de EBS. El contenido que se escribe
una vez y se consulta íntegro, como imágenes, vídeos, registros históricos, copias de
seguridad o conjuntos de datos de entrenamiento, encaja en S3. Los escenarios en los que
varias máquinas necesitan compartir un mismo árbol de directorios corresponden a EFS o a
FSx.

!!! warning "Una base de datos no se aloja en S3"

    Situar los archivos de datos de una base de datos en almacenamiento de objetos es un
    error de diseño recurrente. Al ser el objeto la unidad indivisible de S3, cada
    escritura parcial obligaría a reescribirlo por completo, con un coste y una latencia
    incompatibles con una carga transaccional. Las bases de datos autogestionadas se
    apoyan en volúmenes de EBS, y las gestionadas delegan por completo esta decisión en
    el servicio correspondiente.

## Bases de datos

Los sistemas gestores de bases de datos relacionales (_Relational Database Management
System_, RDBMS) organizan la información en tablas vinculadas entre sí y se consultan
mediante SQL, el lenguaje que permite filtrar registros y recorrer las relaciones
existentes entre ellos, tal y como se describe en el capítulo dedicado a
[SQL](../01_databases/section_1_sql.md). AWS ofrece este modelo y también alternativas
no relacionales, con distintos grados de delegación de la administración.

La opción con mayor control consiste en instalar el motor en una instancia de EC2. El
cliente asume entonces la totalidad de las tareas operativas, desde la instalación y la
aplicación de parches hasta las copias de seguridad, la replicación y la conmutación por
error. Este enfoque solo se justifica cuando se necesita un motor no soportado o un
nivel de personalización que los servicios gestionados no permiten.

**Amazon RDS** (_Relational Database Service_) es el servicio gestionado que facilita la
configuración, operación y escalado de bases de datos relacionales en la nube. RDS
soporta varios motores, como MySQL, PostgreSQL, MariaDB, Oracle y SQL Server, y se
encarga de las tareas rutinarias de administración, entre ellas la aplicación de parches
y las copias de seguridad automáticas con recuperación a un instante concreto. Los
despliegues en múltiples zonas de disponibilidad mantienen una réplica en espera que
asume el servicio de forma automática si la instancia principal falla, mientras que las
réplicas de lectura permiten distribuir las consultas y aliviar la carga de la instancia
principal. Bajo el modelo de responsabilidad compartida, AWS opera el motor y la
infraestructura, y el cliente sigue siendo responsable del diseño del esquema, de la
optimización de las consultas y del control de accesos.

**Amazon Aurora** es el motor propio de AWS, compatible con MySQL y PostgreSQL, que
sustituye la capa de almacenamiento tradicional por una arquitectura distribuida y
replicada entre varias zonas de disponibilidad. Esa arquitectura le permite alcanzar
hasta cinco veces el rendimiento de una instalación estándar de MySQL y hasta tres veces
el de PostgreSQL, además de admitir hasta quince réplicas de lectura y de aumentar la
capacidad de almacenamiento de forma automática a medida que los datos crecen.

**Amazon DynamoDB** es un servicio de base de datos NoSQL completamente gestionado,
orientado a pares de clave y valor y a documentos. Cada elemento almacena sus propios
atributos sin ajustarse a un esquema fijo, lo que aporta flexibilidad frente a modelos
de datos heterogéneos o cambiantes. DynamoDB mantiene latencias de pocos milisegundos
con independencia del volumen almacenado y ofrece dos modelos de capacidad, uno bajo
demanda, que se adapta al tráfico sin configuración previa, y otro aprovisionado con
escalado automático, más económico cuando la carga es predecible.

**Amazon ElastiCache** proporciona una caché en memoria gestionada, compatible con
Valkey, Redis OSS y Memcached, que se sitúa delante de la base de datos. Las consultas
repetidas se atienden desde memoria en tiempos inferiores al milisegundo, con lo que se
reduce el número de peticiones que llegan al motor. El efecto es doble, ya que mejora el
tiempo de respuesta percibido por la aplicación y aumenta el rendimiento agregado del
sistema, y además permite dimensionar la base de datos con instancias más económicas.
También está disponible en modalidad _serverless_, en la que la capacidad se ajusta de
forma automática al uso.

Además de los anteriores, AWS ofrece motores especializados en modelos de datos
concretos. **Amazon DocumentDB** es una base de datos documental compatible con MongoDB,
lo que facilita la migración de aplicaciones que ya utilizan ese modelo. **Amazon
Neptune** es una base de datos de grafos, diseñada para resolver con baja latencia
consultas sobre datos densamente conectados, como redes de relaciones, sistemas de
recomendación, detección de fraude o grafos de conocimiento.

Dos servicios transversales completan el conjunto. **AWS DMS** (_Database Migration
Service_) migra bases de datos hacia AWS manteniendo el origen en funcionamiento durante
el proceso, tanto entre motores idénticos como entre motores distintos. **AWS Backup**
centraliza las copias de seguridad de varios servicios, entre ellos EBS, RDS, DynamoDB,
EFS y S3, bajo políticas comunes de retención y de copia entre regiones, lo que
sustituye los enfoques fragmentados en los que cada servicio se respalda por separado y
con reglas propias.

## Datos, analítica e inteligencia artificial

Los servicios anteriores almacenan y sirven datos operativos. Explotarlos con fines
analíticos requiere un recorrido adicional que va desde la ingesta hasta la
visualización, y que en AWS se cubre encadenando servicios especializados en cada etapa.

### Almacenamiento analítico

La distinción entre _data lake_ y _data warehouse_ introducida en el capítulo de
[fundamentos](section_1_fundamentals.md) tiene una traducción directa en el catálogo de
AWS. El _data lake_ es el depósito de datos en crudo, sin una estructura impuesta de
antemano, y su implementación habitual es un _bucket_ de S3. El _data warehouse_
contiene datos ya organizados y modelados para responder a las preguntas del negocio, y
se materializa en **Amazon Redshift**, un almacén de datos orientado a columnas y
optimizado para consultas analíticas sobre grandes volúmenes de información.

### Ingesta de datos

**Amazon Kinesis Data Streams** captura flujos de datos en tiempo real con baja latencia
y conserva los registros durante una ventana configurable, de modo que varios
consumidores independientes pueden leer el mismo flujo y procesarlo con lógicas
distintas. Resulta adecuado cuando la aplicación debe reaccionar a los eventos en el
momento en que se producen.

**Amazon Data Firehose** cubre el caso contrario, la entrega casi en tiempo real hacia
un destino de almacenamiento. El servicio acumula los registros en lotes y los deposita
en destinos como S3, Redshift u OpenSearch, con la posibilidad de transformarlos,
comprimirlos y cifrarlos antes de la carga. Al no requerir administración de
infraestructura, es la vía habitual para alimentar un _data lake_ de forma continua.

### Procesamiento de datos

**AWS Glue** es un servicio de extracción, transformación y carga sin servidores. Su
componente **Glue Data Catalog** almacena los metadatos de los conjuntos de datos
disponibles, y los rastreadores (_crawlers_) recorren los orígenes para inferir sus
esquemas y catalogarlos de forma automática, lo que permite que otros servicios los
descubran y los consulten sin definiciones manuales.

**Amazon EMR** (_Elastic MapReduce_) aprovisiona y gestiona clústeres para ejecutar
_frameworks_ de procesamiento distribuido como Apache Spark, Hive o Hadoop. Es la opción
indicada para transformaciones complejas sobre grandes volúmenes de datos que no encajan
en un flujo de ETL declarativo.

### Análisis y monitorización

**Amazon Athena** permite consultar con SQL los datos que residen en S3 sin aprovisionar
ninguna infraestructura y facturando en función del volumen de datos examinado. Se apoya
en el catálogo de Glue para conocer los esquemas, lo que hace posible analizar la
información donde se encuentra, sin cargarla previamente en un almacén de datos.

**Amazon QuickSight** es el servicio de inteligencia de negocio (_business
intelligence_) que construye cuadros de mando y visualizaciones interactivas a partir de
esos datos. **Amazon OpenSearch Service** cubre la indexación y la búsqueda de texto
completo, así como la monitorización en tiempo real de registros y métricas.

### Inteligencia artificial y aprendizaje automático

**Amazon SageMaker AI** es la plataforma gestionada para construir, entrenar y desplegar
modelos de aprendizaje automático. Cubre el ciclo completo, desde los entornos de
desarrollo y la preparación de los datos hasta los trabajos de entrenamiento, el ajuste
de hiperparámetros y la publicación del modelo en un punto de inferencia gestionado.

Cuando no se parte de cero, **SageMaker JumpStart** ofrece un catálogo de modelos
preentrenados y de modelos fundacionales que sirven como punto de partida para
adaptarlos después a un caso de uso concreto. **Amazon Bedrock** lleva esa idea un paso
más allá al proporcionar acceso mediante API a modelos fundacionales de varios
proveedores, con soporte multimodal y mecanismos de personalización, sin que el cliente
gestione en ningún momento la infraestructura que los ejecuta.

En el nivel de infraestructura, las instancias de computación acelerada descritas en la
sección de cómputo aportan la capacidad de cálculo necesaria para el entrenamiento,
junto con los aceleradores diseñados por AWS, Trainium para entrenar modelos e
Inferentia para servirlos en producción.

## Infraestructura como código

**AWS CloudFormation** es el servicio de infraestructura como código (_Infrastructure as
Code_, IaC) nativo de AWS. Permite definir todos los recursos de una arquitectura en
plantillas declarativas escritas en formato JSON o YAML. A partir de estas plantillas,
CloudFormation aprovisiona y configura los recursos de forma automática, reproducible y
consistente. Si es necesario realizar cambios, basta con modificar la plantilla y
aplicar la actualización, y CloudFormation se encarga de determinar qué recursos deben
crearse, actualizarse o eliminarse. Este enfoque elimina la configuración manual, reduce
los errores humanos y permite versionar la infraestructura del mismo modo que se
versiona el código fuente de una aplicación.

## Despliegues híbridos

No todas las organizaciones migran la totalidad de su infraestructura a la nube. En
muchos casos, los requisitos de latencia, la normativa sobre residencia de datos o la
existencia de sistemas heredados hacen necesario mantener parte de la infraestructura en
las propias instalaciones (_on-premise_) y combinarla con servicios en la nube. Este
enfoque se conoce como **despliegue híbrido**.

**AWS Outposts** permite ejecutar servicios de AWS directamente en el centro de datos
del cliente, utilizando el mismo _hardware_ y _software_ que en las regiones de AWS. De
este modo, las aplicaciones que requieren baja latencia o que deben procesar datos
sensibles de forma local pueden beneficiarse de las mismas API, herramientas y modelos
operativos que se utilizan en la nube pública. Outposts se integra de forma transparente
con la región de AWS más cercana, lo que permite construir arquitecturas híbridas
coherentes en las que los datos y las cargas de trabajo fluyen entre el entorno local y
la nube según las necesidades del negocio.

## Gobernanza y cumplimiento normativo

Los servicios descritos hasta ahora construyen y operan la arquitectura. Sostenerla en
el tiempo exige además decidir quién puede desplegar qué, demostrar ante terceros que la
infraestructura cumple la normativa aplicable y comprobar que la configuración real no
se desvía de la deseada. La **gobernanza** es precisamente el marco de políticas,
procesos y estructuras con el que una organización alinea el uso de la tecnología con
sus objetivos y verifica su cumplimiento.

### Residencia de los datos

AWS no replica los datos entre regiones de forma automática. Cualquier copia hacia otra
región es siempre una decisión explícita del cliente, y esa garantía es la que hace
posible cumplir las normativas de residencia de la información, como el Reglamento
General de Protección de Datos (RGPD) europeo, que condiciona la transferencia de datos
personales fuera de determinadas jurisdicciones. La elección de la región, planteada al
principio del capítulo como una decisión de latencia y de coste, es por tanto también
una decisión de cumplimiento normativo.

### Evaluación de la configuración

**AWS Config** registra la configuración de los recursos y su evolución a lo largo del
tiempo, y la evalúa frente a las reglas que la organización decide imponer. Cuando un
recurso deja de satisfacer una regla, el servicio lo señala como no conforme, lo que
permite auditar el entorno de forma continua y generar informes de cumplimiento en lugar
de depender de revisiones manuales periódicas. Es la herramienta indicada cuando el
objetivo consiste en verificar que los recursos desplegados por los distintos equipos
respetan las políticas internas.

**AWS Audit Manager** cubre la fase siguiente, la de demostrarlo. El servicio automatiza
la recopilación de las evidencias que acreditan el cumplimiento de un estándar y las
organiza según los marcos de referencia habituales, con lo que sustituye la tarea manual
de reunir registros, capturas e informes cada vez que se afronta una auditoría.

**AWS Artifact** es el repositorio de documentación de cumplimiento de AWS. Su sección
de informes reúne los informes de seguridad y cumplimiento de AWS y de proveedores
terceros, útiles para evaluar la postura de seguridad de los servicios en los que se
apoya la propia arquitectura, mientras que su sección de acuerdos permite revisar,
aceptar y gestionar los acuerdos legales suscritos con AWS.

### Gestión de múltiples cuentas

**AWS Organizations** es el servicio de gestión de cuentas que consolida varias cuentas
de AWS en una única organización, con facturación agregada y administración
centralizada. Las cuentas se agrupan en **unidades organizativas** (_organizational
unit_, OU), agrupaciones lógicas que pueden contener cuentas u otras unidades, lo que
permite reproducir la estructura de la empresa o la separación entre entornos de
desarrollo y producción.

Sobre esa jerarquía se aplican las **políticas de control de servicios** (_service
control policy_, SCP), que establecen el permiso máximo disponible y pueden asociarse
tanto a una unidad organizativa como a una cuenta concreta. Una SCP no concede permisos
por sí misma, sino que fija el techo de lo que las políticas de IAM de esa cuenta pueden
llegar a autorizar, de modo que ninguna identidad de la cuenta puede exceder ese límite
ni siquiera con una política permisiva.

**AWS Control Tower** automatiza la puesta en marcha de un entorno multicuenta conforme
a las buenas prácticas. Crea la estructura inicial de cuentas y unidades organizativas,
aplica barreras de protección (_guardrails_) que impiden las configuraciones prohibidas
o señalan las desviaciones detectadas, y ofrece un cuadro de mando con el estado de
cumplimiento del conjunto.

**AWS Service Catalog** delimita el conjunto de servicios y recursos que los equipos
pueden desplegar por sí mismos. La organización define, organiza y comparte productos
aprobados y preconfigurados, con lo que el autoservicio deja de entrar en conflicto con
la gobernanza. **AWS License Manager** administra las licencias de _software_ asociadas
a las cargas de trabajo y ayuda a ajustar su coste, evitando tanto el incumplimiento de
los términos de licencia como la compra de más licencias de las necesarias.

### Estado del servicio y recomendaciones

**AWS Health Dashboard** informa del estado de los servicios de AWS y, sobre todo, de
los eventos que afectan de forma concreta a los recursos de la cuenta propia, como el
mantenimiento programado de una instancia o la degradación de un servicio en una región
determinada.

**AWS Trusted Advisor** actúa como un asesor automatizado que inspecciona la cuenta y
emite recomendaciones agrupadas en cinco categorías, la optimización de costes, el
rendimiento, la seguridad, la tolerancia a fallos y los límites de servicio. Su utilidad
reside en detectar de forma sistemática lo que una revisión manual pasa por alto, como
un volumen que nadie utiliza, un _bucket_ con permisos excesivos o una cuota a punto de
agotarse.

## Costes y facturación

AWS factura bajo un modelo de pago por uso, sin inversión inicial ni compromiso
obligatorio, y aplica descuentos derivados de la economía de escala a medida que crece
el consumo. Sobre esa base, comprometer un uso sostenido durante uno o tres años reduce
el precio unitario, tal y como se describió en los modelos de precios de EC2.

Tres dimensiones concentran la mayor parte de la factura. La primera es el **cómputo**,
que se paga por el tiempo que los recursos permanecen en ejecución. La segunda es el
**almacenamiento**, que depende del volumen de datos conservado y de la clase de
almacenamiento elegida. La tercera es la **transferencia de datos**, cuyo comportamiento
conviene subrayar, ya que la entrada de datos hacia AWS habitualmente no tiene coste,
mientras que la salida hacia Internet (_outbound_) sí lo tiene, al igual que una parte
significativa del tráfico entre zonas de disponibilidad y entre regiones.

### Herramientas de gestión del gasto

| Servicio                            | Función                                                                                                                                                              |
| :---------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AWS Billing and Cost Management** | Centraliza la facturación, con los cargos vigentes, el uso, las previsiones, las facturas, los pagos y los presupuestos.                                             |
| **AWS Budgets**                     | Define presupuestos de coste, de uso o de aprovechamiento de _Savings Plans_ y _Reserved Instances_, y avisa cuando se superan los umbrales fijados.                 |
| **AWS Cost Explorer**               | Analiza la evolución del gasto con gráficos e informes, proyecta la tendencia futura y sugiere oportunidades de ahorro, entre ellas compras de _Reserved Instances_. |
| **AWS Pricing Calculator**          | Estima el coste de una arquitectura antes de desplegarla y compara configuraciones alternativas de cómputo, almacenamiento y transferencia de datos.                 |

### Optimización de costes

La mayor parte del ahorro en la capa de cómputo procede de ajustar la capacidad a la
demanda real. El redimensionamiento de las instancias de EC2, apoyado en las
recomendaciones de **AWS Compute Optimizer**, identifica los recursos sobredimensionados
que consumen presupuesto sin aportar rendimiento. Las _Spot Instances_ permiten
aprovechar capacidad no utilizada con descuentos de hasta el 90 % frente al precio
_On-Demand_ en las cargas flexibles y tolerantes a interrupciones. El escalado
automático adapta la capacidad a las necesidades de la aplicación y evita el
sobredimensionamiento permanente al que conduce el ajuste manual. A todo ello se añade
una práctica elemental y a menudo olvidada, la eliminación de los recursos que ya no se
utilizan pero siguen facturando, como instancias detenidas con volúmenes asociados,
volúmenes de EBS huérfanos y _snapshots_ antiguos.

En la capa de datos el criterio es equivalente. Una base de datos de RDS debe
dimensionarse según su carga real, y cuando el patrón de acceso está dominado por las
lecturas resulta más económico añadir réplicas de lectura o una caché de ElastiCache que
ampliar la instancia principal. En S3, la elección de la clase de almacenamiento
adecuada marca la diferencia, con S3 Intelligent-Tiering como opción indicada cuando el
patrón de acceso es variable o desconocido. Comprimir los datos, especialmente los
archivos de texto, y definir reglas de ciclo de vida que eliminen las versiones antiguas
y las copias de seguridad que han dejado de ser necesarias completan la optimización del
almacenamiento.

Queda la transferencia de datos, que suele ser la partida menos evidente. Diseñar la
arquitectura para que el tráfico no cruce innecesariamente entre zonas de disponibilidad
ni salga a Internet reduce esa factura de forma directa. Los **_VPC endpoints_**
contribuyen a ese objetivo al permitir que la VPC alcance servicios como S3 a través de
la red privada de AWS, sin pasar por Internet público, lo que además de recortar el
coste de transferencia mejora la postura de seguridad. La idea de fondo es que muchas
optimizaciones pequeñas distribuidas entre varios servicios producen un ahorro
considerable y, en la mayoría de los casos, mejoran al mismo tiempo el rendimiento, la
fiabilidad y la eficiencia operativa.

## Soporte y ecosistema

AWS publica un volumen considerable de material de consulta gratuito, que constituye la
primera vía de resolución de dudas. Incluye la documentación oficial con las guías de
usuario y de los SDK, los _whitepapers_ de arquitectura, el blog de AWS y la comunidad
AWS re:Post, donde se plantean y resuelven preguntas técnicas.

Cuando el material de consulta no basta, los **planes de soporte** determinan el nivel
de asistencia disponible. Los tiempos de respuesta son acumulativos, de modo que cada
plan mantiene los compromisos del anterior y añade los propios de los casos más graves.

| Plan                   | Uso recomendado                                      | Respuesta y acompañamiento                                                                                                                                                 |
| :--------------------- | :--------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Basic**              | Incluido en todas las cuentas de AWS.                | Atención de consultas de cuenta y facturación, documentación, _whitepapers_, AWS re:Post y las comprobaciones básicas de Trusted Advisor. No permite abrir casos técnicos. |
| **Developer**          | Experimentación y pruebas en AWS.                    | Orientación general en menos de 24 horas y sistema afectado en menos de 12 horas.                                                                                          |
| **Business**           | Nivel mínimo recomendado para cargas de producción.  | Sistema de producción afectado en menos de 4 horas y fuera de servicio en menos de 1 hora, con el conjunto completo de comprobaciones de Trusted Advisor.                  |
| **Enterprise On-Ramp** | Producción con operaciones críticas para el negocio. | Sistema crítico para el negocio fuera de servicio en menos de 30 minutos y orientación proactiva de un grupo de gestores técnicos de cuenta.                               |
| **Enterprise**         | Cargas críticas para el negocio y de misión crítica. | Sistema de misión crítica fuera de servicio en menos de 15 minutos, recomendaciones prioritarias del equipo de cuenta y un gestor técnico de cuenta designado.             |

El acompañamiento de un **gestor técnico de cuenta** (_Technical Account Manager_, TAM)
aparece únicamente en los dos últimos planes y es la diferencia cualitativa entre ellos
y los anteriores, ya que aporta orientación arquitectónica y operativa continuada en
lugar de respuesta puntual a incidencias.

Más allá del soporte, dos elementos completan el ecosistema. **AWS Marketplace** es un
catálogo digital de _software_ de terceros listo para desplegar, que incluye
aplicaciones en modalidad SaaS, modelos preentrenados, conjuntos de datos y herramientas
de análisis, con la ventaja de que su facturación se integra en la de AWS. La **AWS
Partner Network** (APN) agrupa a los socios consultores y tecnológicos que construyen y
comercializan soluciones sobre AWS, a los que el programa proporciona apoyo técnico, de
_marketing_ y de comercialización.
