---
layout: page
permalink: /game_engine/unreal_engine/controller/
title: Controller
---

## Overall overview

{% mermaid %}
classDiagram

    class AActor{
    }
    class INavAgentInterface {
    }
    class AController{
        -uint8 bAttachToPawn
        -uint8 bCanPossessWithoutAuthority
        -uint8 bIsPlayerController
        -FRotator ControlRotation
        -uint8 IgnoreLookInput
        -uint8 IgnoreMoveInput
        -FInstigatedAnyDamageSignature OnInstigatedAnyDamage
        -FPawnChangedSignature OnNewPawn
        -FOnPossessedPawnChanged OnPossessedPawnChanged
        -FName StateName

        +AddPawnTickDependency(APawn* NewPawn) void
        +AttachToPawn(APawn* InPawn) void
        +BeginInactiveState() void
        +ChangeState(FName NewState) void
        +CleanupPlayerState() void
        +ClientSetLocation(FVector NewLocation, FVector NewRotation) void
    }
    AController "0..*" *-- "1" APlayerState
    AController "0..*" *-- "1" AActor

    AController --|> AActor
    AController --|> INavAgentInterface

    class APlayerController{
    }
    APlayerController --|> AController
    APlayerController --|> IWorldPartitionStreamingSourceProvider

    class AAIController{
        -uint32 bAllowStrafe
        -uint32 bStopAILogicOnUnposses
    }
    AAIController "0..*" *-- "1" UBlackBoardComponent
    AAIController "0..*" *-- "1" UBrainComponent
    AAIController "0..*" *-- "1" UGameplayTasksComponent
    AAIController "0..*" *-- "1" UNavigationQueryFilter
    AAIController "0..*" *-- "1" UAIPerceptionComponent

    AAIController --|> AController

    class UBehaviorTreeComponent{
    }
    UBehaviorTreeComponent --|> UBrainComponent

{% endmermaid %}
