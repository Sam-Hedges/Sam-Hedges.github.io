---
name: Opal Shader
tags: [Shadergraph, HLSL]
tools: [Shadergraph, HLSL]
image: ../assets/Opal/Opal.gif
description: I created an opal effect shader for the UI element of a university project
---

# **Opal Fluid Shader for a Gauge Bar in Unity**

---

## **Introduction**

As part of the recent game development project, I was tasked with creating a visually appealing shader that presents an opal fluid filling a gauge bar. The challenge was to model the material in such a way that it imitates the opalescent and diffraction properties of real-world opal while providing a dynamic behaviour to simulate a liquid filling.

The shader was designed in Unity's Universal Render Pipeline (URP) using a custom HLSL shader code. This report discusses how I went about creating the shader and the design considerations involved in developing it.

## **The Shader Code**

The shader was created using HLSL (High-Level Shading Language), which is a high-level language that enables the direct use of GPU power for better visual effects and improved game performance.

## **Shader Properties and Variables**

The shader begins by defining several properties and variables. These include textures, floats, and a bool, among others. The textures are for the main texture (_MainTex) and diffraction map (_DiffractionMap). The floats control various parameters like the filling progress (_Progress), colour (_Colour), noise scale (_NoiseScale), dissolve amount (_DissolveAmount), and rotation (_Rotation), to name a few. The bool (_Spherize) toggles the spherization of the UVs.

## **Vertex Function**

The Vertex function takes the Attributes structure as input and returns the Interpolators structure. The Attributes structure holds the incoming mesh's position, normal, tangent, and UVs, and the Interpolators structure is used to pass data to the Fragment function. The UV coordinates of the main texture (_MainTex) are transformed and passed to the Fragment function, along with the vertex's world position, normals, and screen position.

## **Lighting Diffraction Function**

The LightingDiffraction function computes the final lighting of the shader with a diffraction grating effect, mimicking the behaviour of light through an opal. The colour is calculated based on the angular difference between the incoming and outgoing light relative to the surface tangent. The final colour is a combination of the light information and the calculated diffraction colour.

## **Fragment Function**

The Fragment function is where the magic happens. It starts by setting up the UV coordinates, applying a rotation, and optional spherization. It then calculates lighting and diffraction using the previously defined LightingDiffraction function. Noise is added to the UV coordinates to simulate the liquid movement and then pixelated to give it a unique opal-like appearance. The colour of the shader is determined based on several factors including border light, border noise, and the diffraction lighting.

## **Conclusion**

The creation of the opal fluid shader was a fascinating exercise in manipulating and controlling light and colour within a game engine. It was crucial to keep performance in mind while also striving for the highest visual fidelity. Despite the complexity involved, the result was a visually appealing, dynamic, and performant shader that accurately represents an opal fluid filling a gauge bar. The inclusion of shader properties allowed the design team to manipulate the shader's appearance in real-time, contributing to an efficient workflow and a visually consistent final product.