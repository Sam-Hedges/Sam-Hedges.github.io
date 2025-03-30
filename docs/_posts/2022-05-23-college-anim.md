---
title: "An Exploration of Animation"
tags: [Animation]
style: fill
color: warning
description: I originally wrote this essay as part of my university coursework, but have adapted it into a blog format. This explores the history, techniques, and applications of cel-shading in computer graphics, video games and animation. It covers early use, advanced techniques, popularity in the gaming industry, and other topics such as VR/AR technology, indie games, animation, mobile games and comic book influences.
---

In my college animation project, I chose to work with a rigged model of Aang, the central character from the popular animation series "Avatar: The Last Airbender". My original objective was to replicate one of the most iconic scenes from the show's opening sequence: the previous Avatars using their bending abilities. I wanted the create the whole sequence in which Aang, as the Avatar, bends every element (Fire, Earth, Water & Air), however as my first delve into animation, the process of learning to animate as I went along, took longer than anticipated so in the end I was only able to recreate the water bending portion. As I started I knew the real challenge would be to create a visually compelling and realistic depiction of the elements being manipulated.

As a starting point, I decided to focus on Aang's movements and postures, as it is his actions that manipulate the water in the scene. Using the Maya animation toolkit, I was able to develop a smooth, fluid sequence of movements for Aang that closely matched his movements in the original scene. This involved a significant amount of fine-tuning to ensure that Aang's motions were not only natural but also conveyed the character's distinctive, masterful control over the elements.

With Aang's animation in place, I turned my attention to the water. Water bending, as it is represented in the series, is a complex, dynamic process that involves the water moving in response to the bender's actions. To recreate this, I opted to use Blend Shapes on an icosphere, as this version of a sphere has all of its vertices spread evenly across the mesh allowing for smoother looking deformations. This method allowed me to generate a variety of shapes for the water, simulating the fluid, ever-changing nature of this element. The Blend Shapes essentially acted as morph targets, enabling me to animate the water by smoothly transitioning from one preset shape deformation to another in response to Aang's movements.

{% include elements/figure.html image="../assets/Avatar/BlendShapes.gif" caption="Blend Shapes applied to a sphere"%}

However, during the animation process, I encountered a substantial technical hurdle. The water's response to Aang's movements was not as immediate or as accurate as it should be. It created an uncanny lag between Aang's movements and the water's response, disrupting the immersion and making the animation appear less believable.

Identifying the root cause of the problem required some deep diving into the technicalities of the animation process. Upon thorough inspection, I discovered the issue lay in the alignment of the Blend Shape's deformation order in the Maya animation hierarchy. This was causing a delay in how the Blend Shapes were interpreting the information from Aang's movements and translating it into the water's motion.

To resolve this, I delved into Maya's node editor to manually adjust the deformation order, ensuring the Blend Shape deformations correctly aligned with the animation timeline. After several rounds of adjustments and previews, I achieved a much more responsive interaction between Aang and the water.

<video width="100%" muted autoplay controls>
    <source src="../../../assets/Avatar/AvatarAnimation.mp4" type="video/mp4">
</video>

Creating this animation of Aang water bending was not just an exercise in technical skills, but also a study in problem-solving. The experience reinforced the importance of understanding the intricacies of the tools I was using and thinking creatively to overcome the challenges that arose. I'm incredibly proud of the final product, and I believe that it authentically captures the essence of the original scene while showcasing the potential of modern animation techniques. Although the animation is not perfect, I feel it was a solid attempt for my first ever go at animating a fully rigged character.