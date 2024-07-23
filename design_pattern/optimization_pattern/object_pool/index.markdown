---
layout: page
permalink: /design_pattern/optimization_pattern/object_pool
title: Object pool
---

Page dedicated to knowledge related to [object pool design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/object-pool.html) pattern.

## Intent

Improve performance and memory use by reusing objects from a fixed pool instead of allocating and freeing them individually.

## Motivation

### The curse of fragmentation

Fragmentation means the free space in our heap is **broken into smaller pieces of memory instead of one large open block**. The total memory available may be large, but the largest contiguous region might be painfully small.

![Object pool heap fragmentation](/wiki/assets//design_pattern/optimization_pattern/object_pool/object-pool-heap-fragment.png)

### Best of both worlds

Games are very careful about when and how they manage memory. A simple solution is often best — grab a big chunk of memory when the game starts, and don’t free it until the game ends. But this is a pain for systems where we need to create and destroy things while the game is running. An object pool gives us the best of both worlds. To the memory manager, we’re just allocating one big hunk of memory up front and not freeing it while the game is playing. To the users of the pool, we can freely allocate and deallocate objects to our heart’s content.

## The pattern

Define a pool class that maintains a collection of reusable objects. Each object supports an “in use” query to tell if it is currently “alive”.

## When to use it

This pattern is used widely in games for obvious things like game entities and visual effects, but it is also used for less visible data structures such as currently playing sounds. Use Object Pool when:

- You need to frequently create and destroy objects.
- Objects are similar in size.
- Allocating objects on the heap is slow or could lead to memory fragmentation.
- Each object encapsulates a resource such as a database or network connection that is expensive to acquire and could be reused.

## Keep in mind

You normally rely on a garbage collector or new and delete to handle memory management for you. **By using an object pool, you’re saying, “I know better how these bytes should be handled.”**

- The pool may waste memory on unneeded objects;
- Only a fixed number of objects can be active at any one time. How to handle this:
    - tune the pool sizes;
    - do not create the object, for examples with particles;
    - kill an existing object, for examples with sounds;
- Memory size for each object is fixed;
- Reused objects are not automatically cleared;
- Unused objects will remain in memory.

## Sample code

```cpp
// create a new particle from the pool
void ParticlePool::create(double x, double y,
                          double xVel, double yVel,
                          int lifetime)
{
  // for every particles
  for (int i = 0; i < POOL_SIZE; i++)
  {
    // find one not in used
    if (!particles_[i].inUse())
    {
        // init it to the new place
        particles_[i].init(x, y, xVel, yVel, lifetime);
        // return
        return;
    }
  }
}
```

### We can avoid waste time finding free particles

```cpp
class Particle
{
public:
  // a particle stores the next particle

  Particle* getNext() const { return state_.next; }
  void setNext(Particle* next) { state_.next = next; }

private:
  int framesLeft_;

  union
  {
    // State when it's in use.
    struct
    {
      double x, y;
      double xVel, yVel;
    } live;

    // State when it's available.
    Particle* next;
  } state_;
};
class ParticlePool
{
  // a pool stores the first available
private:
  Particle* firstAvailable_;
};
// when you create the pool you set setNext for each particle
ParticlePool::ParticlePool()
{
  // The first one is available.
  firstAvailable_ = &particles_[0];

  // Each particle points to the next.
  for (int i = 0; i < POOL_SIZE - 1; i++)
  {
    particles_[i].setNext(&particles_[i + 1]);
  }

  // The last one terminates the list.
  particles_[POOL_SIZE - 1].setNext(NULL);
}
// when you create a particle you change firstAvailable_
void ParticlePool::create(double x, double y,
                          double xVel, double yVel,
                          int lifetime)
{
  // Make sure the pool isn't full.
  assert(firstAvailable_ != NULL);

  // Remove it from the available list.
  Particle* newParticle = firstAvailable_;
  firstAvailable_ = newParticle->getNext();

  newParticle->init(x, y, xVel, yVel, lifetime);
}
// the particle pool animates all the particles
// that way it keeps track of which particle has ended
void ParticlePool::animate()
{
  for (int i = 0; i < POOL_SIZE; i++)
  {
    // if done animating
    if (particles_[i].animate())
    {
      // add this particle to the front of the list.
      particles_[i].setNext(firstAvailable_);
      firstAvailable_ = &particles_[i];
    }
  }
}
```

## Design decisions

### Are objects coupled to the pool?

- objects are coupled to the pool:
    - implementation is simpler;
    - ensure that object can only be created by the pool (by making the class a friend of the pool in C++ and private constructor).
- objects are not coupled to the pool:
    - objects of any type can be pooled;
    - in use state must be tracked outside the objects.

### What is responsible for initializing the reused objects?

- the pool reinitializes internally:
    - the pool can completely encapsulate its objects;
    - pool is tied to how objects are initialized.
- outisde code initializes the objects:
    - pool interface can be simpler;
    - outside code may need to handle the failure to create a new object.













