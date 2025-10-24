---
name: Tri-Planer Shader
tags: [Unity, Mobile, HLSL]
tools: [Unity, Mobile, HLSL]
image: ../assets/TriPlanerShader/TriPlaner.png
description: I was commissioned to create this shader by an Indie Studio 
---


# **Project Info**

> 👤 <span style="color:dodgerblue">**Role:**</span> Lead Programmer <br>
> 👥 <span style="color:dodgerblue">**Team Size:**</span> 1 <br>
> ⌛ <span style="color:dodgerblue">**Time Frame:**</span> 1 Week <br>
> 🛠️ <span style="color:dodgerblue">**Framework:**</span> Unity <br>

<p>&nbsp;</p>

# **Tri-Planer Lit Toon Shader**

---

During my college education a project came my way from a French Independant Developer, which, while being modest in scale, was a perfect learning opportunity. My brief was to develop a lit toon shader, specifically optimised for mobile devices. This shader was intended for an independent game and was designed to support both RGBA (Red, Green, Blue, Alpha) and HSV (Hue, Saturation, Value) colour spaces. The shader was also to include gradient control applied over its lightmaps. I also had to also ensure that the shader supported triplanar mapping, which presented its own set of challenges.

My first obstacle, lay in the integration of both RGBA and HSV colour spaces within the same shader. This requirement introduced an additional layer of complexity to the project, given that most computer graphics usually stick to one colour space. I'd also never used the HSV colour space up to this point so I had to research the correct ways to impliment it. The goal was to create a mechanism to transition smoothly between RGBA and HSV, each of which offers its unique approach to colour manipulation.

The next hurdle was the implementation of gradient control over lightmaps. Lightmaps in graphics programming offer an efficient method for creating realistic lighting effects. Introducing gradient control required an intricate understanding of how gradients could be mapped onto light intensity levels. My approach was exploratory and iterative, eventually leading to an algorithm that successfully implemented the gradient control.

Triplanar mapping was another intriguing aspect of the project. In the context of shaders, triplanar mapping allows textures to be projected onto geometry along three planes - X, Y, and Z. The challenge here was to ensure the consistency and balance of colours and textures across the three planes, and also considering the lighting and gradient control already implemented. A detailed study of the game's geometry, combined with multiple rounds of adjustments, led to a successful triplanar implementation.

The mandate of the project was also to ensure that the shader was mobile-friendly. This was about striking a balance between the quality of the shader and the limited hardware capabilities of a mobile device. Each feature had to be critically evaluated for its performance impact on the mobile device. The final shader, hence, was a compromise, providing a rich set of features while ensuring performance optimisation.

{% include elements/figure.html image="../assets/TriPlanerShader/TriPlaner.png" caption="Without Texture Mapping VS With Texture Mapping"%}

In conclusion, despite the project's small scale, the endeavour provided a wealth of learning opportunities. Each challenge pushed the boundaries of my knowledge and problem-solving abilities, offering deeper insights into the world of shader programming and game development. The final product, a mobile-friendly lit toon shader supporting RGBA and HSV colour spaces, with gradient control over lightmaps and a triplanar mapping feature, greatly enhanced the visual aesthetics of the indie game. Unfortunately due to the nature of the NDA that I signed I'm currently unable to explore the code in this article, however I can still share the final result! As my first ever paid work in games development, I thoroughly enjoyed the process and will continue to search for other available jobs.
