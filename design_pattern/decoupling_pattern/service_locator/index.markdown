---
layout: page
permalink: /design_pattern/decoupling_pattern/service_locator
title: Service locator
---

Page dedicated to knowledge related to [service locator design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/service-locator.html).

## Intent

Provide a global point of access to a service without coupling users to the concrete class that implements it. Some systems are needed everywhere in the codebase and can be think as a **service**.

### Example

For example playing audio sound can be called anywhere in the game. We can have a singleton or a static class. But it is not good to give access to the audio implementation directly. This is the Service Locator pattern in a nutshell — it **decouples code that needs a service from both who it is (the concrete implementation type) and where it is (how we get to the instance of it).**

## The pattern

A **service class defines an abstract interface to a set of operations**. A concrete **service provider implements this interface**. A separate **service locator provides access to the service** by finding an appropriate provider while hiding both the provider’s concrete type and the process used to locate it.

## When to use it

**Sparingly**. Good examples are audio or logging. When used poorly, service locator carries with it all of the baggage of the Singleton pattern with worse runtime performance.

## Keep in mind

- The service actually has to be located;
- The service does not know who is locating it.

## Sample code

```cpp
// the service
class Audio
{
public:
  virtual ~Audio() {}
  virtual void playSound(int soundID) = 0;
  virtual void stopSound(int soundID) = 0;
  virtual void stopAllSounds() = 0;
};
// the service provider
class ConsoleAudio : public Audio
{
public:
  virtual void playSound(int soundID)
  {
    // Play sound using console audio api...
  }
};
// the service locator
class Locator
{
public:
  static void initialize() { service_ = &nullService_; }

  static Audio& getAudio() { return *service_; }

  static void provide(Audio* service)
  {
    if (service == NULL)
    {
      // revert to null service.
      service_ = &nullService_;
    }
    else
    {
      service_ = service;
    }
  }

private:
  static Audio* service_;
  // store a default nullService
  static NullAudio nullService_;
};
```

You can create a LoggedAudio that will log every call to an Audio object method.

## Design decisions

### How is the service located

- outside code registers it:
    - fast and simple;
    - control how the provider is constructed;
    - change the service while the game is running;
    - locator depends on outside code.
- bind to it at compile time:
    - fast;
    - guarantee the service is available;
    - can not change it.
- configure it at runtime:
    - swap out without recompiling;
    - complex;
    - locating the service takes time.

### If the service can not be located

- let the use handle it:
    - free to determine how to handle failure;
    - but must handle failure.
- halt the game:
    - do not need to handle a missing service.
- return null service:
    - users do not need to handle;
    - may be harder to debug an unintentionally missing service.

### The scope of the service

- global:
    - encourage all the codebase to access it;
    - lose control over who is using it.
- restricted:
    - control couping;
    - can lead to duplicates.

## Notes

- The Service Locator pattern is a sibling to Singleton in many ways, so it’s worth looking at both to see which is most appropriate for your needs.

- The Unity framework uses this pattern in concert with the Component pattern in its GetComponent() method.

