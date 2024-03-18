---
layout: page
permalink: /design_pattern/singleton/
title: Singleton
---

Page dedicated to knowledge related to [Singleton design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/singleton.html).

### In my own words
Ensure a class has one instance, and provide a global point of access to it.

### Basic
Modern implementation of singleton
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
// ===== class Bullet =========================================================
class Bullet
{
public:
  int getX() const { return x_; }
  int getY() const { return y_; }

  void setX(int x) { x_ = x; }
  void setY(int y) { y_ = y; }

private:
  int x_, y_;
};
// ===== class BulletMagaer eligible for singleton since only need one ========
class BulletManager
{
public:
  Bullet* create(int x, int y)
  {
    Bullet* bullet = new Bullet();
    bullet->setX(x);
    bullet->setY(y);

    return bullet;
  }

  bool isOnScreen(Bullet& bullet)
  {
    return bullet.getX() >= 0 &&
           bullet.getX() < SCREEN_WIDTH &&
           bullet.getY() >= 0 &&
           bullet.getY() < SCREEN_HEIGHT;
  }

  void move(Bullet& bullet)
  {
    bullet.setX(bullet.getX() + 5);
  }
};
```
But the problem should be solved like this instead
```cpp
class Bullet
{
public:
  Bullet(int x, int y) : x_(x), y_(y) {}

  bool isOnScreen()
  {
    return x_ >= 0 && x_ < SCREEN_WIDTH &&
           y_ >= 0 && y_ < SCREEN_HEIGHT;
  }

  void move() { x_ += 5; }

private:
  int x_, y_;
};
```

### Use case
To ensure single instantiation, usually use a static class. If that doesn’t work, use a static flag to check at runtime that only one instance of the class is constructed.
