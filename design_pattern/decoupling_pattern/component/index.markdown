---
layout: page
permalink: /design_pattern/decoupling_pattern/component
title: Component
---

Page dedicated to knowledge related to [component design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/component.html).

## Intent

Allow a single entity to **span multiple domains** without **coupling the domains** to each other.

## The pattern

**A single entity spans multiple domains.** To keep the domains isolated, the code for each is placed in its own component class. The entity is reduced to a simple container of components.

## When to use it

This pattern can be put to good use when any of these are true:
- A class touches multiple domains which needs to keep decoupled from each other.
- A class is getting massive and hard to work with.
- Define a variety of objects that share different capabilities, but using inheritance doesn’t let pick the parts that need to be reused precisely enough.

## Keep in mind

The Component pattern adds a good bit of complexity over simply making a class and putting code in it. Each conceptual “object” becomes **a cluster of objects that must be instantiated, initialized, and correctly wired together. Communication between the different components becomes more challenging**, and controlling how they occupy memory is more complex.

For a large codebase, this complexity may be worth it for the decoupling and code reuse it enables, but take care to ensure **you aren’t over-engineering a “solution” to a non-existent problem** before applying this pattern.

## How does the object get its components?
**Answer: outside code provides the components.**
- If the object creates its own components:
    - object always has the components it needs.
    - harder to reconfigure the object.
- If outside code provides the components:
    - object becomes more flexible.
    - object can be decoupled from the concrete component types.

## How does component communicate with each other?
**Answer: a mix of the three solutions.**
- **use a state**:
    - keeps the components decoupled.
    - it requires any information to be pushed to the container object.
    - it makes communication implicit and can mess the order of execution.
- **use references**:
    - simple and fast.
    - components are **coupled**.
- **use events/messages**:
    - components are decoupled.
    - the container object is simple.
    - more difficult to implement.

A mix of them for different purposes:

- State: **Shared state is useful for the really basic stuff that you can take for granted that every object has — things like position and size.**

- References: **Some domains are distinct but still closely related:** animation and rendering, user input and AI, physics and collision.

- Messaging: **useful for “less important” communication.** Its fire-and-forget nature is a good fit for things like having an audio component play a sound when a physics component sends a message that the object has collided with something.



