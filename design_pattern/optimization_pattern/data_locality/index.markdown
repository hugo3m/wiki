---
layout: page
permalink: /design_pattern/optimization_pattern/data_locality
title: Data locality
---

Page dedicated to knowledge related to [data locality design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/data-locality.html) pattern.

## Intent

Accelerate memory access by arranging data to take advantage of CPU caching.

## How CPU works

For a **CPU**, accessing data of the **RAM** through the **bus** is very slow. That is why there is **CPU caching**. Whenevert the chip needs a byte from RAM, it grabs with it a bigger part - 64 to 128 bytes - and puts it in cache called **cache line**. If the next byte of data you need happens to be in that chunk, the CPU reads it straight from the cache, which is much faster than hitting RAM

## Data is performace

Two programs do the exact same computation. The only difference was how many cache misses they caused. The slow one was **fifty times slower** than the other. **The way you organize data directly impacts performance.**

## The pattern

Modern CPUs have **caches to speed up memory access.** These can **access memory adjacent to recently accessed memory much quicker.** Take advantage of that to **improve performance by increasing data locality** — keeping data in contiguous memory in the order that you process it.

## When to use it

Use it when you have a performance problem. Make sure that performance problems are caused by cache misses.

## Keep in mind

One of the hallmarks of software architecture is abstraction. In object-oriented languages, this almost always means interfaces. In C++, using interfaces implies accessing objects through pointers or references. But going through a pointer means hopping across memory, which leads to the cache misses this pattern works to avoid. To please this pattern, you will have to sacrifice some of your precious abstractions.

## Design decisions

### How do you handle polymorphism
- Do not:
    - safe and easy;
    - faster;
    - inflexible.
- Use separate arrays for each type:
    - kepps objects tighly packed;
    - statically dispatch;
    - you have to keep track of a bunch of collections;
    - you have to be aware of every type.
- Collections of pointers:
    - flexible;
    - less cache friendly.

### How are game entities defined
- Game entities are classes with pointers to their components:
    - you can store components in contiguous arrays;
    - given an entity, easy access to its components;
    - moving components in memory is hard.
- Game entities are classes with IDs for their components:
    - more complex;
    - slower;
    - need access to the component "manager".
- Game entity is itself just an ID:
    - entities are tiny;
    - entities are empty;
    - do not have to manage entities lifetime;
    - looking up for a component of an entity may be slow.






