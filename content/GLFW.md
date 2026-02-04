---
title: GLFW
publish: "true"
---
What are window hints in `GLFW`?

```cpp
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
```

GLFW window hints are configuration settings that tell GLFW how to create a window and OpenGL context.

 How they work:
  - You call glfwWindowHint() before creating a window
  - They specify requirements/preferences for the window/context
  - They only affect the next window you create

Without these hints, you might get an old OpenGL version that doesn't support modern features.

---

What is a window context is `GLFW`?

```cpp
glfwMakeContextCurrent(window);
```

An OpenGL context is a state machine that holds all OpenGL data and settings for rendering.

  What it contains:
  - All OpenGL objects (textures, [[Buffers, what are they?||buffers]], shaders, etc.)
  - Current OpenGL state (active shader, bound textures, blend mode, etc.)
  - Settings and configurations

  Why you need it:
  - [[OpenGL|OpenGL is not a library - it's a specification]]
  - The context is the actual implementation that executes OpenGL commands
  - No context = no OpenGL functions work

  `glfwMakeContextCurrent(window)`:
  - Binds the context to the current thread
  - All subsequent OpenGL calls affect this context
  - Required before calling any gl*() functions

  Key points:
  - One context per window (usually)
  - Only one context can be current per thread
  - OpenGL commands only work on the current context
  - If you switch windows, you need to switch contexts

  Example flow:
  ```cpp
  GLFWwindow* window = glfwCreateWindow(800, 600, "Game", NULL, NULL);
  glfwMakeContextCurrent(window);  // ← Now this thread can use OpenGL
  gladLoadGLLoader(...);           // Load OpenGL functions
  glClearColor(0.2f, 0.3f, 0.3f, 1.0f);  // ← Works because context is current
  ```
  
  Without `glfwMakeContextCurrent()`, OpenGL functions would have nowhere to operate.

---
What is this `gladLoadGL(glfwGetProcAddress);`for?

**The Problem**: OpenGL Functions Don't Exist Yet

Here's the weird part: When you compile your C++ program, OpenGL functions like glClearColor(), glDrawArrays(), etc. don't actually exist in your executable.

Why? Because OpenGL is implemented by your GPU driver, not by a library you link to.

Think of it like this:
Your Code: "Hey, I want to call glClearColor()"
Compiler: "I don't know where that is..."
GPU Driver: "I have that function, but you need to ask me where it lives in memory"

**The Solution**: Function Pointers

OpenGL functions are function pointers that need to be loaded at runtime.

Analogy: Imagine a phone book:
- Your code knows the function name ("glClearColor")
- But it doesn't know the phone number (memory address)
- You need to look it up in the phone book (GPU driver) first