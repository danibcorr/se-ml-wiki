---
authors: Daniel Bazo Correa
description:
    Notas de Kubernetes podadas por solape con la wiki. Contenido nuevo restante:
    detalles de Control Plane (api server, etcd, controller manager, scheduler,
    kubelet, kube-proxy), namespace por defecto, limitaciones del NodePort en
    producción, y las definiciones de Kubeflow y MLflow.
title: Kubernetes
---

!!! warning

    Contenido transcrito automáticamente a partir de apuntes manuscritos. Puede
    contener errores de lectura y está pendiente de revisión.

## Exponer aplicaciones

Cuando una petición llega al servicio, este se redirige de manera aleatoria a uno de los
pods gestionados por el
_deployment_.<!-- revisar: posible solape con docs/06_operations/section_3_orchestrators.md -->

Cuando un pod se reescala o se elimina, pierde toda la información asociada si está
alojada localmente. Para mantener la persistencia en un _deployment_ (no un StatefulSet)
necesitamos crear un `PersistentVolumeClaim`.

## Componentes de Kubernetes

!!! note "Figura del original"

    Diagrama oficial de arquitectura de un clúster de Kubernetes. Muestra el
    **Control Plane** (recuadro punteado) con los componentes `api` (API server),
    `etcd` (almacén de estado, persistence store), `c-m` (Controller manager),
    `c-c-m` (Cloud controller manager, opcional) y `sched` (Scheduler), conectado a
    una **Cloud provider API** (AWS, GCP, Azure) para crear o eliminar instancias,
    balanceadores de carga, etc. El Control Plane se conecta a varios **Node**
    (nodos), cada uno con los agentes `kubelet` y `k-proxy` (kube-proxy).
    Anotaciones manuscritas: el Control Plane "maneja el clúster"; cada Node es una
    VM u ordenador físico que actúa como _worker_ en el clúster; `kubelet` es el
    agente de Kubernetes; `kube-proxy` recibe el tráfico y lo manda a los pods que
    requieren ese tráfico; `etcd` es la base de datos con el estado de Kubernetes.

- **Kubelet**: es un agente que permite conectar la API de Kubernetes (orquestador) con
  cada _worker_. Ese agente se encuentra en cada _worker_.

## Kubectl y namespace por defecto

Al no especificar el _namespace_ al crear un pod, Kubernetes lo hace en el
`default`.<!-- revisar: posible solape con docs/06_operations/section_3_orchestrators.md -->

Podemos ejecutar comandos dentro del pod:

```bash
kubectl exec -it nombre-pod
```

## Servicios

El **NodePort** presenta limitaciones en seguridad y escalabilidad si lo llevamos a
producción.

## Definiciones de MLOps sobre Kubernetes

### Kubeflow

Plataforma de código abierto que proviene de Kubernetes. Simplifica el desarrollo,
organización, implementación y ejecución de cargas de trabajo de ML de manera escalable
y portátil.

### MLflow

Es una plataforma de código abierto desarrollada por Databricks que se utiliza para
gestionar el ciclo de vida de ML. Sus componentes son:

- **Tracking**: registra los resultados y parámetros de los modelos para su comparativa.
- **Projects**: empaqueta el código para que sea reproducible.
- **Models**: permite gestionar el versionado de modelos.

Kubeflow puede ser utilizado para crear flujos de trabajo de ML y orquestarlos en
Kubernetes. MLflow puede ser usado para realizar un seguimiento de los experimentos
dentro de cada etapa del flujo de trabajo.

## Contenido eliminado por estar ya en la wiki

| Tema eliminado                                                                                                                | Ya cubierto en                                                                               |
| ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Motivación de Kubernetes (escalado de Docker, manifiestos declarativos, distribución en workers, ETL/Spark/Airflow/Kubeflow)  | `docs/06_operations/section_3_orchestrators.md`                                              |
| Definición de Nodo, nodos on-demand y spot, disco local vs. volúmenes persistentes                                            | `docs/06_operations/section_3_orchestrators.md`                                              |
| Definición de Pod, contenedores compartiendo recursos y red, escalado como unidad                                             | `docs/06_operations/section_3_orchestrators.md`                                              |
| Deployment: declarar réplicas y manifiesto de ejemplo (`nginx`, `replicas: 3`)                                                | `docs/06_operations/section_3_orchestrators.md`                                              |
| Definición de clúster (conjunto de nodos/workers)                                                                             | `docs/06_operations/section_3_orchestrators.md`                                              |
| Manifiesto de un `Deployment` con variables de entorno, `resources`, `readinessProbe`                                         | `docs/06_operations/section_3_orchestrators.md`                                              |
| `DaemonSet`: definición y manifiesto de ejemplo                                                                               | `docs/06_operations/section_3_orchestrators.md`                                              |
| Manifiesto de un `Pod` simple y `kubectl apply -f` / `kubectl get pods`                                                       | `docs/06_operations/section_3_orchestrators.md`                                              |
| Manifiesto de `Pod` completo con `env`, `resources`, `readinessProbe`, `livenessProbe`, `ports`                               | `docs/06_operations/section_3_orchestrators.md`                                              |
| `StatefulSet` y volúmenes: definición, manifiesto de ejemplo, `kubectl get pvc` / `kubectl get sts`                           | `docs/06_operations/section_3_orchestrators.md`                                              |
| Networking de pods: IP propia por pod, Cloud Cluster Networking Interface, namespace compartido de puertos                    | `docs/06_operations/section_3_orchestrators.md`                                              |
| `etcd`, kube-proxy y tipos de servicio (ClusterIP con ejemplo, NodePort con ejemplo, LoadBalancer, Ingress)                   | `docs/06_operations/section_3_orchestrators.md`                                              |
| Kubectl: definición general de la herramienta                                                                                 | `docs/06_operations/section_3_orchestrators.md`                                              |
| Minikube: definición, `minikube start`, `minikube status`                                                                     | `docs/06_operations/section_3_orchestrators.md`                                              |
| Definición de manifiesto (registro de intención, estado deseado)                                                              | `docs/06_operations/section_3_orchestrators.md`                                              |
| Namespace: definición, políticas de tráfico, `kubectl get ns`, `kubectl -n ... get pods -o wide`, `kubectl -n ... delete pod` | `docs/06_operations/section_3_orchestrators.md`                                              |
| Ejemplo "Wattpad Mate" (Pod → VM1/VM2 → Proxmox → Hardware, definición de clúster)                                            | `docs/06_operations/section_3_orchestrators.md` (ejemplo equivalente con Proxmox, VM1 y VM2) |

## Procedencia

Cursos/Data Engineer/Apuntes.pdf, páginas 18 a 27 y 30. La página 28 (divisor
"Secretos") no contiene desarrollo aprovechable y la página 29 es un divisor manuscrito
("MLOps"). Documento podado: se ha eliminado el contenido ya cubierto por
`docs/06_operations/section_3_orchestrators.md` (véase la tabla anterior); solo se
conserva la información nueva respecto a la wiki.
