---
layout: page
permalink: /computer_graphics/opengl
title: OpenGL
---

Page dedicated to knowledge related to rasterizing.

## Player controller

### Vertex array object **VAO** and vertex buffer object **VBO**

Vertex buffer object stores data, such as _int_. It needs to know what is the size of a single data and how many occurences of them it should store.

Vertex array object links a _VBO_ and the logic to read the data from it.

![vao-vb-relationship](/wiki/assets/computer_graphics/opengl/vao-vbo.png)
