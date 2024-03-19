---
layout: page
permalink: /lyra/character
title: Lyra character
---

{% mermaid %}
classDiagram
    
    class LyraCharacter{
    }
    
    class HealthComponent{
    }
    
    class CameraComponent{
    }

    class PawnExtComponent{
    }

    LyraCharacter --* HealthComponent

    LyraCharacter --* CameraComponent

    LyraCharacter --* PawnExtComponent

    note for LyraCharacter "base character pawn class\nresponsible for sending events to pawn components"

{% endmermaid %}