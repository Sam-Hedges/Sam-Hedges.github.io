---
title: "Beginner's Guide to Game Development"
tags: [Development]
style: fill
color: primary
description: Welcome to the exciting world of game development! If you've ever dreamed of creating your own game but felt overwhelmed about where to start, this guide is for you. We'll cover the basics, from learning programming to launching your game, ensuring you have a solid foundation to begin your journey.
---

Welcome to the exciting world of game development! If you've ever dreamed of creating your own game but felt overwhelmed about where to start, this guide is for you. We'll cover the basics, from learning programming to launching your game, ensuring you have a solid foundation to begin your journey.

---

## 1. Basic Computer Function
  
CPUs, or Central Processing Units, are the brains of computers and operate using binary, a base-2 numeral system that represents data with: 0 and 1. ON or OFF.

A normal 0-9 number line is a base-10 numeral system. The level of base just represents how many digits a number can contain. In the case of binary, numbers can look like 0100110. 

In our more common base-10 system we use digits 0-9 for numbers such as 10989827.

Each binary digit (or bit) is a fundamental unit of information in computing, signifying the electrical state of a single transistor within the CPU. Complex instructions and data are processed by the CPU as sequences of bits, performing operations such as arithmetic, logic, control, and input/output. 

Abstraction in computing refers to the extent to which these basic elements of binary can be made more human readable. 

For simplicities sake, assembly language serves as a thin layer of abstraction over a computer's binary machine code. It uses symbols to represent binary instructions, making it slightly easier for humans to read and write. Despite its closer proximity to human-readable language, assembly programming requires a deep understanding of a computer's hardware and architecture.

---

## 2. Programming Languages

This is where Programming Languages come in. A programming language is a formal language comprising a set of instructions that produce various outputs.

These instructions are more abstract than the symbols computers use to function, so that programmers can easily write code that is human readable. This gives us a form to write out logical instructions that we can understand that can then be translated into a form computers can understand. 

For example this following code is what you would call pseudo-code. This is just an example of pure logical instructions written down in the format of code to make it as human readable as possible. This doesn't represent the actual words you would write but the layout of the logic.
```cpp
// Check if the player has picked up a power-up
if (player touches power-up) {

    // Increase the player's score by 100 points
    add 100 to player's score

    // Display a message to the player
    show "Power-up collected!" on screen
}

// Check if the player has encountered an enemy
if (player encounters enemy) {

    // Decrease the player's health by 20 points
    subtract 20 from player's health

    // If the player's health drops to 0 or below, the player loses the game
    if (player's health <= 0) {
        show "Game Over!" on screen
    }
}
```
*Pseudo Code*

In the context of game development, programming languages like C++ and C# are often used to write the code that dictates game mechanics, physics, graphics, and more.

The code written by developers is then compiled into executables, which are files that your computer can run as programs. This compilation process translates the high-level, human-readable code into low-level machine-readable code, such as assembly.

---

## 3. Object-Oriented Programming (OOP)

READ THIS FIRST: Don't worry about completely absorbing this section first time around. These concepts are best learnt through actually writing code and as such this is here so you have better context for other sections. Don't worry about remembering it but it's still important.

TLDR: OOP is a style in which we commonly write code for games

Object-oriented programming (OOP) is a programming paradigm based on the concept of "objects", which can contain data, in the form of fields (often called attributes or properties), and code, in the form of procedures (often known as methods / functions). OOP is a fundamental approach in game development because it mirrors the way we can think about game elements in the real world as objects with properties and behaviors.

### Key Concepts of OOP

- **Classes and Objects:** At the heart of OOP are classes and objects. A class is a blueprint for creating objects (a particular data structure), providing initial values for state (member variables or attributes), and implementations of behaviour (member functions or methods). Objects are instances of classes created with specific data.
    
- **Encapsulation:** This principle is about bundling the data (attributes) and methods that operate on the data into a single unit or class and restricting access to some of the object's components. This prevents external code from directly accessing and changing the internal representation of the object.
    
- **Inheritance:** Inheritance allows a class to use methods and properties of another class. In game development, this can be used to create a general class (like `Enemy`) and then create more specific subclasses (like `Orc` or `Troll`) that inherit properties and methods from the `Enemy` class, allowing for code reuse and organization.
    
- **Polymorphism:** Polymorphism lets a function or method operate in many different ways, depending on the context. For instance, a single function `attack()` could behave differently based on whether it's called on an `Orc` object or a `Troll` object, even though both are subclasses of `Enemy`.
    

### Applying OOP in Game Development

In game development, OOP is used to model complex game systems in an understandable way. Here are some practical applications:

- **Creating Game Characters:** Each character in a game can be represented as an object with attributes like health, strength, and speed, and methods like `move()`, `attack()`, and `defend()`. Characters can inherit from a general `Character` class but have their unique implementations.
    
- **Managing Game States:** Different states of the game (such as the main menu, in-game, pause menu) can be managed using OOP by creating a class for each state and defining methods to enter or exit each state.
    
- **Handling User Interface (UI) Elements:** UI elements like buttons, sliders, and menus can be designed as objects, allowing for modular, reusable, and easily manageable code.
    
- **Level Design:** Different levels or stages in a game could be represented as objects, making it easier to load, unload, and switch between levels dynamically during gameplay.
    

OOP in game development not only helps in organizing and structuring code but also makes it easier for teams to collaborate and modify code without affecting other parts of the game. By leveraging OOP principles, developers can create more dynamic, scalable, and maintainable games.

---

## 4. Different Levels of Programming Languages

Understanding the different levels of programming languages, such as C, C++, and C#, is crucial for game developers. These languages operate at various levels of abstraction and serve different purposes in the development process.

