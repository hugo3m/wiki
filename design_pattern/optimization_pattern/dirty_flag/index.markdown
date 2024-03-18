---
layout: page
permalink: /design_pattern/optimization_pattern/dirty_flag
title: Dirty flag
---

Page dedicated to knowledge related to [dirty flag design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/dirty-flag.html) pattern.

## Intent

Avoid unnecessary work by deferring it until the result is needed.

## Motivation

Game have something called a **scene graph**, a big data structure that contains object in the world. The rendering engine uses this to determine where to draw stuff. As its simplets, scene graph is just a flat list of object with a transform (position, rotation, scale). To render, the rendering engine take the objects model and apply the transform to it. However, scene graphs are hierachical. An object in the graph may have a parent object.

*Transforms are stored and manipulated with a 4x4 matrix. You can make a single transform that combines two transforms — for example, translating and then rotating an object — by multiplying the two matrices.*

There are local and world transforms. In the degenerate case where the object has no parent, its local and world transforms are equivalent.

## The pattern

A set of **primary data** changes over time. A set of **derived data** is determined from this using some expensive process. A “dirty” flag tracks **when the derived data is out of sync with the primary data (i.e. is dirty).** For example in the world hierarchy, if you have an entity B child of entity A, you do not need to move entity B if entity A did not move. When entity A moves, you **set the dirty flag to true** on entity B **as a signal of out-of-sync**.

## Requirements

- The primary data has to change more often than the derived data is used;
- it should be hard to update incrementally.

## Keep in mind

- There is a cost to deferring for too long;
- You have to make sure to set the flag every time the state changes;
- You have to keep the previous derived data in memory.

## Sample code

```cpp
// a graphnode has children
// the very first parent get a render called by superior instance
// the superior instance call render with dirty = False
void GraphNode::render(Transform parentWorld, bool dirty)
{
  // dirty || dirty_ = True if one is dirty
  // keep current dirtieness into is_dirty
  was_dirty = dirty_ || dirty;
  if (was_dirty)
  {
    world_ = local_.combine(parentWorld);
    // object is not dirty anymore
    dirty_ = false;
  }

  if (mesh_) renderMesh(mesh_, world_);

  for (int i = 0; i < numChildren_; i++)
  {
    // transfer to children if the object was dirty
    children_[i]->render(world_, was_dirty);
  }
}
```

## Design decisions

### When is the dirt flag cleaned?
- when the result is needed:
  - avoid doing calculation entirely if the result is never used;
  - can cause noticeable pause if calculation is time-consuming.
- at well-defined checkpoint:
  - doing the work does not impact user experience;
  - lose control over when the work happens
- in the background:
  - tune how often work is performed;
  - can do more redundant work;
  - need support for async.

### How fine-grained is your dirty tracking?
- more fine-grained:
  - only process data that actually changed.
- more coarse-grained:
  - end up processing unchanged data;
  - less memory used;
  - less time spent on fixed overhead.






