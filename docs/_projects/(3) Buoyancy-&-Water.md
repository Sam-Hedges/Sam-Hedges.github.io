---
name: Buoyancy & Water
tags: [C#, Mesh Generation, Shadergraph, Physics]
tools: [C#, Mesh Generation, Shadergraph, Physics]
image: ../assets/WaterAndBuoyancy/Water.gif
description: In this project I created a water system in conjuction with a buoyancy effect for unity rigidbodies
status: wip
---

# **Buoyancy & Water**

---

### **Skills**
- Unity
- C#
- Curve Graphs
- Animation Rigging

<p>&nbsp;</p>

## **Introduction**


---

A common method of producing a <span style="color:orange">**Fire Effect**</span> in **Unity** is to use a **Particle System** or **VFX Graph**, however the many transparent quads used for each particle creates overdraw which can affect performance, especially for mobile platforms.

Usually when rendering Opaque geometry, Unity renders objects front-to-back, allowing z-testing to take place; objects obscured by closer objects have their fragments discarded from being rendered, avoiding unnecessary calculations. However when rendering Transparent geometry, Unity has to render those objects back-to-front instead, in order to achieve the correct alpha blending between transparent objects. If a scene contains many of these particles systems the Transparent queue can get filled very quickly, resulting in an overcomplex scene that eats away a large portion of the overall graphics budget.

A different technique that mitigates this issue is to offset a single quad's UVs, using noise, to create a fire effect. This reduces the amount of transparent objects per fire effect to one, means many of these fires can run in parallel on the GPU, and also takes less calulations than the particle system. I first saw this technique at an Unreal Engine talk about **[RiME Stylized VFX](https://youtu.be/fwKQyDZ4ark)**.

{% include elements/video.html id="fwKQyDZ4ark" %}

Based on this concept I’ve made multiple versions of a fire shader to try replicate the benefits, one using procedural noise and shapes, and the other using premade textures.

---

{% capture list_items %}
Procedural
{% endcapture %}
{% include elements/list.html title="Contents" type="toc" %}

---

### **Procedural**

---

In order to create the movement needed for the <span style="color:orange">**Fire Effect**</span>, I've sampled the **Time** and multiplied it by a float to control the magnitude of the scrolling. To get the scrolling to move upwards I inverted it, as the default **Time** output scrolls the texure downwards. I then used this value as the Y component of a **Vector2**, leaving the X component as 0, which is used as the offset for the **Tiling And Offset Node**. I used a **Vector2 Property** to control the scale of the tiling, allowing the scale of the noise to changed in the **Inspector**. The scrolling UV's from the **Tiling And Offset** will then be used as the UV input for a **[Simple Noise Node](https://docs.unity3d.com/Packages/com.unity.shadergraph@7.1/manual/Simple-Noise-Node.html)** and **[Voronoi Node](https://docs.unity3d.com/Packages/com.unity.shadergraph@6.9/manual/Voronoi-Node.html)**.

{% include elements/figure.html image="../assets/StylizedFire/Fire2Graph1.gif" caption="Add Caption" %}

This noise is used later to offset the UVs to create fire like movement. Unlike offsetting by a constant value or time (which changes, but is the same value for every UV coordinate), we are shifting each UV coordinate by a different value, which produces a distortion (aka warping) effect. We also should Subtract 0.5, so the distortion is centered and doesn’t offset more in one direction than the other.

Multiply this with a Vector1 property named Distortion Strength. We also want the top of the fire to be distorted more than the bottom, so we also Multiply with the Y axis of the UV (G output from Split). We put this result into the Y axis of a Vector2, then into the Offset input on a Tiling And Offset node to apply our distortion, only in the vertical direction.

The distortion will mean our UVs could go outside of the 0-1 range, so parts of the texture/ellipse later could be cut-off by the size of the quad. To help fix this, we’ll also include a Vector1 property called Y Scale and put it into the Y of a Vector2, with X as 1, into the Tiling input. This will allow us to scale down the effect in the vertical direction, to try to keep the fire contained to the quad.

{% include elements/figure.html image="../assets/StylizedFire/Fire2Graph2.gif" caption="Add Caption" %}

While we could take our UVs and directly put them into an Ellipse node, I’d like to apply a bit more offset first, to warp it into a more tear-drop, or candle-flame like shape. There might be a few ways to achieve this, but I’m taking the Y axis (G output from a Split), using One Minus to flip it upside-down and putting the result into a Power node with a B value of 2. (I guess this could also just be a Multiply, not sure if the compiler is clever enough to optimise this automatically or not). The next One Minus flips the vertical component back, but since the ellipse is centered and symmetrical it shouldn’t really matter.

{% include elements/figure.html image="../assets/StylizedFire/Fire2Graph3.gif" caption="Add Caption" %}

With our edited UVs, we’ll put them into two Ellipse nodes. The first with a Width of 0.7 and Height of 0.5, and the other with 1 and 1.

We’ll also take the output from the Power in the previous step, Add 0.3, Multiply by 1.5 and put the result into a Posterize with 3 Steps. These values were mostly trial and error to ensure the fire had a nice amount of coloured bands, to help with a more stylised look.

This is added with the smaller Ellipse output, then we can tint the fire by multiplying with a Color property (which is also set to HDR mode and has an Intensity of 1.5) and plug the result into the Color input on the Unlit Master node. Be sure to also set the surface type to Transparent via the small cog on the node.

The larger Ellipse is used as the Alpha input on the Master node if using Alpha blending. If you prefer Additive blending, Multiply this with the current colour result and use that as the Color input instead, as black areas will show as transparent.

{% include elements/figure.html image="../assets/StylizedFire/Fire2Graph4.gif" caption="Add Caption" %}

{% include elements/figure.html image="../assets/StylizedFire/StylizedFire2.gif" caption="Add Caption" %}

---


