---
name: 🔥 Stylized Fire 🔥
tools: [Shadergraph, Particle System]
image: ../assets/StylizedFire/StylizedFire2.gif
description: In this project I recreated a toon style fire effect inspired by Tequila Works, Rime
---

# **🔥 Stylized Fire 🔥**

---

> - Unlit shader
> - Transparent surface mode 
> - Alpha or Additive blending 

---

A common method of producing a <span style="color:orange">**Fire Effect**</span> in **Unity** is to use a **Particle System** or **VFX Graph**, however the many transparent quads used for each particle creates overdraw which can affect performance, especially for mobile platforms.

- When rendering Opaque geometry, Unity renders objects front-to-back, allowing z-testing to take place; objects obscured by closer objects have their fragments discarded from being rendered, avoiding unnecessary calculations.

- However in the Transparent queue, Unity has to render those objects back-to-front instead, in order to achieve the correct alpha blending between see through objects. 

- Rendering many objects on top of each other, like transparent quads from the particle system, can cause the Transparent queue to get overfilled and you end up rendering an overcomplex scene, reducing the overall graphics budget.

A different technique that mitigates this issue is to offset a quad's UVs, using noise, to create a fire effect. This reduces the amount of transparent objects per fire to one and also takes alot less calulations than the particle system. This technique I first saw at an Unreal Engine talk about **[RiME Stylized VFX](https://youtu.be/fwKQyDZ4ark)**.

I’ve made multiple versions of a fire shader, one a bit different from the RiME talk using the **[Simple Noise](https://docs.unity3d.com/Packages/com.unity.shadergraph@7.1/manual/Simple-Noise-Node.html)** & **[Voronoi Noise](https://docs.unity3d.com/Packages/com.unity.shadergraph@6.9/manual/Voronoi-Node.html)** nodes applied to a **[Ellipse](https://docs.unity3d.com/Packages/com.unity.shadergraph@6.9/manual/Ellipse-Node.html)** node, and the other using a seamless noise texture to offset a mask texture.

---

##### **Finished Results:**

{% capture carousel_images %}
../assets/StylizedFire/StylizedFire2.gif
../assets/StylizedFire/StylizedFire.gif
../assets/StylizedFire/PixelStylizedFire.gif
{% endcapture %}
{% include elements/carousel.html height="50" unit="%" duration="5" %}

{% capture list_items %}
Procedural
Textured
Pixel Rendered
{% endcapture %}
{% include elements/list.html title="Contents" type="toc" %}

---

### **Procedural**

---

lopers

---

### **Textured**

---

lopers

---

### **Pixel Rendered**

---

lopers