- **C:** Considered a low-level language, C provides minimal abstraction from assembly language. It offers direct manipulation of hardware and memory, which is essential for system programming and resource-constrained applications. In game development, C is  not commonly used any more due to how much more difficult it is to work with than alternative higher-level languages.
    
- **C++:** C++ is a middle-level language that offers a balance between high-level abstraction and low-level control. It extends the C language with object-oriented features, allowing more complex and modular code design. C++ is widely used in game development for writing game engines, game logic, and performance-sensitive parts because it combines the efficiency of C with the benefit of OOP, enabling developers to create scalable and maintainable codebases.
    
- **C#:** A high-level language, C# simplifies development with its comprehensive standard library and automatic memory management. It is the primary language for developing games in the Unity engine, offering a user-friendly environment with less concern about low-level hardware interactions. C# abstracts away many of the complexities associated with lower-level languages, allowing developers to focus on game logic and design without delving into the intricacies of memory management and hardware control.
    

Understanding the differences between these programming levels is important because it helps developers choose the right language for the specific needs of their game project. Lower-level languages offer more control but require more detailed management from the developer, while higher-level languages simplify many programming tasks at the cost of some performance and control.

---

## 5. Integrated Development Environments (IDEs)

An Integrated Development Environment (IDE) is a software application that provides comprehensive tools to programmers for software development. They are designed to maximize productivity by providing an all-in-one environment that includes a source code editor, compiler/interpreter, build automation tools, and debugger.

You could technically write code in a text-editor such as Microsoft word or notepad however those tools either have no features or are catered to writing human language. IDEs provide useful features such as highlighting errors within code, code completion, etc.

- **Source Code Editor:** A text editor designed for writing and editing code with features like syntax highlighting, code completion, and code navigation that make it easier to write clean code efficiently.
- **Compiler/Interpreter:** Translates the written code into a language that the computer can understand and execute. IDEs typically integrate these tools to streamline the build and run process.
- **Build Automation Tools:** Automate common development tasks like compiling source code into binary code, packaging binary code, and running automated tests.
- **Debugger:** A program used to test and debug other programs. IDEs incorporate debuggers that allow developers to execute their code step by step, inspect variables, and diagnose issues within the code.

IDEs play a crucial role in game development by simplifying the coding process, reducing the chance of errors, and making code management more efficient. 

 **To begin with programming and game development I would recommend using Visual Studio (widely used with C++ and C#). They have a free community license and provides an easy out of the box working experience. They also have modules you can download that integrate with game engines to make development easier.**

---

## 6. Game Engines

A game engine is a software development tool designed for people to build video games.

Developers use game engines to create games for consoles, mobile devices, and personal computers. 

Game engines come with important tools and features such as physics engines, rendering engines, scripting, animation, and AI capabilities. 

Examples of popular game engines include Unity, Unreal Engine, and Godot Engine. These engines provide a framework and set of tools that significantly speed up the development process, allowing developers to focus on unique aspects of their game rather than building from scratch.

Some engines use proprietary languages which means that those programming languages are only in use for that engine. I'd steer clear from this as it limits you when using different tools or engines.

**I'd recommend using the Unity Game engine. It uses the C# language for programming which is the easiest language to get started with, and also a transferable language.**

---

## 7. Source Control

Source control, also known as version control, is a system that records changes to a file or set of files over time so that you can recall specific versions later. 

For game development, platforms like GitHub are essential for team collaboration, as they allow multiple developers to work on different parts of a game simultaneously without overwriting each other's contributions. 

Source control systems manage updates and ensure that changes are tracked, making it easier to debug issues or revert to previous versions of the project if necessary.

**I'd recommend using Github and their desktop app Github Desktop to start with source control.**

---

## 8. How It All Comes Together

Programming and game engines are distinct yet complementary skills in the realm of game development. 

While programming involves writing the code that dictates the behavior and mechanics of the game, game engines provide the tools and functionalities to bring these codes to life in a visual and interactive form. 

Learning how to use game engines and programming languages in tandem is crucial for developing games efficiently.

---

## **9. Learning Programming**

**Why Learn Programming?**  
Programming is the backbone of game development. It allows you to bring your ideas to life, from simple mechanics to complex systems that engage players.

**Languages to Learn:**

- **C:** Useful for understanding low-level programming concepts.
- **C++:** Widely used in game development for its performance and control.
- **C#:** Popular in game development, especially with the Unity engine.

**Goals:**

Distinguish between your interest in game development and programming. While intertwined, each requires dedication to master. Consider what excites you more: creating the game itself or the coding that makes it possible. 

**Practice Coding**

**Online Platforms:**

- **YouTube, Codecademy, and Brilliant:** Great for beginners to learn coding fundamentals.
- **LeetCode:** Challenge yourself with coding problems to sharpen your skills. (I personally do this one alot)

Just create and code as much as you can. Practice makes perfect and the sooner you get 10,000 hours in, the better you will be. Try coding anything, calculator, to-do app, etc. You can create basic programs that just output text to a console and reads the text you type. 

In Visual Studio this is called a console app and is a great way to explore coding without the baggage of a game engine.

---

## **10. Dive Into Game Development**

**Understand the Basics:**

- Learn about variables, functions, control structures, loops, and conditional statements.
- Continuous learning: Read documentation, follow tutorials, and consider online courses.

**Create Simple Games:**

- Begin with text-based games or 2D platformers to understand game mechanics and design principles.

---

## **11. Continuous Learning and Improvement**

To improve it's constantly about learning new things. Try to avoid using any AI tools such as ChatGPT to learn. It can be useful if you're having a hard time finding a specific answer but it is terrible at teaching broad topics, and if you use to to generate code then you are just depriving yourself of the opportunity to fail and learn from those lessons. It will become a crutch and you will end up not being able to think without out it.