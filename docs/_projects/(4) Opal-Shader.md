---
name: Opal Shader
tags: [Shadergraph, HLSL]
tools: [Shadergraph, HLSL]
image: ../assets/Opal/Opal.gif
description: Liquid opal shader for a UI health meter
---

# **Project Info**

> 👤 <span style="color:dodgerblue">**Role:**</span> Graphics Programmer <br>
> 👥 <span style="color:dodgerblue">**Team Size:**</span> 5 <br>
> ⌛ <span style="color:dodgerblue">**Time Frame:**</span> 12 Weeks <br>
> 🛠️ <span style="color:dodgerblue">**Framework:**</span> Unity <br>

<p>&nbsp;</p>

# **Opal Fluid Shader for a Gauge Bar in Unity**

---

## **Introduction**

As part of the recent game development project, I was tasked with creating a visually appealing shader that presents an opal fluid filling a gauge bar. The challenge was to model the material in such a way that it imitates the opalescent and diffraction properties of real-world opal while providing a dynamic behaviour to simulate a liquid filling.

The shader was designed in Unity's Universal Render Pipeline (URP) using a custom HLSL shader code. This report discusses how I went about creating the shader and the design considerations involved in developing it.

## **The Shader Code**

The shader was created using HLSL (High-Level Shading Language), which is a high-level language that enables the direct use of GPU power for better visual effects and improved game performance.

