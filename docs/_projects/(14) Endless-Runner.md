---
name: Endless Runner
tags: [Unity]
tools: [Unity]
image: ../assets/EndlessRunner/generation.gif
description: This was a collaborative project made for a University assignment

---

# **Project Info**

> 👤 <span style="color:dodgerblue">**Role:**</span> Graphics Programmer <br>
> 👥 <span style="color:dodgerblue">**Team Size:**</span> 5 <br>
> ⌛ <span style="color:dodgerblue">**Time Frame:**</span> 12 Weeks <br>
> 🛠️ <span style="color:dodgerblue">**Framework:**</span> Unity <br>

<p>&nbsp;</p>

# **🏃‍♂️Endless Runner 🏃‍♂️**

---

Common methods of producing <span style="color:orange">**Fire VFX**</span> in **Unity**, is to use a **Particle System** or **VFX Graph**, however the many transparent quads used for each particle can create unnecessary overdraw which negatively affects performance, especially for mobile platforms.

Usually when rendering Opaque geometry, Unity renders objects front-to-back, allowing z-testing to take place; objects obscured by closer objects have their fragments discarded from being rendered, avoiding unnecessary calculations. However when rendering Transparent geometry, Unity has to render those objects back-to-front instead, in order to achieve the correct alpha blending between transparent objects. If a scene contains many of these particles systems the transparency queue can get filled very quickly, resulting in an overcomplex scene that eats away a large portion of the overall graphics budget.

A different technique that mitigates this issue is to offset a single quad's UVs, using noise, to create a fire effect. This reduces the amount of transparent objects per Fire VFX to one, meaning many of these fires can run in parallel on the GPU, and also take less calulations than the particle system. I first saw this technique at an Unreal Engine talk about **[RiME Stylized VFX](https://youtu.be/fwKQyDZ4ark)**. Based on this talk I’ve made multiple versions of a fire shader to try replicate the benefits and test my skills with the Unity Shadergraph.

{% include elements/video.html id="" %}
{% include elements/figure.html image="../assets/EndlessRunner/generation.gif" %}

---

### **Character**

---



---

# <u>**Water**</u>

---

## <u>Overview</u>



## <u>Mesh Generation</u>

## Introduction

In this article, we delve into a Unity script designed for generating procedural meshes, particularly tailored for water shaders. This script exemplifies the integration of 3D graphics, procedural generation, and Unity's powerful rendering capabilities. The code is structured as a `MonoBehaviour` class named `GenerateMesh`, allowing it to be attached to Unity game objects.


## Overview of the Script Structure

### Preliminaries

The script begins with essential namespace imports, followed by Unity-specific attributes to enhance editor functionality:

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
[RequireComponent(typeof(MeshFilter), typeof(MeshRenderer))]
public class GenerateMesh : MonoBehaviour { ... }
```

 
- **ExecuteInEditMode** : Ensures the script runs both in the editor and at runtime, aiding in real-time mesh visualization. 
- **RequireComponent** : Automatically adds required `MeshFilter` and `MeshRenderer` components, crucial for mesh generation and rendering.


### Class Members

The class defines several private and serialized fields:

```csharp
private protected MeshFilter meshFilter;
private bool built = false;
private Mesh mesh;
private bool[,,] tiles;

[SerializeField] private protected Vector3Int dimensions = Vector3Int.one;
[SerializeField] private float tileScale = 1f;
[SerializeField] private int uvScale = 1;
// ...
```
 
- **MeshFilter meshFilter** : Holds the mesh data. 
- **bool built** : Flag to avoid unnecessary mesh rebuilds. 
- **Mesh mesh** : The procedural mesh itself. 
- **bool[,,] tiles** : A 3D array representing the mesh tiles. 
- **Vector3Int dimensions** : Size of the mesh in tiles. 
- **float tileScale** : Scale of each tile. 
- **int uvScale** : UV scaling factor.


### Enumerations and Flags

The script introduces a `TileFace` enum decorated with the `[Flags]` attribute, allowing bitwise operations for multiple face selections:

```csharp
[Flags]
public enum TileFace : int {
    NegX = 1, PosX = 2, NegZ = 4, PosZ = 8
}
```


### Script Lifecycle Methods

#### Awake and Update Methods

```csharp
private void Awake() {
    meshFilter = GetComponent<MeshFilter>();
}

private void Update() {
    if (!built) {
        BuildMesh();
    }
}
```

 
- **Awake** : Initializes the `meshFilter`. 
- **Update** : Triggers `BuildMesh` if the mesh isn't built.


#### OnValidate Method

```csharp
private void OnValidate() {
    mesh = new Mesh { name = "Procedural Mesh" };
    meshFilter.mesh = mesh;
    built = false;
}
```

 
- **OnValidate** : Called when script loads or inspector values change, initializing the mesh and marking it unbuilt.


### Mesh Generation Logic

#### Tile Generation

```csharp
private void GenerateTiles(ref bool[,,] _tiles) { ... }
```

 
- **GenerateTiles** : Populates the `tiles` 3D array based on `dimensions`, marking each tile as active.


#### BuildMesh Method

The core functionality resides in `BuildMesh`, a comprehensive method responsible for constructing the mesh:

```csharp
private void BuildMesh() {
    // Initialization and Clearing Previous Mesh
    // ...

    // Creating Arrays for Mesh Data
    List<Vector3> vertices = new List<Vector3>();
    List<int> triangles = new List<int>();
    List<Vector3> normals = new List<Vector3>();
    List<Vector2> uvs = new List<Vector2>();
    List<Color> colors = new List<Color>(); 

    // Looping Through Each Tile
    for (int x = 0; x < dimensions.x; x++) {
        // ...
    }

    // Applying Mesh Data to Mesh Object
    mesh.SetVertices(vertices);
    mesh.SetNormals(normals);
    mesh.SetUVs(0, uvs);
    mesh.SetColors(colors);
    mesh.SetTriangles(triangles, 0);

    // Finalizing Mesh
    mesh.RecalculateBounds();
    mesh.RecalculateNormals();
    mesh.RecalculateTangents();
    meshFilter.sharedMesh = mesh;
    built = true;
}
```

This method performs several crucial steps: 
1. **Initialization** : Creates and clears the mesh. 
2. **Data Arrays** : Initializes lists for vertices, triangles, normals, UVs, and colors. 
3. **Tile Looping** : Iterates over each tile, calculating vertex positions, normals, UVs, and foam flags. 
4. **Mesh Application** : Assigns calculated data to the mesh. 
5. **Finalization** : Recalculates mesh properties and assigns it to `meshFilter`.


### Detailed Mesh Construction

Within `BuildMesh`, the script dynamically creates faces for each tile. This involves: 
1. **Vertex Calculation** : Determines each vertex's position based on tile coordinates and scale. 
2. **UV Mapping** : Assigns UV coordinates for texture mapping. 
3. **Normal Generation** : Sets normals for lighting calculations. 
4. **Color Assignment** : Marks edges for foam generation in the shader. 
5. **Triangle Configuration** : Defines mesh topology by connecting vertices into triangles.

{% include elements/figure.html image="../assets/EndlessRunner/generation.gif" %}

## <u>Shader</u>

- Vertex Offset
-  
- 
- 
- 
- 
- 
- 