---
layout: page
permalink: /netcode/networked_physics/deterministic_lockstep
title: Deterministic lockstep
---

Summary of [Deterministic lockstep - Glenn Fielder](https://gafferongames.com/post/deterministic_lockstep/).

### Basic idea

Deterministic lockstep is a method of networking a system from one computer to another by sending only the inputs that control that system, rather than the state of that system. In the context of networking a physics simulation, this means we send across a small amount of input, while avoiding sending state like position, orientation, linear velocity and angular velocity per-object.

Benefit:
- bandwidth is proportional to the size of the input

Drawback:
- most physics simulation are not deterministic

### Determinism

Determinism means that given the same initial condition and the same set of inputs your simulation gives exactly the same result. And I do mean exactly the same result.

### Implementation

Packet sent over the network

```cpp
// 6 bit
struct Input
{
    bool left;
    bool right;
    bool up;
    bool down;
    bool space;
    bool z;
};
```

### Playout delay buffer

The problem with determinism is that you need reliable packets. If you need reliable packets you need to use TCP. But if you use TCP, when you lose a packet you lose time to resend the packet. The latency will be visible within your game.

### Can we do better than TCP?

Try to send the history of all your inputs until you receive a ACK from your receiver. This ACK is used as a baseline for the next history.