```hlsl
// Pull in URP library functions and our own common functions
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl"
			
// Contains all the shadergraph related functions
#include "CommonFunctions.hlsl"

// This file contains the vertex and fragment functions for the forward lit pass
// This is the shader pass that computes visible colours for a material
// by reading material, light, shadow, etc. data

// Shader Properties
TEXTURE2D(_MainTex); SAMPLER(sampler_MainTex);
TEXTURE2D(_DiffractionMap); SAMPLER(sampler_DiffractionMap);
float4 _MainTex_ST; // Automatically set by Unity. Used in TRANSFORM_TEX to apply UV tiling
float _Progress;
float4 _Colour;
float4 _BackgroundColour;
float _BorderNoiseScale;
float _MovingAmount;
float _DissolveTransition;
float _DissolveAmount;
float _Rotation;
float _NoiseScale;
float _NoiseIntensity;
bool _Spherize;
float _BorderDistortionAmount;
float _NoiseRoundFactor;
float _Distance;

// Variables
float3 worldTangent;

// This attributes struct receives data about the mesh we're currently rendering
// Data automatically populates fields according to their semantic
struct Attributes
{
    float3 positionOS : POSITION; // Position in object space
    float3 normalOS : NORMAL; // Normal in object space
    float4 tangetOS : TANGENT; // Tangent in object space
    float2 uv : TEXCOORD0; // Material texture UVs
	float2 lightmapUV	: TEXCOORD6;
};

// This struct is output by the vertex function and input to the fragment function
// Note that fields will be transformed by the intermediary rasterization stage
struct Interpolators
{
    // This value should contain the position in clip space when output from the
    // vertex function. It will be transformed into the pixel position of the
    // current fragment on the screen when read from the fragment function
    float4 positionCS : SV_POSITION;

    // The following variables will retain their values from the vertex stage, except the
    // rasterizer will interpolate them between vertices
    // Two fields should not have the same semantic, the rasterizer can handle many TEXCOORD variables
    float2 uv : TEXCOORD0; // Material texture UVs
    float3 normalWS : TEXCOORD1; // Normal in world space
    float3 normalOS : TEXCOORD2; // Normal in object space
    float3 normalTS : TEXCOORD3; // Normal in object space
    float3 positionWS : TEXCOORD4;
    float4 screenPosition : TEXCOORD5;

	DECLARE_LIGHTMAP_OR_SH(lightmapUV, vertexSH, 6);
};

// The vertex function which runs for each vertex on the mesh.
// It must output the position on the screen, where each vertex should appear,
// as well as any data the fragment function will need
Interpolators Vertex(Attributes input)
{
    Interpolators output;
    
    // These helper functions, found in URP/ShaderLib/ShaderVariablesFunctions.hlsl
    // transform object space values into world and clip space
    // CalulateWaves is a custom function that calculates the wave movement
    VertexPositionInputs posnInputs = GetVertexPositionInputs(input.positionOS);
    VertexNormalInputs normInputs = GetVertexNormalInputs(input.normalOS);
	
    // Pass position, orientation and normal data to the fragment function
    output.uv = input.uv;
    output.uv = TRANSFORM_TEX(input.uv, _MainTex);
    output.positionCS = posnInputs.positionCS;
    output.normalWS = normInputs.normalWS;
    output.normalOS = input.normalOS;
    output.normalTS = normInputs.tangentWS;
    output.positionWS = posnInputs.positionWS;
    output.screenPosition = ComputeScreenPos(output.positionCS);
	
    return output;
}

//  SurfaceData & InputData
void InitalizeSurfaceData(Interpolators input, out SurfaceData surfaceData){
	surfaceData = (SurfaceData)0; // avoids "not completely initalized" errors

	// Populates the fields in the input structs
	surfaceData.albedo = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, input.uv).rgb;
	surfaceData.alpha = 1;
	surfaceData.specular = 1; // Set Highlights to white
	surfaceData.smoothness = 1;
	surfaceData.occlusion = 1.0; // unused

}

void InitializeInputData(Interpolators input, out InputData inputData) {
	inputData = (InputData)0; // avoids "not completely initalized" errors

	inputData.positionWS = input.positionWS;
	inputData.viewDirectionWS = GetWorldSpaceNormalizeViewDir(input.positionWS);
	inputData.shadowCoord = TransformWorldToShadowCoord(input.positionWS);
	// When the normal is rasterized due to the semantic in the Interpolators struct, it's interpolated between
	// values, and for lighting to look its best all normal vectors must have a length of one.
	// Hence the Normalize method, which can be slow, since it has an expensive square root calculation
	// but I think this step is worth the performance cost for smoother lighting. (especially noticeable on specular highlights)
	//lightingInput.normalWS = UnityObjectToWorldNormal(normalize(finalNormal);
	inputData.normalWS = NormalizeNormalPerPixel(inputData.normalWS);
	//inputData.normalWS = NormalFromTexture(TEXTURE2D_ARGS(_DiffractionMap, sampler_DiffractionMap), input.uv + _Time.y * 0.1, 1, 1);
	
	inputData.bakedGI = SAMPLE_GI(input.lightmapUV, input.vertexSH, inputData.normalWS);
}

float3 bump3y (float3 x, float3 yoffset)
{
	float3 y = 1 - x * x;
	y = saturate(y-yoffset);
	return y;
}

float3 spectral_zucconi6 (float w)
{
	// w: [400, 700]
	// x: [0,   1]
	float x = saturate((w - 400.0)/ 300.0);
	const float3 c1 = float3(3.54585104, 2.93225262, 2.41593945);
	const float3 x1 = float3(0.69549072, 0.49228336, 0.27699880);
	const float3 y1 = float3(0.02312639, 0.15225084, 0.52607955);
	const float3 c2 = float3(3.90307140, 3.21182957, 3.96587128);
	const float3 x2 = float3(0.11748627, 0.86755042, 0.66077860);
	const float3 y2 = float3(0.84897130, 0.88445281, 0.73949448);
	return
		bump3y(c1 * (x - x1), y1) +
		bump3y(c2 * (x - x2), y2) ;
}

float4 LightingDiffraction(Interpolators input)
{
    // Setup SurfaceData
    SurfaceData surfaceData;
    InitalizeSurfaceData(input, surfaceData);

    // Setup InputData
    InputData inputData;
    InitializeInputData(input, inputData);

    // Simple Lighting (Lambert & BlinnPhong)
    float4 lighting = UniversalFragmentBlinnPhong(inputData, surfaceData); // v12 only

	// Diffraction grating effect
	float3 L = inputData.bakedGI;
	float3 V = _WorldSpaceCameraPos.xyz - input.positionWS;
	float3 T = worldTangent;
	float d = _Distance;
	float cos_ThetaL = dot(L, T);
	float cos_ThetaV = dot(V, T);
	float u = abs(cos_ThetaL - cos_ThetaV);
	if (u == 0) { return lighting; }
	
	// Calculates the reflection color
	float3 color = 0;
	for (int n = 1; n <= 8; n++)
	{
		float wavelength = u * d / n;
		color += spectral_zucconi6(wavelength);
	}
	color = saturate(color);
	// Adds the reflection to the material colour
	lighting.rgb += color;
    
    return lighting;
}

// The fragment function runs once per fragment, akin to a pixel on the screen but virtualized
// It must output the final colour of this pixel hence the function is a float4
// The function is tagged with a semantic so that the return value is interpreted in a specific way
float4 Fragment(Interpolators input) : SV_TARGET
{
    // Setup UVs
    float2 uv = Rotate(input.uv, 0.5, _Rotation);
    if (_Spherize) { uv = Spherize(uv, 0.5, 10, 0); }
	
	float2 uv_orthogonal = normalize(uv);
	float3 uv_tangent = float3(-uv_orthogonal.y, 0, uv_orthogonal.x);
	worldTangent = normalize(mul(unity_ObjectToWorld, float4(uv_tangent, 0))).xyz;
	worldTangent = NormalFromTexture(TEXTURE2D_ARGS(_DiffractionMap, sampler_DiffractionMap), float2(uv.x, uv.y + _Time.y * 0.1), 1, 2);

	float4 lightingDiffraction = LightingDiffraction(input);
	
    // Sample scene time
    float time = _Time.y;

    // Border Shape, Light & Noise
    float gradientNoise = GradientNoise(uv + float2(0, time * 0.2), _BorderNoiseScale);
    //float borderNoise = _Progress - lerp(uv1.x, gradientNoise, _BorderDistortionAmount);
    float borderNoise = _Progress - lerp(uv.x, gradientNoise, _BorderDistortionAmount);
    float4 finalBorderNoise = SampleBasicGradient(borderNoise, 1, 0.678431, 0.0009);
    float4 finalBorderLightNoise = SampleBasicGradient(borderNoise, 1, 0, 0.0005) * _MovingAmount;
    
    // Inside Noise
    float simpleNoise1 = SimpleNoise(uv + float2(0, time * 0.1), _NoiseScale);
    float simpleNoise2 = SimpleNoise(uv + float2(0, time * 0.03), _NoiseScale * 1.3);
    float simpleNoise3 = SimpleNoise(uv + float2(0, time * -0.03), _NoiseScale * 1.5);
    float simpleNoiseMerged = simpleNoise2 * simpleNoise3 * simpleNoise1;
    float simpleNoisePixelated = Remap((round(simpleNoiseMerged * _NoiseRoundFactor ) / _NoiseRoundFactor).xxxx, float2(0, 1), float2(0, 10)).x;
    float simpleNoiseLerped = lerp(1, simpleNoisePixelated, _NoiseIntensity);
    float4 finalSimpleNoise = lerp((1 - step(finalBorderNoise, 0.1822373)), lightingDiffraction, saturate(lightingDiffraction.a * 0.4));
	
    // Dissolve
    float dissolveNoise = SimpleNoise(uv + float2(time * -1, 0), 30);
    float dissolveStep1 = step(_DissolveAmount - 0.06, dissolveNoise);
    float dissolveStep2 = step(_DissolveAmount, dissolveNoise);
    float dissolveFinalStep = (dissolveStep1 - dissolveStep2) * 15 + dissolveStep2;
    //float4 finalDissolve = simpleNoiseLerped * _Colour * 3 * dissolveFinalStep;
	float4 finalDissolve = _Colour * 3 * dissolveFinalStep;
	
    // Colour
    float4 finalColour = finalBorderLightNoise + _Colour * finalBorderNoise * finalSimpleNoise;
    finalColour = lerp(finalColour, finalDissolve, _DissolveTransition);
    float colourMask = ColourMask(finalColour.xyz, float3(0, 0, 0), 0, 0).x;
    finalColour.rgb = (finalColour + colourMask * _BackgroundColour).xyz;
    float4 mainTex = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, input.uv);
    finalColour.a = mainTex.a * Remap(colourMask, float2(0, 1), float2(_Colour.a, _BackgroundColour.a)).x;
    
    return finalColour;
}

```

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


