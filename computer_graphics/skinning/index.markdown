---
layout: page
permalink: /computer_graphics/skinning
title: Skinning
---

Page dedicated to knowledge related to skinning.

## Bone diagram

{% mermaid %}
classDiagram

    class aiScene{
        +aiMesh[]
    }
    class aiMesh{
        +aiVector3D vertices[]
        +aiBone[]
    }
    class aiBone{
        +string name
        +mat4 offsetMatrix
        +aiVertexWeight[]
    }
    class aiVector3D{
        +float x
        +float y
        +float z
    }
    class aiVertexWeight{
        +uint VertexID
        +float Weight
    }
    note for aiVertexWeight " VertexID: Index of the vertex which is influenced by the bone.
    Index is from aiMesh array of vertices.
    "

    aiScene "1" --* "0..*" aiMesh
    aiMesh "1..*" --* "0..*" aiBone
    aiMesh "1..*" --* "0..*" aiVector3D
    aiBone "1..*" --* "0..*" aiVertexWeight
    aiVertexWeight "1" --> "1" aiVector3D

{% endmermaid %}

## Animation diagram

{% mermaid %}
classDiagram

    class aiScene{
        aiNode RootNode
        +Animations[]
    }
    class aiNode{
        +string Name
        +mat4 Transformation
        +aiNode Parent
        +aiNode Children[]
    }
    note for aiNode "Matrix transform from node space into parent space
    If the node represents a bone, the names must match.
    "
    class aiAnimation{
        +double Duration
        +double TicksPerSecond
        +aiNodeAnim Channels[]
    }
    note for aiAnimation "A single animation represents a sequence of animation frames.
    "
    class aiNodeAnim{
        +string Name
        +vector3D Positions[]
        +quaternion Rotations[]
        +vector3D Scalings[]
    }
    note for aiNodeAnim "Each channel is the bone with all its transformations.
    The name must match the bone name.
    "

    aiScene "1" --* "1" aiNode
    aiScene "1" --* "0..*" aiAnimation
    aiNode "1..*" --* "1..*" aiNode
    aiAnimation "1" --* "0..*" aiNodeAnim

{% endmermaid %}
