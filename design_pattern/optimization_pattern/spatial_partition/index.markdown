---
layout: page
permalink: /design_pattern/optimization_pattern/spatial_partition
title: Spatial partition
---

Page dedicated to knowledge related to [spatial partition design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/spatial-partition.html) pattern.

## Intent

Efficiently locate objects by storing them in a data structure organized by their positions.

## Motivation

For example distance between every units in a battlefield:

```cpp
// handleMelee is o(n^2) i.e not good
void handleMelee(Unit* units[], int numUnits)
{
  for (int a = 0; a < numUnits - 1; a++)
  {
    for (int b = a + 1; b < numUnits; b++)
    {
      if (units[a]->position() == units[b]->position())
      {
        handleAttack(units[a], units[b]);
      }
    }
  }
}
```

But imagine we simplify the game and have a 1D battlefield instead of a 2D battlefield.
![spatial partition battle line](/wiki/assets/design_pattern/optimization_pattern/spatial_partition/spatial-partition-battle-line.png)
Then we just need to sort the array. The lesson here is: **if we store our objects in a data structure organized by their locations, we can find them much more quickly.**

## The pattern

For a set of objects, **each object has a position in space**. Store them in a **spatial data structure** organized by objects positions. Data structure **efficiently query for objects at or near a location. But when a position object change, update the spatial data structure.**

## Keep in mind

Spatial partitions exist to knock an O(n) or O(n²) operation down to something more manageable. But if n is small enough, it may not be worth the bother. Object that change position are harder to deal with because of the need to reorganize the data. Make sure the trade-off is worth it. Spatial partition also uses additional memory.

## Sample code

```cpp
// we have a grid
class Grid
{
public:
  Grid()
  {
    for (int x = 0; x < NUM_CELLS; x++)
    {
      for (int y = 0; y < NUM_CELLS; y++)
      {
        cells_[x][y] = NULL;
      }
    }
  }

  static const int NUM_CELLS = 10;
  static const int CELL_SIZE = 20;
private:
  Unit* cells_[NUM_CELLS][NUM_CELLS];
};
// unit are doubly linked lit i.e. has a previous and a next
Unit::Unit(Grid* grid, double x, double y)
: grid_(grid),
  x_(x),
  y_(y),
  prev_(NULL),
  next_(NULL)
{
  grid_->add(this);
}
// the grid handles attacks for each cell
void Grid::handleMelee()
{
  for (int x = 0; x < NUM_CELLS; x++)
  {
    for (int y = 0; y < NUM_CELLS; y++)
    {
      handleCell(cells_[x][y]);
    }
  }
}
// for each cell handle each unit
void Grid::handleCell(int x, int y)
{
  Unit* unit = cells_[x][y];
  while (unit != NULL)
  {
    // Handle other units in this cell.
    handleUnit(unit, unit->next_);

    // Also try the neighboring cells.
    if (x > 0 && y > 0) handleUnit(unit, cells_[x - 1][y - 1]);
    if (x > 0) handleUnit(unit, cells_[x - 1][y]);
    if (y > 0) handleUnit(unit, cells_[x][y - 1]);
    if (x > 0 && y < NUM_CELLS - 1)
    {
      handleUnit(unit, cells_[x - 1][y + 1]);
    }
    // next unit of the cell
    unit = unit->next_;
  }
}
// handle a unit
void Grid::handleUnit(Unit* unit, Unit* other)
{
  // for every unit in the cell
  while (other != NULL)
  {
    // if a unit is close enough
    if (distance(unit, other) < ATTACK_DISTANCE)
    {
      // attack and return
      handleAttack(unit, other);
      return;
    }

    other = other->next_;
  }
}
// moving a unit
void Grid::move(Unit* unit, double x, double y)
{
  // see which cell it was in.
  int oldCellX = (int)(unit->x_ / Grid::CELL_SIZE);
  int oldCellY = (int)(unit->y_ / Grid::CELL_SIZE);

  // see which cell it's moving to.
  int cellX = (int)(x / Grid::CELL_SIZE);
  int cellY = (int)(y / Grid::CELL_SIZE);

  unit->x_ = x;
  unit->y_ = y;

  // if it didn't change cells, we're done.
  if (oldCellX == cellX && oldCellY == cellY) return;

  // if unit had a previous neighbor
  if (unit->prev_ != NULL)
  {
    // change the next neighbor from itself to its own next
    unit->prev_->next_ = unit->next_;
  }
  // if unit had a next neighbor
  if (unit->next_ != NULL)
  {
    // change the previous neighbor from itself to its own previous
    unit->next_->prev_ = unit->prev_;
  }

  // if it's the head of a list
  if (cells_[oldCellX][oldCellY] == unit)
  {
    // remove it
    cells_[oldCellX][oldCellY] = unit->next_;
  }

  // add it back to the grid at its new cell.
  add(unit);
}
```

## Design decisions

### Is the partition hierarchical of flat?

- flat partition:
    - simpler;
    - memory usage is constant;
    - can be faster to update when object change position.
- hierarchical:
    - handle empty space more efficiently;
    - handles densely populated areas more efficiently.

## Does the partitioning depend on the set of objects?

- object-independent:
    - objects can be added incrementally;
    - objects can be moved quickly;
    - partitions can be imbalanced.
- the partition adapts to the set of objects:
    - ensure that partitions are balanced;
    - more efficient to partition an entire set of objects at once.
- the partition is object-independent, but hierarchy is object-dependent (quadtree: if the number of objects in a space exceeds threshhold, slice it. See image below):
    - objects can be added incrementally;
    - objects can be moved quickly;
    - partition are balanced.
![spatial partition quadtree](/wiki/assets/design_pattern/optimization_pattern/spatial_partition/spatial-partition-quadtree.png)

## Are objects only stored in the partition?

- if it is the only place objects are stored, it avoid the memory overhead anc complexity of two collections;
- if there is another collections for the objects, traversing all objects are faster.

## More about spatial partitioning structure