{% include elements/figure.html image="../assets/Opal/Opal.gif" caption="Final Opal Shader" %}

Shader Structure Overview

A Unity shader typically consists of properties, variables, and two main functions: Vertex and Fragment. The Vertex function computes the vertex positions, while the Fragment function calculates the color of each pixel.

Including Necessary Librarieshlsl
```hlsl
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl"
#include "CommonFunctions.hlsl"
```
These lines include essential URP library functions and custom common functions required for the shader operations.

## Shader Properties
```hlsl
TEXTURE2D(_MainTex); SAMPLER(sampler_MainTex);
TEXTURE2D(_DiffractionMap); SAMPLER(sampler_DiffractionMap);
float4 _MainTex_ST; // Automatically set by Unity. Used in TRANSFORM_TEX to apply UV tiling
float _Progress;
float4 _Colour;
float4 _BackgroundColour;
float _BorderNoiseScale;
float _MovingAmount;
float _DissolveTransition;
float _DissolveAmount;
float _Rotation;
float _NoiseScale;
float _NoiseIntensity;
bool _Spherize;
float _BorderDistortionAmount;
float _NoiseRoundFactor;
float _Distance;
float3 worldTangent;
```
These lines declare various shader properties like textures, color, and float values. These properties can be set and adjusted in Unity’s material inspector to change the shader's appearance dynamically.

