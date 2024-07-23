---
layout: page
permalink: /design_pattern/behavioral_pattern/type_object
title: Type object
---

Page dedicated to knowledge related to [type object design pattern described by by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/type-object.html).

## My understanding

The idea is that **an object represents different conceptual type.**

For example, in the following code, we have a ```Monster``` interface and a ```Dragon``` class inheriting from ```Monster```.

```cpp
// monster interface
class Monster
{
public:
  virtual ~Monster() {}
  virtual const char* getAttack() = 0;

protected:
  Monster(int startingHealth)
  : health_(startingHealth)
  {}

private:
  int health_;
};
// Dragon inheriting from Monster
class Dragon : public Monster
{
public:
  Dragon() : Monster(230) {}

  virtual const char* getAttack()
  {
    return "The dragon breathes fire!";
  }
};
```

If suddenly:
* I need to create thousand new ```Monster``` type, it will take time to recompile.
* I do not know what type of ```Monster``` I need to handle.

The idea is moving from inheritance to type object by defining an object composition handling all the cases, from this:
![Subclass](/wiki/assets/design_pattern/behavioral_pattern/type_object/type-object-subclasses.png)
To this:
![Type object](/wiki/assets/design_pattern/behavioral_pattern/type_object/type-object-breed.png)

```cpp
// a breed type
class Breed
{
public:
  Breed(int health, const char* attack)
  : health_(health),
    attack_(attack)
  {}
  // instanciate once a breed called "Dragon" to define a dragon and give the reference
  // if you need to create a new type, you instanciate a new object
  // to create a monster, you call the breed with newMonster
  // similar to factory?
  Monster* newMonster() { return new Monster(*this); }
  int getHealth() { return health_; }
  const char* getAttack() { return attack_; }

private:
  int health_; // Starting health.
  const char* attack_;
};
class Monster
{
  // breed can access private fields
  friend class Breed;

public:
  const char* getAttack() { return breed_.getAttack(); }

private:
  // constructor is private
  Monster(Breed& breed)
  : health_(breed.getHealth()),
    breed_(breed)
  {}

  int health_;
  Breed& breed_;
};
// instanciate a new monster
Monster* monster = someBreed.newMonster();
```

## Sharing data through inheritance

```cpp
class Breed
{
public:
  // specify the parent on instanciation
  Breed(Breed* parent, int health, const char* attack)
  : parent_(parent),
    health_(health),
    attack_(attack)
  {}

  int         getHealth();
  const char* getAttack();

private:
  // breed has a parent
  Breed*      parent_;
  int         health_;
  const char* attack_;
};
// define the constructor
Breed(Breed* parent, int health, const char* attack)
: health_(health),
  attack_(attack)
{
  // inherit non-overridden attributes.
  if (parent != NULL)
  {
    if (health >= 0) health_ = parent->getHealth();
    if (attack == NULL) attack_ = parent->getAttack();
  }
}
```

You cade then have a JSON file defining different breed:. No need to recompile the code to change a breed, just changing the JSON file:
```json
{
  "Troll": {
    "health": 25,
    "attack": "The troll hits you!"
  },
  "Troll Archer": {
    "parent": "Troll",
    "health": 0,
    "attack": "The troll archer fires an arrow!"
  },
  "Troll Wizard": {
    "parent": "Troll",
    "health": 0,
    "attack": "The troll wizard casts a spell on you!"
  }
}
```

## Is the type object encapsulated or exposed?

In our sample implementation, Monster has a reference to a breed, but it doesn’t publicly expose it. Outside code can’t get directly at the monster’s breed. We can expose or encapsulate the breed, as following:

```cpp
const char* Monster::getAttack()
{
  if (health_ < LOW_HEALTH)
  {
    return "The monster flails weakly.";
  }

  return breed_.getAttack();
}
```