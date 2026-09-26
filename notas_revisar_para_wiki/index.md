---
authors: Daniel Bazo Correa
description:
    Índice de los apuntes manuscritos de notas_ml.zip transcritos a Markdown y depurados
    contra el contenido ya publicado en la wiki.
title: Apuntes transcritos
---

!!! warning

    Contenido transcrito automáticamente a partir de apuntes manuscritos. Puede
    contener errores de lectura y está pendiente de revisión.

Esta carpeta es un área de preparación: recoge la transcripción a Markdown de los
apuntes manuscritos contenidos en `notas_ml.zip`, ya depurada. De cada documento se ha
eliminado la información que `docs/` cubre, de modo que lo que queda es únicamente
contenido candidato a incorporarse a la wiki.

## Documentos

| Documento                                               | Contenido nuevo que conserva                                                             | PDF de origen                                          | Páginas   |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------ | --------- |
| `cursos/matematicas_ml.md`                              | Sistemas de ecuaciones lineales, propiedades de matrices, inversa, transpuesta           | `Cursos/Matemáticas Para ML.pdf`                       | 1-2       |
| `cursos/sql.md`                                         | NoSQL, pipeline de ejecución de consultas, diseño físico de claves, SQLite               | `Cursos/SQL/Apuntes SQL.pdf`                           | 1-14      |
| `cursos/data_engineer/01_introduccion_data_engineer.md` | Rol del ingeniero de datos, Hadoop, Airflow, Kafka, datos operativos frente a analíticos | `Cursos/Data Engineer/Apuntes.pdf`                     | 1-4       |
| `cursos/data_engineer/02_proxmox.md`                    | Proxmox completo: la wiki no trata virtualización ni hipervisores                        | `Cursos/Data Engineer/Apuntes.pdf`                     | 5-7       |
| `cursos/data_engineer/03_docker.md`                     | Entornos dev/prod separados, rutas de datos de MySQL y PostgreSQL                        | `Cursos/Data Engineer/Apuntes.pdf`                     | 9-17      |
| `cursos/data_engineer/04_kubernetes.md`                 | Detalle del Control Plane, Kubeflow, MLflow                                              | `Cursos/Data Engineer/Apuntes.pdf`                     | 18-27, 30 |
| `cursos/mlops.md`                                       | ZenML, Ray y entrenamiento distribuido, Ray Tune, taxonomía de tests                     | `Cursos/MLOps/Apuntes.pdf`                             | 1-8       |
| `cursos/devops.md`                                      | Introducción a DevOps, Jenkins, configuración avanzada de Compose                        | `Cursos/DevOps Curso/Apuntes DevOps.pdf`               | 1-9       |
| `cursos/dli_clases.md`                                  | Ciclo APOD, flujo iterativo con `nsys`, ejercicios con cifras de rendimiento             | `Cursos/DLI/Clases.pdf`                                | 1-10      |
| `cursos/estructuras_datos.md`                           | Tablas hash, cinco ejercicios de entrevista, listas circulares                           | `Cursos/Estructuras de Datos Y Algoritmos/Apuntes.pdf` | 1-11      |
| `machine_learning/general.md`                           | Formulación de entropía y ganancia de información, derivación del margen en SVM          | `Machine Learning/Machine Learning.pdf`                | 1-5       |
| `machine_learning/mit.md`                               | Fórmula de la atención, truco de reparametrización del VAE, difusión                     | `Machine Learning/Mit.pdf`                             | 1-5       |
| `machine_learning/ssl.md`                               | SwAV, BYOL, Barlow Twins                                                                 | `Machine Learning/SSL.pdf`                             | 1-2       |
| `machine_learning/variado.md`                           | Mixture of Experts, cuantización de modelos, SAM sobre ViT                               | `Machine Learning/Variado.pdf`                         | 1-8       |

## Cómo leer estos documentos

Cada documento incluye, antes de su sección `## Procedencia`, una tabla
`## Contenido eliminado por estar ya en la wiki` que enumera los temas podados y el
fichero de `docs/` que los cubre. Esa tabla permite auditar la depuración sin releer la
wiki.

Las marcas presentes en el texto significan lo siguiente:

| Marca                 | Significado                                                        |
| --------------------- | ------------------------------------------------------------------ |
| `[ilegible]`          | Trazo del original que no se pudo descifrar.                       |
| `término[?]`          | Lectura plausible pero no segura.                                  |
| `%% [?]` en Mermaid   | Relación del diagrama inferida por no ser inequívoca en el dibujo. |
| `<!-- revisar: … -->` | Posible solape con la wiki que no se pudo resolver con certeza.    |

Las capturas de pantalla y diapositivas pegadas en los apuntes no se reproducen como
imagen: su texto y código se transcriben dentro de un bloque
`!!! note "Figura del original"`.

## Criterios aplicados

- El contenido se limita a lo presente en los apuntes originales. No se ha añadido
  material externo.
- Se eliminó todo fragmento que la wiki ya explica con igual o mayor profundidad. Cuando
  el apunte añadía un matiz sobre un concepto ya cubierto, se conservó solo el matiz.
- Los desarrollos matemáticos se conservaron incluso cuando la wiki nombra el concepto,
  si la wiki no incluye la formulación.
- Los apuntes incompletos no se completaron: hay código sin terminar y una solución de
  complejidad peor que la que pide su enunciado, fieles al original.
