---
layout: page
permalink: /computer_graphics/rasterizing/transform_matrix
title: Transform matrix
---
## How to render instances within a scene?
### Facts
- Each **instance** to render has two properties:
    1. an existing **model**
    2. a **transform** such as:
        - **scale**: increase/decrease the size of the original model.
        - **rotation**: rotate the original model.
        - **translation**: translates from the original model position.
- A camera also has a **transform**.

### Steps to render an object
#### 1. From model space to world space
1. Apply scale:
$vertex_{modelscaled} = vertex_{model} * instance.scale$
2. Apply rotation:
$vertex_{modelrotated} = vertex_{modelscaled} * instance.rotation$
3. Apply translation:
$vertex_{world} = vertex_{modelrotated} * instance.translation$
#### 2. From world space to camera space
1. Apply camera translation:
$vertex_{translated} = vertex_{world} - camera.translation$
2. Apply camera rotation:
$vertex_{camera} = camera.rotation^{-1} * vertex_{translated}$
#### 3. From camera space to viewport
$viewport_x = \frac{V_{camera}.x * d}{V_{camera}.z}$
$viewport_y = \frac{V_{camera}.y * d}{V_{camera}.z}$
#### 4. From viewport to canvas
$canvas_x = \frac{viewport_x * canvas_{width}}{viewport_{width}}$
$canvas_y = \frac{viewport_y * canvas_{height}}{viewport_{height}}$

### A Matrix point of view
$C_r$ camera rotation, $C_t$ camera translation, $I_r$ instance rotation, $I_s$ instance scale, $I_t$ instance translation, $P$ perspective projection, $M$ viewport-to-canvas, $V$ original vertex, $V'$ point on the canvas.

$V' = M(P(C_r^{-1}(C_t^{-1}(I_t(I_r)(I_s(V))))))$
$F =M \times P \times C_r^{-1} \times C_t^{-1} \times I_t \times I_r \times I_s$
$V' = F(V)$

### How to simplify the function from original vertex to point on the canvas?
Problem: Manipulation of 3D point and 3D vectors. Solution: add a forth value to $\begin{pmatrix}x\cr y\cr z\end{pmatrix}$ such as $\begin{pmatrix}x\cr y\cr z\cr w\end{pmatrix}$ where:
- if $w = 0$, it is a point,
- if $w = 1$, it is a vector,
called **homogeneous coordinates**. Examples:
- substracting two points produces a vector: $\begin{pmatrix}1&2&3&1\end{pmatrix} - \begin{pmatrix}0&3&1&1\end{pmatrix} = \begin{pmatrix}-1&-1&2&0\end{pmatrix}$
- adding two vectors produces a vector: $\begin{pmatrix}1&2&3&0\end{pmatrix} + \begin{pmatrix}0&3&1&0\end{pmatrix} = \begin{pmatrix}1&5&4&0\end{pmatrix}$
Coordinates with a $w$ value other than 0 or 1 also represents a point. Example:
- $\begin{pmatrix}1&2&3&1\end{pmatrix} = \begin{pmatrix}2&4&6&2\end{pmatrix} = \begin{pmatrix}-3&-6&-9&-3\end{pmatrix}$
When $w=1$, the representation is called **canonical representation**:
$\begin{pmatrix}x\cr y\cr z\cr w\end{pmatrix} = \begin{pmatrix}\frac{x}{w}\cr \frac{y}{w}\cr \frac{z}{w}\cr 1\end{pmatrix} = \begin{pmatrix}\ x\cr y\cr z\end{pmatrix}$
