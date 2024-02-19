---
layout: page
title: Observer
permalink: /design_pattern/observer/
---
Page dedicated to knowledge related to [Observer](https://gameprogrammingpatterns.com/observer.html).

## In my own words

Observer pattern is for letting one piece of code announce that something interesting happened without actually caring who receives the notification.

## Basic

```cpp
// ===== Defining an Observer class observing a subject =======================
class Observer
{
public:
  virtual ~Observer() {}
  virtual void onNotify(const Entity& entity, Event event) = 0;
};
// ===== Subject being observed ===============================================
class Subject
{
// ----- The subject has a list of observers ----------------------------------
private:
  Observer* observers_[MAX_OBSERVERS];
  int numObservers_;
// ----- You can publicly add or remove observers from the subject ------------
public:
  void addObserver(Observer* observer)
  {
  }
  void removeObserver(Observer* observer)
  {
  }
// ----- The subject will notify all his observers ----------------------------
protected:
  void notify(const Entity& entity, Event event)
  {
    for (int i = 0; i < numObservers_; i++)
    {
      observers_[i]->onNotify(entity, event);
    }
  }
};
```
The Observer pattern gets a particularly bad rap here because it’s been known to hang around with some shady characters named “events”, “messages”, and even “data binding”. But, now that you’ve seen how the pattern is actually implemented, you know that isn’t the case. Sending a notification is simply walking a list and calling some virtual methods.

My guideline for how to cope with this is pretty simple. If you often need to think about both sides of some communication in order to understand a part of the program, don’t use the Observer pattern to express that linkage. Prefer something more explicit.

