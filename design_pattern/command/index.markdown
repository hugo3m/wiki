---
layout: page
title: Command
permalink: /design_pattern/command/
---
Page dedicated to knowledge related to [Command design pattern](https://gameprogrammingpatterns.com/command.html).

## In my own words
Command design pattern is a design pattern for encapsulating command function. The most interesting case is when storing a state for allowing undo and redo actions.

## Basic idea
From the Gang of Four: **Commands are an object-oriented replacement for callbacks.**

```cpp
// ===== An interface command defining command methods ========================
class Command
{
public:
  virtual ~Command() {}
  // pass the actor on which the command must be executed
  virtual void execute(GameActor& actor) = 0;
};

// ===== A function returning command according to input ======================
Command* InputHandler::handleInput()
{
  if (isPressed(BUTTON_X)) return buttonX_;
  if (isPressed(BUTTON_Y)) return buttonY_;
  if (isPressed(BUTTON_A)) return buttonA_;
  if (isPressed(BUTTON_B)) return buttonB_;

  // Nothing pressed, so do nothing.
  return NULL;
}

// ===== Retrieve command and execute it on specific actor ====================
Command* command = inputHandler.handleInput();
if (command)
{
  command->execute(actor);
}
```

We can let the player control any actor in the game now by changing the actor we execute the commands on.

## Undo and Redo

```cpp
// ===== Add undo method ======================================================
class Command
{
public:
  virtual ~Command() {}
  virtual void execute() = 0;
  virtual void undo() = 0;
};

// ===== Now the command stores previous and next state =======================
class MoveUnitCommand : public Command
{
public:
  MoveUnitCommand(Unit* unit, int x, int y)
  : unit_(unit),
    xBefore_(0),
    yBefore_(0),
    x_(x),
    y_(y)
  {}

  virtual void execute()
  {
    // retrieve current position
    xBefore_ = unit_->x();
    yBefore_ = unit_->y();
    // move to next position
    unit_->moveTo(x_, y_);
  }

  virtual void undo()
  {
    unit_->moveTo(xBefore_, yBefore_);
  }

private:
  // unit of which the command was run
  Unit* unit_;
  // previous state
  int xBefore_, yBefore_;
  // next state
  int x_, y_;
};
```

