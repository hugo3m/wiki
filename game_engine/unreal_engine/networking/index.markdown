---
layout: page
permalink: /game_engine/unreal_engine/networking
title: Unreal Engine Networking
---

In Unreal Engine, **replication** is the name for synchronizing data and procedure calls between clients and servers.

### [Replicate Actor properties](https://dev.epicgames.com/documentation/en-us/unreal-engine/replicate-actor-properties-in-unreal-engine?application_version=5.3)

```cpp
// define the property to replicate as well as the function to run when property is received
UPROPERTY(ReplicatedUsing = OnRep_ServerState)
FGoKartState ServerState;

// function called on the client
// perform an action each time the property is replicated
UFUNCTION()
void OnRep_ServerState();

UGoKartMovementReplicator::UGoKartMovementReplicator()
{

	PrimaryComponentTick.bCanEverTick = true;
    // set replication to true
	SetIsReplicated(true);
}

void UGoKartMovementReplicator::GetLifetimeReplicatedProps(TArray< FLifetimeProperty > & OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
    // specify property to replicated
	DOREPLIFETIME(UGoKartMovementReplicator, ServerState);
}
```

### [Remote Procedure Calls (RPCs)](https://dev.epicgames.com/documentation/en-us/unreal-engine/remote-procedure-calls-in-unreal-engine?application_version=5.3)

Functions called locally being executed remotely to one or more machines.

```cpp
// function call by a client to be executed on the server
UFUNCTION(Server, Reliable, WithValidation)
void Server_SendMove(FGoKartMove Move);

// defines the implementation of the function being executed remotly
void UGoKartMovementReplicator::Server_SendMove_Implementation(FGoKartMove Move)
{
}

// defines a validator according to the parameter
// return true for valid parameter
bool UGoKartMovementReplicator::Server_SendMove_Validate(FGoKartMove Move)
{
}
```


