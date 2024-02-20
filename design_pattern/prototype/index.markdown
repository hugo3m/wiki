---
layout: page
permalink: /design_pattern/prototype/
---

## Prototype
Page dedicated to knowledge related to [Prototype](https://gameprogrammingpatterns.com/prototype.html).

### Note
**“object-oriented programming” lets you define “objects” which bundle data and code together.**

### In my own words
The key idea is that an object can spawn other objects similar to itself.

### Basic
```cpp
// ===== interface defining a spawner =========================================
class Spawner
{
public:
  virtual ~Spawner() {}
  virtual Monster* spawnMonster() = 0;
};
// ===== template class to spawn class of type T ==============================
template <class T>
class SpawnerFor : public Spawner
{
public:
  virtual Monster* spawnMonster() { return new T(); }
};
// ===== spawn a ghost class ==================================================
Spawner* ghostSpawner = new SpawnerFor<Ghost>();
```

Later on, we can use some JSON format to spawn class with specific values for attributes. The constructor or the spawner can have default values that can be overriden.
```json
{
  "name": "goblin grunt",
  "minHealth": 20,
  "maxHealth": 30,
  "resists": ["cold", "poison"],
  "weaknesses": ["fire", "light"]
}

{
  "name": "goblin wizard",
  "prototype": "goblin grunt",
  "spells": ["fire ball", "lightning bolt"]
}

{
  "name": "goblin archer",
  "prototype": "goblin grunt",
  "attacks": ["short bow"]
}
```