Struct DefinitionsAttributeshlsl
```hlsl
struct Attributes {
    float3 positionOS : POSITION; // Position in object space
    float3 normalOS : NORMAL; // Normal in object space
    float4 tangetOS : TANGENT; // Tangent in object space
    float2 uv : TEXCOORD0; // Material texture UVs
	float2 lightmapUV	: TEXCOORD6;
};
```
This struct defines the attributes of each vertex, such as position, normal, and UV coordinates, passed to the Vertex function.

Interpolatorshlsl
```hlsl
struct Interpolators {
    // This value should contain the position in clip space when output from the
    // vertex function. It will be transformed into the pixel position of the
    // current fragment on the screen when read from the fragment function
    float4 positionCS : SV_POSITION;

    // The following variables will retain their values from the vertex stage, except the
    // rasterizer will interpolate them between vertices
    // Two fields should not have the same semantic, the rasterizer can handle many TEXCOORD variables
    float2 uv : TEXCOORD0; // Material texture UVs
    float3 normalWS : TEXCOORD1; // Normal in world space
    float3 normalOS : TEXCOORD2; // Normal in object space
    float3 normalTS : TEXCOORD3; // Normal in object space
    float3 positionWS : TEXCOORD4;
    float4 screenPosition : TEXCOORD5;

	DECLARE_LIGHTMAP_OR_SH(lightmapUV, vertexSH, 6);
};
```
The Interpolators struct is used to pass data from the Vertex function to the Fragment function. It includes position, normal, and texture coordinates.

Vertex Functionhlsl
```hlsl
Interpolators Vertex(Attributes input) {
    Interpolators output;
    
    // These helper functions, found in URP/ShaderLib/ShaderVariablesFunctions.hlsl
    // transform object space values into world and clip space
    // CalulateWaves is a custom function that calculates the wave movement
    VertexPositionInputs posnInputs = GetVertexPositionInputs(input.positionOS);
    VertexNormalInputs normInputs = GetVertexNormalInputs(input.normalOS);
	
    // Pass position, orientation and normal data to the fragment function
    output.uv = input.uv;
    output.uv = TRANSFORM_TEX(input.uv, _MainTex);
    output.positionCS = posnInputs.positionCS;
    output.normalWS = normInputs.normalWS;
    output.normalOS = input.normalOS;
    output.normalTS = normInputs.tangentWS;
    output.positionWS = posnInputs.positionWS;
    output.screenPosition = ComputeScreenPos(output.positionCS);
	
    return output;
}
```
The Vertex function calculates the screen position of each vertex. It transforms object space values to world and clip space and passes other necessary data to the Fragment function.

Lighting and Diffraction Effecthlsl
```hlsl
float4 LightingDiffraction(Interpolators input) {
    // Setup SurfaceData
    SurfaceData surfaceData;
    InitalizeSurfaceData(input, surfaceData);

    // Setup InputData
    InputData inputData;
    InitializeInputData(input, inputData);

    // Simple Lighting (Lambert & BlinnPhong)
    float4 lighting = UniversalFragmentBlinnPhong(inputData, surfaceData); // v12 only

	// Diffraction grating effect
	float3 L = inputData.bakedGI;
	float3 V = _WorldSpaceCameraPos.xyz - input.positionWS;
	float3 T = worldTangent;
	float d = _Distance;
	float cos_ThetaL = dot(L, T);
	float cos_ThetaV = dot(V, T);
	float u = abs(cos_ThetaL - cos_ThetaV);
	if (u == 0) { return lighting; }
	
	// Calculates the reflection color
	float3 color = 0;
	for (int n = 1; n <= 8; n++)
	{
		float wavelength = u * d / n;
		color += spectral_zucconi6(wavelength);
	}
	color = saturate(color);
	// Adds the reflection to the material colour
	lighting.rgb += color;
    
    return lighting;
}
```
This function computes the diffraction effect, which contributes to the liquid opal look. It uses the surface and input data to calculate the lighting and adds a diffraction grating effect.

