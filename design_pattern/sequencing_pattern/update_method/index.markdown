---
layout: page
permalink: /design_pattern/sequencing_pattern/update_method
title: Update method
---

Page dedicated to knowledge related to [update method design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/update-method.html).

## Basic

Each entity in the game should encapsulate its own behavior. This pattern, along with **[Game Loop](/wiki/design_pattern/sequencing_pattern/game_loop)** and Component, is part of a **trinity** that often forms **the nucleus of a game engine**.

## The pattern

The game world maintains a collection of objects. Each object implements an update method that simulates one frame of the object’s behavior. Each frame, the game updates every object in the collection.

Important notes:
- **Deactivate update when not needed *(Example of a game of chess)*.**
- **Objects all simulate each frame but are not truly concurrent.**
- **Be careful modifying the object list while updating.**
- **This pattern lets us separate populating the game world from implementing it.**

![Update method UML](update-method-uml.png)

## Composition vs inheritance

When the game industry emerged from the primordial seas of 6502 assembly code and VBLANKs onto the shores of object-oriented languages, developers went into a software architecture fad frenzy. One of the biggest was using inheritance. Towering, Byzantine class hierarchies were built, big enough to blot out the sun.

It turns out that was a terrible idea and no one can maintain a giant class hierarchy without it crumbling around them. Even the Gang of Four knew this in 1994 when they wrote:

**Favor ‘object composition’ over ‘class inheritance’.**

## What class does the update method live on?
- The entity class: Simplest option but having to subclass entity everytime is painful.
- The component class: It lets each component update itself independently. In the same way that the Update Method pattern in general lets you **decouple game entities from each other** in the game world, this lets you **decouple parts of a single entity** from each other.
- A delegate class: Other patterns involve delegating part of a class behavior to another object, like [state pattern](/wiki/design_pattern/state/).











