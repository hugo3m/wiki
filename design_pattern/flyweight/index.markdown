---
layout: page
permalink: /design_pattern/flyweight/
title: Flyweight
---

Page dedicated to knowledge related to [Flyweight design pattern](https://gameprogrammingpatterns.com/flyweight.html).

### In my own words
Flyweight is the idea of separating generic data common for every instance of an object, called **instrinsic state**, such as the color and texture, from the data specific for each instance, such as location, called **extrinsic state**.

### Basic idea
Flyweight, like its name implies, comes into play when you have objects that need to be more lightweight, generally because you have too many of them.
```cpp
// ===== a concrete example of a Tree storing too much data ===================
class Tree
{
private:
  Mesh mesh_;
  Texture bark_;
  Texture leaves_;
  Vector position_;
  double height_;
  double thickness_;
  Color barkTint_;
  Color leafTint_;
};
```
You can separate the Tree class into two different classes.
```cpp
// ===== a class storing common data for every tree - instrinsic ==============
class TreeModel
{
private:
  Mesh mesh_;
  Texture bark_;
  Texture leaves_;
};

// ===== a class storing specific data for each tree - extrinsic ==============
class Tree
{
private:
  TreeModel* model_;

  Vector position_;
  double height_;
  double thickness_;
  Color barkTint_;
  Color leafTint_;
};
```
This helps for storing stuff in main memory, but that doesn’t help rendering. To minimize the amount of data we have to push to the GPU, we want to be able to send the shared data — the TreeModel — just once. Then, separately, we push over every tree instance’s unique data — its position, color, and scale. Finally, we tell the GPU, “Use that one model to render each of these instances.”
![Flyweight tiles](flyweight-tiles.png)
```cpp
// ===== define the intrinsic attribute of a terrain ==========================
class Terrain
{
public:
  Terrain(int movementCost,
          bool isWater,
          Texture texture)
  : movementCost_(movementCost),
    isWater_(isWater),
    texture_(texture)
  {}

private:
  int movementCost_;
  bool isWater_;
  Texture texture_;
};
// ===== world holds the different type of terrain ============================
class World
{
public:
  World()
  : grassTerrain_(1, false, GRASS_TEXTURE),
    hillTerrain_(3, false, HILL_TEXTURE),
    riverTerrain_(2, true, RIVER_TEXTURE)
  {}

private:
  // each terrain is just a classic terrain with different attribute
  Terrain grassTerrain_;
  Terrain hillTerrain_;
  Terrain riverTerrain_;
  // world holds reference to where is each terrain
  Terrain* tiles_[WIDTH][HEIGHT];
};
// ===== then you create terrain by redirecting to the extrinsic existing instance
void World::generateTerrain()
{
  for (int x = 0; x < WIDTH; x++)
  {
    for (int y = 0; y < HEIGHT; y++)
    {
      if (random(10) == 0)
      {
        tiles_[x][y] = &hillTerrain_;
      }
      else
      {
        tiles_[x][y] = &grassTerrain_;
      }
    }
  }
}
```