Fragment Functionhlsl
```hlsl
float4 Fragment(Interpolators input) : SV_TARGET {
    // Setup UVs
    float2 uv = Rotate(input.uv, 0.5, _Rotation);
    if (_Spherize) { uv = Spherize(uv, 0.5, 10, 0); }
	
	float2 uv_orthogonal = normalize(uv);
	float3 uv_tangent = float3(-uv_orthogonal.y, 0, uv_orthogonal.x);
	worldTangent = normalize(mul(unity_ObjectToWorld, float4(uv_tangent, 0))).xyz;
	worldTangent = NormalFromTexture(TEXTURE2D_ARGS(_DiffractionMap, sampler_DiffractionMap), float2(uv.x, uv.y + _Time.y * 0.1), 1, 2);

	float4 lightingDiffraction = LightingDiffraction(input);
	
    // Sample scene time
    float time = _Time.y;

    // Border Shape, Light & Noise
    float gradientNoise = GradientNoise(uv + float2(0, time * 0.2), _BorderNoiseScale);
    //float borderNoise = _Progress - lerp(uv1.x, gradientNoise, _BorderDistortionAmount);
    float borderNoise = _Progress - lerp(uv.x, gradientNoise, _BorderDistortionAmount);
    float4 finalBorderNoise = SampleBasicGradient(borderNoise, 1, 0.678431, 0.0009);
    float4 finalBorderLightNoise = SampleBasicGradient(borderNoise, 1, 0, 0.0005) * _MovingAmount;
    
    // Inside Noise
    float simpleNoise1 = SimpleNoise(uv + float2(0, time * 0.1), _NoiseScale);
    float simpleNoise2 = SimpleNoise(uv + float2(0, time * 0.03), _NoiseScale * 1.3);
    float simpleNoise3 = SimpleNoise(uv + float2(0, time * -0.03), _NoiseScale * 1.5);
    float simpleNoiseMerged = simpleNoise2 * simpleNoise3 * simpleNoise1;
    float simpleNoisePixelated = Remap((round(simpleNoiseMerged * _NoiseRoundFactor ) / _NoiseRoundFactor).xxxx, float2(0, 1), float2(0, 10)).x;
    float simpleNoiseLerped = lerp(1, simpleNoisePixelated, _NoiseIntensity);
    float4 finalSimpleNoise = lerp((1 - step(finalBorderNoise, 0.1822373)), lightingDiffraction, saturate(lightingDiffraction.a * 0.4));
	
    // Dissolve
    float dissolveNoise = SimpleNoise(uv + float2(time * -1, 0), 30);
    float dissolveStep1 = step(_DissolveAmount - 0.06, dissolveNoise);
    float dissolveStep2 = step(_DissolveAmount, dissolveNoise);
    float dissolveFinalStep = (dissolveStep1 - dissolveStep2) * 15 + dissolveStep2;
    //float4 finalDissolve = simpleNoiseLerped * _Colour * 3 * dissolveFinalStep;
	float4 finalDissolve = _Colour * 3 * dissolveFinalStep;
	
    // Colour
    float4 finalColour = finalBorderLightNoise + _Colour * finalBorderNoise * finalSimpleNoise;
    finalColour = lerp(finalColour, finalDissolve, _DissolveTransition);
    float colourMask = ColourMask(finalColour.xyz, float3(0, 0, 0), 0, 0).x;
    finalColour.rgb = (finalColour + colourMask * _BackgroundColour).xyz;
    float4 mainTex = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, input.uv);
    finalColour.a = mainTex.a * Remap(colourMask, float2(0, 1), float2(_Colour.a, _BackgroundColour.a)).x;
    
    return finalColour;
}
```

The Fragment function is the core of the shader, determining the final color of each pixel. It incorporates various effects like UV manipulation, noise generation, and color blending to create the final liquid opal effect.Key Components of the ShaderUV Manipulation

The shader manipulates UV coordinates for various effects, such as rotation (```Rotate```) and spherizing (```Spherize```).Noise Functions

Several noise functions (```SimpleNoise```, ```GradientNoise```) are used to create a dynamic, flowing appearance in the liquid.Dissolve Effect

The shader uses a dissolve effect to create transitions in the liquid's appearance, controlled by properties like ```_DissolveAmount``` and ```_DissolveTransition```.Color Blending

