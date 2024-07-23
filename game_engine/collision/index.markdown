---
layout: page
permalink: /game_engine/collision
title: Collision
---

## [Axis-aligned bounding box - AABB](https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_collision_detection)

AABB is the quickest algorithm to determine whether two entities are overlapping or not. It consists of wrapping entities in a non-rotated box (thus axis-aligned).

![AABB](/wiki/assets/game_engine/collision/aabb.png)

The axis-aligned constraint improves performance. The overlapping area between two non-rotated boxes can be checked with logical comparisons alone, whereas rotated boxes require additional trigonometric operations, which are slower to calculate.

![AABB adapt](/wiki/assets/game_engine/collision/aabb_adapt.gif)
