---
layout: page
permalink: /design_pattern/state/
title: State
---
Page dedicated to knowledge related to [State design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/state.html)

## In my own words
Object implementation for state machines. Quick sum up of an example of state machine in the drawing below.
![State machine](/wiki/assets/design_pattern/state/state.png)

## Use case
- You have an entity whose behavior changes based on some internal state.
- That state can be rigidly divided into one of a relatively small number of distinct options.
- The entity responds to a series of inputs or events over time.

## Basic
Define a state interface and a class for each state
```cpp
// ===== define singleton class filesystem ====================================
class FileSystem
{
public:
  // static method for retrieving the instance
  static FileSystem& instance()
  {
    // C++11 mandates that the initializer for a local static variable is only run once
    static FileSystem *instance = new FileSystem();
    // return the instance
    return *instance;
  }

private:
  // the constructor of the class is private
  FileSystem() {}
};
```

### When not to use singleton
Singleton are often use as manager. For example here, BulletManager could be singleton
```cpp
// ===== heroin state interface for every possible state ======================
class HeroineState
{
public:
  virtual ~HeroineState() {}
  virtual void handleInput(Heroine& heroine, Input input) {}
  virtual void update(Heroine& heroine) {}
};
// ===== implement a specific state inheriting from the state interface =======
class DuckingState : public HeroineState
{
public:
  DuckingState()
  : chargeTime_(0)
  {}

  virtual void handleInput(Heroine& heroine, Input input) {
    if (input == RELEASE_DOWN)
    {
      // Change to standing state...
      heroine.setGraphics(IMAGE_STAND);
    }
  }

  virtual void update(Heroine& heroine) {
    chargeTime_++;
    if (chargeTime_ > MAX_CHARGE)
    {
      heroine.superBomb();
    }
  }

private:
  int chargeTime_;
};
// ===== A class heroin is composed by a state ================================
class Heroine
{
public:
  virtual void handleInput(Input input)
  {
    state_->handleInput(*this, input);
  }

  virtual void update()
  {
    state_->update(*this);
  }

private:
  HeroineState* state_;
};
```

## Assigning a new state
```cpp
// ===== function returning a state ===========================================
HeroineState* StandingState::handleInput(Heroine& heroine,
                                         Input input)
{
  if (input == PRESS_DOWN)
  {
    return new DuckingState();
  }
  // stay in this state
  // return StadingState()
  return NULL;
}
// ===== function assigning a new state =======================================
void Heroine::handleInput(Input input)
{
  HeroineState* state = state_->handleInput(*this, input);
  if (state != NULL)
  {
    delete state_;
    state_ = state;
  }
}
```

### Enter and exit actions
```cpp
// ===== give the state an entry action =======================================
class StandingState : public HeroineState
{
public:
  virtual void enter(Heroine& heroine)
  {
    heroine.setGraphics(IMAGE_STAND);
  }
  // ...
};
```

### Concurrent state machine
```cpp
// ===== we can have two different state in one class =========================
class Heroine
{
  // ...
private:
  HeroineState* state_;
  HeroineState* equipment_;
};
void Heroine::handleInput(Input input)
{
  state_->handleInput(*this, input);
  equipment_->handleInput(*this, input);
}
```

## Hierarchical / nested state machines
```cpp
// ===== create a onground state ==============================================
class OnGroundState : public HeroineState
{
public:
  virtual void handleInput(Heroine& heroine, Input input)
  {
    if (input == PRESS_B)
    {
      // Jump...
    }
    else if (input == PRESS_DOWN)
    {
      // Duck...
    }
  }
};
class DuckingState : public OnGroundState
{
public:
  virtual void handleInput(Heroine& heroine, Input input)
  {
    if (input == RELEASE_DOWN)
    {
      // Stand up...
    }
    else
    {
      // Didn't handle input, so walk up hierarchy.
      OnGroundState::handleInput(heroine, input);
    }
  }
};
```

### Pushdown automata
Sometimes you want to store the history of states. For example, when a character is firing while running, you would like to fire and then recall the previous state running later.