Colors are blended and adjusted to create the desired visual effect. This includes mixing the main color with background color and applying various adjustments based on noise and border effects.Reflection and Refraction

Spectral reflection and refraction calculations (```spectral_zucconi6```) contribute to the opal-like appearance, simulating light interaction with a semi-transparent material.Conclusion

This shader is a complex example of how various techniques like noise generation, color manipulation, and light calculations can be combined in a shader to create visually striking effects, suitable for dynamic UI elements in games and applications. The shader is highly customizable through exposed properties, allowing easy adjustments to fit different artistic visions and functional requirements.

**ChatGPT**: Explaining a Unity Shader for Liquid Opal Effect

This document explains a Unity shader code snippet designed to create a liquid opal effect, often used in user interface (UI) elements like health bars. The shader is written in HLSL (High-Level Shading Language) and utilizes the Universal Render Pipeline (URP) of Unity. The shader allows adjusting the fill amount of the liquid, providing a dynamic and visually appealing effect.Shader Structure Overview

A Unity shader typically consists of properties, variables, and two main functions: Vertex and Fragment. The Vertex function computes the vertex positions, while the Fragment function calculates the color of each pixel.Including Necessary Librarieshlsl
```hlsl
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl"
#include "CommonFunctions.hlsl"
```

These lines include essential URP library functions and custom common functions required for the shader operations.Shader Propertieshlsl
```hlsl
TEXTURE2D(_MainTex);
SAMPLER(sampler_MainTex);
...
bool _Spherize;
float _BorderDistortionAmount;
...
```

These lines declare various shader properties like textures, color, and float values. These properties can be set and adjusted in Unity’s material inspector to change the shader's appearance dynamically.Variables Declarationhlsl
```hlsl
float3 worldTangent;
```

This line declares a global variable used across the shader.Struct DefinitionsAttributeshlsl
```hlsl
struct Attributes {
    ...
};
```

This struct defines the attributes of each vertex, such as position, normal, and UV coordinates, passed to the Vertex function.Interpolatorshlsl
```hlsl
struct Interpolators {
    ...
};
```

The Interpolators struct is used to pass data from the Vertex function to the Fragment function. It includes position, normal, and texture coordinates.Vertex Functionhlsl
```hlsl
Interpolators Vertex(Attributes input) {
    ...
}
```

The Vertex function calculates the screen position of each vertex. It transforms object space values to world and clip space and passes other necessary data to the Fragment function.SurfaceData &amp; InputData Initializationhlsl
```hlsl
void InitalizeSurfaceData(Interpolators input, out SurfaceData surfaceData){
    ...
}

void InitializeInputData(Interpolators input, out InputData inputData) {
    ...
}
```

These functions initialize surface and input data structures which are used for lighting calculations.Lighting and Diffraction Effecthlsl
```hlsl
float4 LightingDiffraction(Interpolators input) {
    ...
}
```

This function computes the diffraction effect, which contributes to the liquid opal look. It uses the surface and input data to calculate the lighting and adds a diffraction grating effect.Fragment Functionhlsl
```hlsl
float4 Fragment(Interpolators input) : SV_TARGET {
    ...
}
```

The Fragment function is the core of the shader, determining the final color of each pixel. It incorporates various effects like UV manipulation, noise generation, and color blending to create the final liquid opal effect.Key Components of the ShaderUV Manipulation

The shader manipulates UV coordinates for various effects, such as rotation (```Rotate```) and spherizing (```Spherize```).Noise Functions

Several noise functions (```SimpleNoise```, ```GradientNoise```) are used to create a dynamic, flowing appearance in the liquid.Dissolve Effect

The shader uses a dissolve effect to create transitions in the liquid's appearance, controlled by properties like ```_DissolveAmount``` and ```_DissolveTransition```.Color Blending

Colors are blended and adjusted to create the desired visual effect. This includes mixing the main color with background color and applying various adjustments based on noise and border effects.Reflection and Refraction

Spectral reflection and refraction calculations (```spectral_zucconi6```) contribute to the opal-like appearance, simulating light interaction with a semi-transparent material.Conclusion

This shader is a complex example of how various techniques like noise generation, color manipulation, and light calculations can be combined in a shader to create visually striking effects, suitable for dynamic UI elements in games and applications. The shader is highly customizable through exposed properties, allowing easy adjustments to fit different artistic visions and functional requirements.