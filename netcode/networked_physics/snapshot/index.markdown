---
layout: page
permalink: /netcode/networked_physics/snapshot
title: Snapshot
---

Summary of [Snapshot - Glenn Fielder](https://gafferongames.com/post/snapshot_interpolation/).

### Basic idea

Capture a snapshot of all relevant state from the simulation, send them over the network and reconstruct a visual approximation of the simulation from the snapshot.

### Implementation

State sent over the network:

```cpp
struct CubeState
{
    bool interacting;
    vec3f position;
    quat4f orientation;
};
```

The cost if this technique is increased bandwidth usage. And even though we sent one packet per frame, the internet does not guarantee that packets sent 60 times per-second are received 1/60 of a second apart. Because of jitter, sometimes you receive two snapshot, sometimes none.

### Jitter and hitches

How to succeed against jitter?

#### Linear interpolation

Instead of immediately rendering snapshot data, we buffer snapshots for a short amount of time in an interpolation buffer. The buffer holds snapshots for a period of time so that when you render the snapshot, you are already aware of the next one. Then you can perform interpolation between two slightly delated snapshots, providing illusion of smoothness.

#### Hermite interpolation

Why not upgrading to a more accurate interpolation scheme? Unlike other splines with control points that affect the curve indirectly, the hermite spline is guaranteed to pass through the start and end points while matching the start and end velocities. The drawback is that bandwidth has increased slightly because we need to include linear velocity with each cube in the snapshot.

### Real world conditions

Adding 350 ms delay for the jitter buffer is a lot. One technique that people often use to hide the delay added by the interpolation is extrapolation. But in Glenn Fielder experience, extrapolation doesn’t work very well for rigid bodies because their motion is non-linear and unpredictable.

## Snapshot compression

Summary of [Snapshot compression - Glenn Fielder](https://gafferongames.com/post/snapshot_compression/).

Let's have a look at the per-cube state sent in the snapshot:

```cpp
// 321 bits
struct CubeState
{
    // one bit
    bool interacting;
    // 96 bits (3 * 32)
    vec3f position;
    // 96 bits (3 * 32)
    vec3f linear_velocity;
    // 128 bits (4 * 32)
    quat4f orientation;
};
```

### Optimizing orientation

How to compress quaternion:

For a quaternion, because it represents a rotation and is normed:

```
x^2 + y^2 + z^2 + w^2 = 1
```

We can then drop one component. You might think you need to send a sign bit for w negative, but you don't, because you can make w always positive by negating the entier quaternion, knowing that in quaternion space:

```
(x,y,z,w) = (-x, -y, -z, -w)
```

However, do not always drop the same component due to numerical precision issues.

One filan improvement. If v is the absolute value of the largest component of the quaternion, the next largest possible component value occurs when two components have the same absolute value, and the other two are zero.

```
(v, v, 0, 0)

v^2 + v^2 = 1
=> v = 1/sqrt(2)
```

This means you can encode the smallest three components in [-0.707107,+0.707107] instead of [-1,+1] giving you more precision with the same number of bits.

### Optimizing linear velocity

1. Bound
2. Quantize

To compress linear velocity we need to bound its x,y,z components in some range so we don’t need to send full float values. Let's talk a maximum speed of 32 meters per-second. Linear velocity has been bounded and quantized and is now three integers in the range [-1024,1023]. That breaks down as follows: [-32,+31] (6 bits) for integer component and multiply 5 bits fraction precision.

### Optimizing position

1. Bound
2. Quantize

Same trick we used for linear velocity: bound and quantize. I chose a position bound of [-256,255] meters in the horizontal plane (xy) and since in the cube simulation the floor is at z=0, I chose a range of [0,32] meters for z. With experimentation I found that 512 values per-meter (roughly 2mm precision) provides enough precision. This gives position x and y components in [-131072,+131071] and z components in range [0,16383]. That’s 18 bits for x, 18 bits for y and 14 bits for z giving a total of 50 bits per-position (originally 96).

### Delta compression

Delta compression is a way for signaling that the state has not changed between updates. The most important is on both side to agree: what was the last update we agreed on? (called baseline). The receiver has to continually send "ACK" packets. The next time a packet is sent out the snapshot is encoded relative to this more recent baseline.








