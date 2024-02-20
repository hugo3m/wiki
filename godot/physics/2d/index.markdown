---
layout: default
permalink: /godot/physics/2d
---
## 2D Physics
### Collision objects

#### **Area2D**
Area2D provide detection and influence. Usage examples:
- Overriding physics parameters
- Detecting when bodies enter, exit (or who is currently in the region)
- Checking other areas for overlap

#### **StaticBody2D**
A static body is one not moved by the physics engine. It has collision detection, but does not move in response to collision. However it can forward motion and rotation to a colliding body [StaticBody2D](https://docs.godotengine.org/en/stable/tutorials/physics/physics_introduction.html#staticbody2d). StaticBody2D usage examples:

- Platforms
- Conveyor belts
- Walls (and other obstacles)

#### **RigidBody2D**
##### Introduction
A rigid body implement simulated physics. You do not control a rigid body directly. Instead, you apply forces and the physics engine calculates the resulting movement (collissions, bounciness, rotation...). You can modify a rigid body behavior by modifying properties "Mass", "Friction" or "Bounce". **When a rigid body hasn't moved or is at rest for a while it goes to sleep. Then, it acts like a static body. It will wake up when forces are applied.**

##### Usage
If you need to alter any of the physics-related properties, use [_integrate_forces()](https://docs.godotengine.org/en/stable/classes/class_rigidbody2d.html#class-rigidbody2d-private-method-integrate-forces) instead of ```_physics_process()```. **When a rigid body goes to sleep, ```_integrate_forces()``` will not be called. You either have to create collision, apply force or disable ```can_sleep```.**

##### Contacts
**By default, rigid body do not keep track of contacts.** To enable contact reporting, set the [max_contacts_reported](https://docs.godotengine.org/en/stable/classes/class_rigidbody2d.html#class-rigidbody2d-property-max-contacts-reported) property to a non-zero value. Contacts can be obtained via [PhysicsDirectBodyState2D.get_contact_count()](https://docs.godotengine.org/en/stable/classes/class_physicsdirectbodystate2d.html#class-physicsdirectbodystate2d-method-get-contact-count).

#### **CharacterBody2D**
Character body detect collisions but are not affected by physics properties like gravity or friction. Instead, they must be controlled by the user via code. To move a character body, use [move_and_collide()](https://docs.godotengine.org/en/stable/classes/class_physicsbody2d.html#class-physicsbody2d-method-move-and-collide) and [move_and_slide()](https://docs.godotengine.org/en/stable/classes/class_characterbody2d.html#class-characterbody2d-method-move-and-slide).