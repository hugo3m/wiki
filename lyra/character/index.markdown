---
layout: page
permalink: /lyra/character
title: Lyra character
---


## Overall overview

{% mermaid %}
classDiagram
    
    class AActor{
    }
    class ACharacter{
    }
    class ALyraCharacter{
        -TObjectPtr~ULyraCameraComponent~ CameraComponent
        -TObjectPtr~ULyraHealthComponent~ HealthComponent
        -TObjectPtr~ULyraPawnExtensionComponent~ PawnExtComponent

        #OnDeathStarted(AActor* OwningActor)
        #PossessedBy(AController* NewController)
        #SetupPlayerInputcomponent(UInputComponent* PlayerInputComponent)

    }
    class ALyraCharacterWithAbilities{
        -TObjectPtr~ULyraAbilitySystemComponent~ AbilitySystemComponent;
    }
    class AModularCharacter{
    }
    class APawn{
    }
    class UCharacterMovementComponent{
    }
    class ULyraAbilitySystemComponent{
    }
    class ULyraCameraComponent{
        +FindCameraComponent(const AActor* Actor) ULyraCameraComponent*
        #GetCameraView(float DeltaTime, FMinimalViewInfo& DesiredView) 
    }
    class ULyraCharacterMovementComponent{
    }
    class ULyraHealthComponent{
        +FindHealthComponent(const AActor* Actor) ULyraHealthComponent* 
        +GetHealth() float
        #HandleHealthChanged(AActor* DamageInstigator, AActor* DamageCauser)
    }
    class ULyraPawnExtensionComponent{
        +FindPawnExtensionComponent(const AActor* Actor) ULyraPawnExtensionComponent*
        +HandleControllerChanged()
        +SetupPlayerInputComponent()
    }
    class ULyraPawnData{
        +TSubclassOf~APawn~ PawnClass
    }
    class ULyraAbilitySet{
    }
    class ULyraInputConfig{
    }
    class ULyraCameraMode{
    }
    class UPawnComponent{
    }

    ACharacter "0..*" --* "1" UCharacterMovementComponent
    ACharacter --|> APawn
    AModularCharacter --|> ACharacter
    APawn --|> AActor
    ALyraCharacter --* ULyraHealthComponent
    ALyraCharacter --* ULyraCameraComponent
    ALyraCharacter --* ULyraPawnExtensionComponent
    ALyraCharacter --|> AModularCharacter
    ALyraCharacterWithAbilities --|> ALyraCharacter
    ALyraCharacterWithAbilities "0..*" --* "1" ULyraAbilitySystemComponent
    ULyraCharacterMovementComponent --|> UCharacterMovementComponent
    ULyraPawnExtensionComponent --|> UPawnComponent
    ULyraPawnExtensionComponent --* ULyraAbilitySystemComponent
    ULyraPawnExtensionComponent --* ULyraPawnData
    ULyraPawnData "0..*" --* "1" APawn
    ULyraPawnData "1" --* "0..*" ULyraAbilitySet
    ULyraPawnData --* ULyraInputConfig
    ULyraPawnData --* ULyraCameraMode


{% endmermaid %}

## ULyraPawnExtensionComponent focus

{% mermaid %}
classDiagram
    
    class APawn{
    }
    class ULyraAbilitySystemComponent{
    }
    class ULyraPawnExtensionComponent{
        +FindPawnExtensionComponent(const AActor* Actor) ULyraPawnExtensionComponent*
        +HandleControllerChanged()
        +SetupPlayerInputComponent()
    }
    class ULyraPawnData{
        +TSubclassOf~APawn~ PawnClass
    }
    class ULyraAbilitySet{
    }
    class ULyraInputConfig{
    }
    class ULyraCameraMode{
    }
    class UPawnComponent{
    }

    ULyraPawnExtensionComponent --|> UPawnComponent
    ULyraPawnExtensionComponent --* ULyraAbilitySystemComponent
    ULyraPawnExtensionComponent --* ULyraPawnData
    ULyraPawnData "0..*" --* "1" APawn
    ULyraPawnData "1" --* "0..*" ULyraAbilitySet
    ULyraPawnData --* ULyraInputConfig
    ULyraPawnData --* ULyraCameraMode

    note for APawn "pawn is the base class of all actors that can be possessed by players or AI"
    note for ULyraAbilitySystemComponent "base ability system component class"
    note for ULyraPawnExtensionComponent "components adding functionality to all pawn classes\ncan be used for characters/vehicles"
    note for ULyraPawnData "non-mutable data asset that contains properties used to define a pawn"
    note for UPawnComponent "actor component made for APawn and receives pawn events."


{% endmermaid %}

## ALyraCharacter focus

{% mermaid %}
classDiagram
    
    class AActor{
    }
    class ACharacter{
    }
    class ALyraCharacter{
        -TObjectPtr~ULyraCameraComponent~ CameraComponent
        -TObjectPtr~ULyraHealthComponent~ HealthComponent
        -TObjectPtr~ULyraPawnExtensionComponent~ PawnExtComponent

        #OnDeathStarted(AActor* OwningActor)
        #PossessedBy(AController* NewController)
        #SetupPlayerInputcomponent(UInputComponent* PlayerInputComponent)

    }
    class ALyraCharacterWithAbilities{
        -TObjectPtr~ULyraAbilitySystemComponent~ AbilitySystemComponent;
    }
    class AModularCharacter{
    }
    class APawn{
    }
    class UCharacterMovementComponent{
    }
    class ULyraAbilitySystemComponent{
    }
    class ULyraCameraComponent{
        +FindCameraComponent(const AActor* Actor) ULyraCameraComponent*
        #GetCameraView(float DeltaTime, FMinimalViewInfo& DesiredView) 
    }
    class ULyraCharacterMovementComponent{
    }
    class ULyraHealthComponent{
        +FindHealthComponent(const AActor* Actor) ULyraHealthComponent* 
        +GetHealth() float
        #HandleHealthChanged(AActor* DamageInstigator, AActor* DamageCauser)
    }
    class ULyraPawnExtensionComponent{
    }

    ACharacter "0..*" --* "1" UCharacterMovementComponent
    ACharacter --|> APawn
    AModularCharacter --|> ACharacter
    APawn --|> AActor
    ALyraCharacter --* ULyraHealthComponent
    ALyraCharacter --* ULyraCameraComponent
    ALyraCharacter --* ULyraPawnExtensionComponent
    ALyraCharacter --|> AModularCharacter
    ALyraCharacterWithAbilities --|> ALyraCharacter
    ALyraCharacterWithAbilities "0..*" --* "1" ULyraAbilitySystemComponent
    ULyraCharacterMovementComponent --|> UCharacterMovementComponent

    note for AActor "base class for an Object that can be placed or spawned in a level"
    note for ALyraCharacter "base character pawn class\nresponsible for sending events to pawn components"
    note for UCharacterMovementComponent "handles movement logic for the associated Character owner.\nnetworking is fully implemented"
    note for ULyraAbilitySystemComponent "base ability system component class"
    note for ULyraPawnExtensionComponent "components adding functionality to all pawn classes\ncan be used for characters/vehicles"


{% endmermaid %}