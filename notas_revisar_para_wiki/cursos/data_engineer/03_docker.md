---
authors: Daniel Bazo Correa
description:
    Notas de Docker podadas por solape con la wiki. Contenido nuevo restante: rutas de
    datos de MySQL y PostgreSQL, y el flujo de ambientes de desarrollo con
    Dockerfile.dev y docker-compose-dev.yml.
title: Docker
---

!!! warning

    Contenido transcrito automáticamente a partir de apuntes manuscritos. Puede
    contener errores de lectura y está pendiente de revisión.

## Volúmenes

Otras rutas de datos habituales no cubiertas en la wiki: MySQL usa `/var/lib/mysql`,
PostgreSQL usa `/var/lib/postgresql/data`.

## Ambientes y hot reload

Los ambientes nos permiten diferenciar la parte de desarrollo de la parte de producción.
Para ello creamos un fichero `Dockerfile.dev` y `docker-compose-dev.yml`.

En el `docker-compose-dev.yml` cambia lo siguiente respecto al `docker-compose.yml` de
producción:

```yaml
version: "3.9"
services:
    mi_app:
        build:
            context: . # define el contexto (/app) donde se va a estar trabajando
            dockerfile: Dockerfile.dev # le indicamos el Dockerfile.dev
        ports:
            - "3000:3000"
        links:
            - mongodb
        volumes:
            - .:/home/app # definimos un volumen anónimo: ruta actual del host
              # mapeada a la ruta del contenedor
```

Para ejecutar Docker Compose usamos:

```bash
docker compose -f docker-compose-dev.yml up
```

Docker Compose crea una red de forma automática entre los servicios
definidos.<!-- revisar: posible solape con docs/06_operations/section_1_containers.md -->

## Contenido eliminado por estar ya en la wiki

| Tema eliminado                                                                                                                                                         | Ya cubierto en                                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Definición de contenedor, portabilidad, capas de imágenes, Docker vs. VM, Docker Desktop, Docker Hub                                                                   | `docs/06_operations/section_1_containers.md`                                                             |
| Comandos de imágenes (`docker images`, `docker pull`, `docker image rm`)                                                                                               | `docs/06_operations/section_1_containers.md`                                                             |
| Comandos de contenedores (`docker create`, `docker start`, `docker ps`, `docker stop`, `--name`, `docker exec`, `docker network inspect`, `docker stats`, `docker cp`) | `docs/06_operations/section_1_containers.md`                                                             |
| Port mapping (`docker container create -p`) y `docker logs` / `docker logs --follow`                                                                                   | `docs/06_operations/section_1_containers.md`                                                             |
| `docker run` (crear + iniciar en un solo paso)                                                                                                                         | `docs/06_operations/section_1_containers.md`                                                             |
| Variables de entorno para conectar contenedores (ejemplo MongoDB)                                                                                                      | `docs/06_operations/section_1_containers.md`                                                             |
| Dockerfile básico (`FROM`, `RUN mkdir`, `COPY`, `EXPOSE`, `CMD`)                                                                                                       | `docs/06_operations/section_1_containers.md`                                                             |
| Creación y uso de redes (`docker network ls`, `docker network create`, `--network`)                                                                                    | `docs/06_operations/section_1_containers.md`                                                             |
| Modos de red de Docker (bridge, red personalizada)                                                                                                                     | `docs/06_operations/section_1_containers.md`                                                             |
| Tipos de volúmenes (anónimo, de host, nombrado) y declaración en Compose                                                                                               | `docs/06_operations/section_1_containers.md`                                                             |
| Docker Compose: estructura del fichero, `docker compose up`, `docker compose down`                                                                                     | `docs/06_operations/section_1_containers.md`                                                             |
| Ejemplo "Wattpad Mate" (imagen Linux → Python → pip, publicación de puerto host-contenedor)                                                                            | `docs/06_operations/section_1_containers.md` (concepto genérico de mapeo de puertos e imágenes en capas) |

## Procedencia

Cursos/Data Engineer/Apuntes.pdf, páginas 9 a 17. Documento podado: se ha eliminado el
contenido ya cubierto por `docs/06_operations/section_1_containers.md` (véase la tabla
anterior); solo se conserva la información nueva respecto a la wiki.
