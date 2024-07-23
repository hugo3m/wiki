---
layout: page
permalink: /design_pattern/decoupling_pattern/event_queue
title: Event queue
---

Page dedicated to knowledge related to [event queue design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/event-queue.html).

## Intent

Decouple when a message or event is sent from when it is processed. A queue stores a series of notifications or requests in first-in, first-out order.

## The pattern

Events are stored in a **queue**. It is common for a game to have its own event queue. Event pattern allow to **run code from another thread than the caller's thread.**

## When to use it

If you **only want to decouple who receives a message from its sender**, patterns like [Observer](/wiki/design_pattern/observer/) and [Command](/wiki/design_pattern/command) will take care of this with less complexity. You **only need a queue when you want to decouple something *in time***.

## Keep in mind

Unlike some more modest patterns in this book, **event queues are complex** and tend to have a wide-reaching effect on the architecture of our games:
- a central event queue is a global variable;
- the state of the world can change;
- stuck in feedback loops.

## Sample code
In practice, **the best way to store a bunch of homogenous things is almost always a plain old array**.

## Ring buffer: a queue implementation

* The head is where request are read from
* The tail is the other end

![Event-queue-queue](/wiki/assets/design_pattern/decoupling_pattern/event_queue/event-queue-queue.png)
![Event-queue-crawl](/wiki/assets/design_pattern/decoupling_pattern/event_queue/event-queue-crawl.png)
![Event-queue-ring](/wiki/assets/design_pattern/decoupling_pattern/event_queue/event-queue-ring.png)

```cpp
// play sound function add event to the queue
void Audio::playSound(SoundId id, int volume)
{
  // add to the end of the list.
  pending_[tail_].id = id;
  pending_[tail_].volume = volume;
  // increase to next index
  // go back to beginning if max size reached
  tail_ = (tail_ + 1) % MAX_PENDING;
}
// update retrieve event from the queue
void Audio::update()
{
  // if there are no pending requests, do nothing.
  if (head_ == tail_) return;
  // retrieve the sound
  ResourceId resource = loadSound(pending_[head_].id);
  // trigger the event
  startSound(resource, pending_[head_].volume);
  // increase to next index
  // go back to beginning if max size reached
  head_ = (head_ + 1) % MAX_PENDING;
}
```

## Message vs event

- If you queue events: An **event** or **notification** describes **something that already happened**, like “monster died”.
    - sort of like an asynchronous Observer pattern;
    - allow multiple listeners;
    - the sender probably doesn’t care who receives it.
- If you queue messages: a **message** or **request** describe **an action that we want to happen**:
    - more likely to have a single listener.

## Who can read from the queue?
- A single-cast queue:
    - queue is encapsulated;
    - do not worry about contention between listeners.
- A broadcast queue. If you have ten listeners when an event comes in, all ten of them see the event:
    - may need to filter events.
- A work queue. Like a broadcast queue, here you have multiple listeners too. The difference is that **each item in the queue only goes to one of the listeners**. This is a common pattern for parceling out jobs to a pool of concurrently running threads:
    - you have to schedule.

## Who can write to the queue?
This pattern works with all of the possible read/write configurations: one-to-one, one-to-many, many-to-one, or many-to-many.

- One writer: this style is most similar to the synchronous [Observer](/wiki/design_pattern/observer) pattern.
- Multiple writers: any part of the codebase can add a request to the queue.

## What is the lifetime of the objects in the queue?

- **Pass ownership**: when a message gets queued, the queue claims it and the sender no longer owns it. **In C++, unique_ptr<T> gives the exact semantics**.
- **Share ownership**: The message sticks around as long as anything has a referemce to it. **In C++, it is shared_ptr<T>**.
- **The queues own its: Messages always live on the queue.


