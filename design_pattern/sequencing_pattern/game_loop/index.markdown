---
layout: page
permalink: /design_pattern/sequencing_pattern/game_loop
---

## Game loop
Page dedicated to knowledge related to [game loop](https://gameprogrammingpatterns.com/game-loop.html).

### Basic understanding

**A game loop processes user input, but doesn’t wait for it. The loop always keeps spinning.**

```cpp
// basic game loop
while (true)
{
  // handles user input since last call
  processInput();
  // update game simulation one step forward, such as physics
  update();
  // draws the game
  render();
}
```

#### Seconds per seconds

If we measure how quickly the game loop cycles in terms of real time, we get the game’s “frames per second”. Two factors determine the frame rate:
* How much work it has to do each frame. Complex physics, a bunch of game objects, and lots of graphic detail all will keep your CPU and GPU busy, and it will take longer to complete a frame.
* The speed of the underlying platform. Faster chips churn through more code in the same amount of time. Multiple cores, GPUs, dedicated audio hardware, and the OS’s scheduler all affect how much you get done in one tick.

**A game loop runs the game at a consistent speed despite differences in the underlying hardware.**

#### The pattern

The loop we’re talking about here is some of the most important code in your game. They say a program spends 90% of its time in 10% of the code. Your game loop will be firmly in that 10%. Take care with this code, and be mindful of its efficiency.

##### One small step, one giant step

The idea then is to choose a time step to advance based on how much real time passed since the last frame. The longer the frame takes, the bigger steps the game takes. It always keeps up with real time because it will take bigger and bigger steps to get there. They call this a variable or fluid time step. **In my own words: to keep a feeling of constant loop, if the previous loop took long to execute, instead of taking a small step for the next loop, we take a bigger one to keep up with *real* time.**
```cpp
double lastTime = getCurrentTime();
while (true)
{
  // retrieve current time
  double current = getCurrentTime();
  // calculate elapsed time
  double elapsed = current - lastTime;
  processInput();
  // give the elapse time to the update
  // we determine how much real time passed since
  // the engine is responsible for advancing the game world forward by that amount of time
  update(elapsed);
  render();
  lastTime = current;
}
```

**However, the game loop is now non-deterministic and unstable.**

##### Play catch up
```cpp
double previous = getCurrentTime();
double lag = 0.0;
while (true)
{
  // get current time
  double current = getCurrentTime();
  // get elapsed time
  double elapsed = current - previous;
  // store time for next iteration
  previous = current;
  // add to lag the elapsed time
  lag += elapsed;

  processInput();
// while lag is above ms update
  while (lag >= MS_PER_UPDATE)
  {
    // update
    update();
    // decrement lag
    lag -= MS_PER_UPDATE;
  }
  // AND THEN render
  render();
}
```

Note that the time step here isn’t the visible frame rate anymore. MS_PER_UPDATE is just the granularity we use to update the game. **The shorter this step is, the more processing time it takes to catch up to real time.**

##### Stuck in the middle

There’s one issue we’re left with, and that’s residual lag. We update the game at a fixed time step, but we render at arbitrary points in time. Conveniently, we actually know exactly how far between update frames we are when we render: it’s stored in lag. We bail out of the update loop when it’s less than the update time step, not when it’s zero. That leftover amount? That’s how far into the next frame we are.
```cpp
render(lag / MS_PER_UPDATE);
```

##### How do you control gameplay speed?

A game loop has two key pieces: non-blocking user input and adapting to the passage of time.








