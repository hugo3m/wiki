---
layout: page
permalink: /design_pattern/sequencing_pattern/double_buffering
title: Double buffering
---

Page dedicated to knowledge related to [double buffering design pattern described by Robert Nystrom in the game programming patterns.](https://gameprogrammingpatterns.com/double-buffer.html).

### How computer graphics work briefly
In the example below, when drawing a smiley, we need to write to the **framebuffer**. In order to display the smiley, the computer will read value from the framebuffer. However, because the reading operation is faster than the writing operation, it will not display correctly the smiley. It is only displaying pixels before the writing operation was done.
![Double buffering tearing](/wiki/assets/design_pattern/sequencing_pattern/double_buffering/double-buffer-tearing.png)
We need a pattern because program renders the pixels one at a time, but we need the display driver to see them all at once.

### Basic understanding
The principle is to have **two framebuffers**, so that while one, is being displayed, the other one is being prepared to be displayed. We just need to **swap** buffers.

### Use case
- We have some state that is being modified incrementally.
- That same state may be accessed in the middle of modification.
- We want to prevent the code that’s accessing the state from seeing the work in progress.
- We want to be able to read the state and we don’t want to have to wait while it’s being written.

**The core problem that double buffering solves is state being accessed while it’s being modified.**




