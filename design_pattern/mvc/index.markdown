---
layout: page
permalink: /design_pattern/mvc/
---
## MVC
Page dedicated to knowledge related to MVC Model View Controller

### **Introduction**
The Model-View-Controller (MVC) is an architectural pattern that separates an application into three main logical components: the model, the view, and the controller.

#### Models
- Hold the application’s core data and state, such as player health or gun ammo.
- Serialize, deserialize, and/or convert between types.
- Load/save data.
- Notify Controllers of the progress of operations.
- Store the Game State for the Game’s Finite State Machine.
- Never access Views.

#### Views
- Can get data from Models in order to represent up-to-date game state to the user. For example, a View method player.Run() can internally use model.speed to manifest the player abilities.
- Should never mutate Models.
- Strictly implements the functionalities of its class. For example:
    - A PlayerView should not implement input detection or modify the Game State.
    - A View should act as a black box that has an interface and notifies of important events.
    - Does not store core data (like speed, health, lives,…).

#### Controllers
- Do not store core data.
- Can sometimes filter notifications from undesired Views.
- Update and use the Model’s data.
- Manages Unity’s scene workflow.

### **My understanding**

#### Model-View-Controller
MVC works with notification.
- **Views** holds what the user view. Furthermore, it holds the different node of an entity. The view is responsible to call notification when an event happens, for example ```OnCollisionEnter```. View is also responsible to play sound, play animation, ect..
- **Models** holds the data for the current entity. For example, if the entity has life, create a model that holds the life variable.
- **Controllers** holds the logic of the entity. Controllers listen to notification and change the data within the model.

#### Isolation principles
Ideally a MVC is independent from one to another. Each MVC can work independently. For instance:
- MVC responsible for life: A view holding the collision, a model holding the life data and a controller modifying the life data on collision.
- MVC responsible for movement: A view holding the body, a model holding the speed and weight and a controller modifying the location of the body.


