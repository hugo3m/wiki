---
layout: page
permalink: /netcode/networked_physics/state_synchronization
title: State synchronization
---

Summary of [State Synchronization - Glenn Fielder](https://gafferongames.com/post/state_synchronization/).

### Basic idea

Simulation is runned on both sides. Both `input` and `state` are sent over the network.

- No need to perfect determinism to stay in sync.

### Implementation

Data sent over the network:

```cpp
struct StateUpdate
{
    int index;
    vec3f position;
    quat4f orientation;
    vec3f linear_velocity;
    vec3f angular_velocity;
};
```

Unlike snapshot interpolation, it is not only position and orientation that are sent but also non-visual state such as linear and angular velocity.

A look at the overall structure of the packets:

```cpp
const int MaxInputsPerPacket = 32;
const int MaxStateUpdatesPerPacket = 64;

struct Packet
{
    uint32_t sequence;
    Input inputs[MaxInputsPerPacket];
    int num_object_updates;
    StateUpdate state_updates[MaxStateUpdatesPerPacket];
};
```

- A sequence number to determine order
- Input for extrapolation

The maximum of state update per packet is set to 64. What happens if you need to update more than 64 objects? Priority.

#### Priority accumulator

It is an array of float values, one value per-object, remembered from frame to frame. Each frame we add the current priority of each object to its priority accumulator value, then sort. When a state has been sent over the network then decrease the priority.

#### Jitter buffer

On the receiving side, you need to deal with jitter, meaning that for one frame you could receive 2 packets and the next one none. The solution is to implement a jitter buffer. All you do in a jitter buffer is hold packets before delivering them to the application at the correct time as indicated by the sequence number (frame number) in the packet.

#### Applying state updates

Once the packet comes out of the jitter how do you apply state updates? My recommendation is that you should snap the physics state hard. This means you apply the values in the state update directly to the simulation.

#### Quantize both sides

On both side (client and server), before each simulation step, quantize the entier simulation state as if it has been transmitted over the network. Once this is done both sides are extrapolating from quantized state and therefore have similkar extrapolations.

#### Visual smoothing

#### Delta compression




