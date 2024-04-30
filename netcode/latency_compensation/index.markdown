---
layout: page
permalink: /netcode/latency_compensation
title: Latency compensation
---

Page dedicated to knowledge related to latency compensation.

## Intent

Latency compensation algorithms are algorithms who intend to compensate the network conditions to provide satisfying Quality of Experience (QoE).

## Motivation

- Dumb client: Single authoritative server that is responsible for running the main game logic and one or more dumbs clients. Clients are nothing more than a way to forward user inputs. First, the client creates and sends a user command to the server. The server then executes the user command and sends updated positions of everything back to client. Finally, the client renders the scene with all of these objects. The problem is that it takes too much time to execute every single little action in the game.
- Client side prediction:For ameliorating this problem, the client can perform actions locally and temporarily assume that the server will accept the commands. However, there is still an authoritative server running the simulation. If the client simulates different results than the server, the server's results will eventually correct the client’s incorrect simulation. the general procedure of client side prediction is: client inputs are sampled and a user command is generated. This user command is sent off to the server. Each user command (and the exact time it was generated) is stored on the client. The prediction algorithm uses these stored commands. For prediction, the last acknowledged movement from server is used as starting point. It indicates which user command was last acted upon by the server and state data simulated on the server. The last acknowledge command will be in the past (because of the time required to send the packet from the server to the client). \textit{For instance, if the client is running at 50 frames per second (5 frames per deciseconds) and has 100 milliseconds of latency (1 decisecond), then the client will have stored up five user commands ahead of the last one acknowledged by the server. These five user commands are simulated on the client as a part of client-side prediction.} Client side prediction create difference between state data on the client and on the server. For basic movement, the client side do not need to store data from a start state (since last acknowledged) to the final state. However, if there is logic totally client side that affects fields not replicated from the server to the client you need to store intermediate state. The idea is when the client is receiving an acknowledge, you copy needed data from stored actions on the client side to the new from state. Replicating the necessary fields to the client and handling all of the intermediate state is a fair amount of work. Why not eliminate all of the server stuff and just have the client report the actions? The answer is: cheating.

## Methods

### Extrapolation

#### Summary

Extrapolating is guessing where something will be at a time *t+1* knowing that it has a state (velocity, angle, ..) at a time *t*.

#### Explanation

The other player/object is simulated forward in time for last known spot, direction, velocity. If you are 100 milliseconds lagged and during the last update the other player was running 500 units per seconds, you can assume that the player is 50 units
in forward direction. However this is very non-deterministic and subject to high jerk, because in most games players can apply unrealistic forces to create huge accelerations and arbitrary angles.

### Interpolation

#### Summary

Overall, interpolating is simulating a behavior between *t1* and *t2* with *t2 > t1* knowing the state at *t1* and the state at *t2*.

#### Explanation

Interpolation can be viewed as always moving objects somewhat in the past with respect to the last valid position. You can think of interpolation as buffering an additional 100 milliseconds of data on the client. This type of interpolation requires a fixed time interval between server updates and suffers from visual quality issues. An-other method is to keep a "position history" for each object that might be interpolated. Each update receive from the server creates a new position history. To interpolate, we compute the target time (by substracting the interpolation time i.e. buffer time. Then we look for the pair of updates between this time and update the position accordingly.
Here is general algorithm about interpolation:
– Each update received by the client from the server contains a timestamp *t*
– First the client receives a first update at time *tc1* sent by the server at time *ts1*
– Then the client receives a second update at time *tc2* sent by the server at time *ts2*
– The client calculates a target time *tt = ts2 − I* where *I* is interpolation time
– If *tc1 < tt < tc2*, then the client can interpolate the position.

### Lag compensation

#### Summary

Lag compensation is looking a way of trying to satisfy client over the server state. When a client realizes an action, the server will confirm this action regarding the time at when the client realized the action. If the action has consequences on the present time, the server will perform them.

#### Explanation

Understanding interpolation is important for designing lag compensation. Lag compensation is a method of normalizing server-side the state of the world for each player as that player’s user commands are executed. Lag compensation is taking a step back in time and looking at the state of the world when the user performed an action. Here is the algorithm:
- Before executing current player *p1* command *c1*, sent by the client at a time tc and received by the server at time *ts* (of course *ts* > *tc*):
    – Computes latency for the player *p1*
    – Searches in the server history for the player *p1* the world *wtc* update that was sent and received at time *tc* just before the player executed the command *c1*
    – From *wtc* for each player pn move them backwards in time *tc* when the player *p1* submitted the command *c1*
- Execute the command *c1*
- Move back all players *pn*
Of course this behavior is a game design tradeoff: certain paradoxes and inconsistencies can occur. The inconsistencies occur from the point of view of players being fired. For instance, if a highly lagged player shoots at a less lagged player, it can appear that some-
how he shot around a corner. But this inconsistency is much less pronounced in normal combat situations. For Half-Life, Team Fortress and Counter Strike, the benefits of lag compensation easily outweighed the inconsistencies noted above.




