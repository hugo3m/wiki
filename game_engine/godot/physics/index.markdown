---
layout: default
permalink: /game_engine/godot/physics/
---
## Physics

### **Introduction**
If need to know when two objects in the game intersect or come into contact:
- collision detection
- collision response

Where processing physics related:
- A code that runs each frame is **idle processing**.
- A code that runs at fixed rate is **physics processing**.

Any code that needs to access body's properties should run within the [Node._physics_process](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-physics-process).

#### Shapes
A physics body can hold Shape objects as children to define collision bounds and contacts. **Never scale a shape in the editor. Always use the size handles**.

#### Collision layers and masks
- collision_layer: describe the layers that object appears in.
- collision_mask: describe what layers the body scan for collisions.

### **Navigation**
- [2D Physics](/wiki/game_engine/godot/physics/2d)
- [RigidBody](/wiki/game_engine/godot/physics/rigidbody)