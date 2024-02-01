---
layout: default
title: Physics
permalink: /godot/physics/
---
## Introduction

- A code that runs each frame is **idle processing**.
- A code that runs at fixed rate is **physics processing**.

Any code that needs to access body's properties should run within the [Node._physics_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-physics-process).