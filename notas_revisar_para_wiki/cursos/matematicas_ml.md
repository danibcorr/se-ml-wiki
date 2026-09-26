---
authors: Daniel Bazo Correa
description:
    Apuntes de álgebra lineal para machine learning: vectores, sistemas de
    ecuaciones y operaciones con matrices.
title: Matemáticas para ML
---

!!! warning

    Contenido transcrito automáticamente a partir de apuntes manuscritos. Puede
    contener errores de lectura y está pendiente de revisión.

## Tema 1: Álgebra lineal

### Vectores

Cualquier objeto que pueda sumarse con otro objeto del mismo tipo y multiplicarse por un
escalar, devolviendo otro objeto del mismo tipo, se puede considerar un vector (se
denota en letra negrita).

Ejemplo: $\mathbf{a} = [1, 2, 3]$, $\mathbf{a} \in \mathbb{R}^3$.

En ocasiones consideramos los datos como representaciones vectoriales en $\mathbb{R}^n$.

Por ejemplo, veremos un sistema de ecuaciones:

$$
\begin{aligned}
① \quad & x_1 + x_2 + x_3 = 3 \\
② \quad & x_1 - x_2 + 2x_3 = 2 \\
③ \quad & 2x_1 + 3x_3 = 5
\end{aligned}
$$

A partir de las operaciones ① + ② y ① − ②:

$$
① + ② \Rightarrow 2x_1 + 3x_3 = 5 \Rightarrow ③ \Rightarrow x_1 = \frac{5 - 3x_3}{2}
$$

$$
① - ② \Rightarrow 2x_2 - x_3 = 1 \Rightarrow x_2 = \frac{1 + x_3}{2}
$$

Siendo $x_3$ un valor libre tal que $x_3 = a \in \mathbb{R}$, el sistema tiene la
solución:

$$
\left(\frac{5 - 3a}{2}, \ \frac{1 + a}{2}, \ a\right), \quad a \in \mathbb{R}
$$

En un sistema de ecuaciones lineales con dos variables ($x_1, x_2$) cada ecuación lineal
define una línea en el plano, donde la solución es la intersección (viéndolo como un
plano, sería la parte donde se tocan ambas líneas), que puede ser otra línea, un punto o
vacío si las líneas son paralelas.

Un sistema de ecuaciones puede ser visto como:

$$
\begin{cases}
4x_1 + 4x_2 = 5 \\
2x_1 - 4x_2 = 1
\end{cases}
\Rightarrow
\begin{pmatrix} 4 \\ 2 \end{pmatrix} x_1 + \begin{pmatrix} 4 \\ -4 \end{pmatrix} x_2 =
\begin{pmatrix} 5 \\ 1 \end{pmatrix}
\Rightarrow
\begin{bmatrix} 4 & 4 \\ 2 & -4 \end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \end{bmatrix} =
\begin{bmatrix} 5 \\ 1 \end{bmatrix}
$$

Esta última forma es un sistema de matrices.

### Matrices

La suma de dos matrices, $A \in \mathbb{R}^{m \times n}$ y
$B \in \mathbb{R}^{m \times
n}$, se define como la suma elemento a elemento:

$$
A + B :=
\begin{bmatrix}
a_{11} + b_{11} & \cdots & a_{1n} + b_{1n} \\
\vdots & & \vdots \\
a_{m1} + b_{m1} & \cdots & a_{mn} + b_{mn}
\end{bmatrix} \in \mathbb{R}^{m \times n}
$$

Propiedades de las matrices:

- No son conmutativas: $A \cdot B \neq B \cdot A$.
- Asociativa:
  $\forall A \in \mathbb{R}^{m \times n}, B \in \mathbb{R}^{n \times p}, C
  \in \mathbb{R}^{p \times q}: (A \cdot B) \cdot C = A \cdot (B \cdot C)$.
- Distributiva:
  $\forall A, B \in \mathbb{R}^{m \times n}, C, D \in \mathbb{R}^{n
  \times p}: (A + B) \cdot C = AC + B \cdot C$;
  $A(C + D) = AC + AD$.

Si contamos con una matriz cuadrada $A \in \mathbb{R}^{n \times n}$, y tenemos otra
matriz $B \in \mathbb{R}^{n \times n}$, si $A \cdot B = I = B \cdot A$, entonces $B$ es
la inversa de $A$, definida como $A^{-1}$. No todas las matrices tienen inversa; cuando
la tienen se llaman matrices regulares, invertibles o no singulares. En caso contrario,
se conocen como singulares o no invertibles.

Si tenemos una matriz $A \in \mathbb{R}^{m \times n}$, la matriz
$B \in \mathbb{R}^{n
\times m}$ con $a_{ij} = b_{ji}$ se dice que es la transpuesta de
$A$, $B = A^T$.

Algunas propiedades de las inversas y transpuestas:

- $A \cdot A^{-1} = I = A^{-1} \cdot A$
- $(A \cdot B)^{-1} = B^{-1} \cdot A^{-1}$
- $(A + B)^{-1} \neq A^{-1} + B^{-1}$
- $(A^T)^T = A$
- $(A \cdot B)^T = B^T \cdot A^T$
- $(A + B)^T = A^T + B^T$

Una matriz $A \in \mathbb{R}^{n \times n}$ es simétrica si $A = A^T$. Solo las matrices
cuadradas pueden ser simétricas.

## Contenido eliminado por estar ya en la wiki

| Tema eliminado                                                                                      | Ya cubierto en                                                                   |
| --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Definición formal de matriz (dimensiones $m \times n$, elementos $a_{ij}$)                          | `docs/07_artificial_intelligence/01_mathematics/section_2_deep_learning_math.md` |
| Definición y fórmula del producto matricial, incluida la condición de compatibilidad de dimensiones | `docs/07_artificial_intelligence/01_mathematics/section_2_deep_learning_math.md` |
| Definición del producto de Hadamard (elemento a elemento, mismo tamaño)                             | `docs/07_artificial_intelligence/01_mathematics/section_2_deep_learning_math.md` |

## Procedencia

Contenido transcrito a partir de `Cursos/Matemáticas Para ML.pdf` (dentro de
`notas_ml.zip`), páginas 1 y 2. Podado para eliminar contenido ya cubierto en la wiki
publicada (`docs/`); ver tabla anterior.
