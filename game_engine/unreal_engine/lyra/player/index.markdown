---
layout: page
permalink: /game_engine/unreal_engine/lyra/player
title: Lyra character
---


## Player controller

{% mermaid %}
classDiagram

    class ACommonPlayerController{
    }
    class AController{
    }
    class ALyraPlayerController{
        #TObjectPtr~APlayerState~ LastSeenPlayerState
    }
    class ALyraPlayerState{
    }
    class AModularPlayerState{
    }
    class APlayerController{
    }
    class APlayerState{
    }
    class AModularPlayerController{
    }
    class UCommonLocalPlayer{
    }
    class ULocalPlayer{
    }
    class ULyraLocalPlayer{
        -TWeakObjectPtr~APlayerController~ LastBoundPC

        +SpawnPlayActor(FString URL, FString OutError, UWorld InWorld) bool
        +SwitchController(APlayerController PC)
    }


    ACommonPlayerController --|> AModularPlayerController
    ALyraPlayerController --|> ACommonPlayerController
    ALyraPlayerState --|> AModularPlayerState
    AModularPlayerController --|> APlayerController
    AModularPlayerState --|> APlayerState
    APlayerController --|> AController
    UCommonLocalPlayer --|> ULocalPlayer
    ULyraLocalPlayer --|> UCommonLocalPlayer

    ULyraLocalPlayer "0..*" --* "1" APlayerController
    ALyraPlayerController "1" --* "1" APlayerState



{% endmermaid %}


## Player start

{% mermaid %}
classDiagram

    class ACommonPlayerController{
    }
    class AController{
    }
    class ALyraPlayerController{
    }
    class ALyraPlayerStart{
        #TObjectPtr~AController~ ClaimingController

        +IsClaimed() bool
        +TryClaim(AController OccupyingController) bool
    }
    class APlayerController{
    }
    class AModularPlayerController{
    }
    class ULyraPlayerSpawningManagerComponent{
        TArray~TWekObjectPtr~ALyraPlayerStart~~ CachedPlayerStarts

        -ChoosePlayerStart(AController Player) AActor
        -ControllerCanRestart(AController Player) bool
        -FinishRestartPlayer(AController NewPlayer, FRotator StartRotation)
    }


    ACommonPlayerController --|> AModularPlayerController
    ALyraPlayerController --|> ACommonPlayerController
    AModularPlayerController --|> APlayerController
    APlayerController --|> AController

    ALyraPlayerStart "1" --* "1" AController
    ULyraPlayerSpawningManagerComponent "1" --* "0..*" ALyraPlayerStart

{% endmermaid %}

## Lyra Player Bot Controller

{% mermaid %}
classDiagram

    class AAIController{
    }
    class AController{
    }
    class ALyraPlayerBotController{
        -TObjectPtr~APlayerState~ LastSeenPlayerState
    }
    class AModularAIController{
    }
    class APlayerState{
    }

    AAIController --|> AController
    ALyraPlayerBotController --|> AModularAIController
    AModularAIController --|> AAIController

    ALyraPlayerBotController "1" --* "1" APlayerState

{% endmermaid %}