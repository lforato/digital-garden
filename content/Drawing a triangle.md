---
title: Drawing a triangle
publish: "true"
---
```cpp
#include "glad/gl.h"
#include <GLFW/glfw3.h>

#include <iostream>

void framebuffer_size_callback(GLFWwindow *window, int width, int height);
void processInput(GLFWwindow *window);

// settings
const unsigned int SCR_WIDTH = 800;
const unsigned int SCR_HEIGHT = 600;

const char *vertexShaderSource =
    "#version 330 core\n"
    "layout (location = 0) in vec3 aPos;\n"
    "void main()\n"
    "{\n"
    "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
    "}\0";
const char *fragmentShaderSource =
    "#version 330 core\n"
    "out vec4 FragColor;\n"
    "void main()\n"
    "{\n"
    "   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
    "}\n\0";

int main() {
  // glfw: initialize and configure
  // ------------------------------
  glfwInit();
  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
  glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

#ifdef __APPLE__
  glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
#endif

  // glfw window creation
  // --------------------
  GLFWwindow *window =
      glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "LearnOpenGL", NULL, NULL);
  if (window == NULL) {
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
  }
  glfwMakeContextCurrent(window);
  glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

  // glad: load all OpenGL function pointers
  // ---------------------------------------

  gladLoadGL(glfwGetProcAddress);

  // build and compile our shader program
  // ------------------------------------
  // vertex shader
  unsigned int vertexShader = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
  glCompileShader(vertexShader);
  // check for shader compile errors
  int success;
  char infoLog[512];
  glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
  if (!success) {
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
              << infoLog << std::endl;
  }
  // fragment shader
  unsigned int fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
  glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
  glCompileShader(fragmentShader);
  // check for shader compile errors
  glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
  if (!success) {
    glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n"
              << infoLog << std::endl;
  }
  // link shaders
  unsigned int shaderProgram = glCreateProgram();
  glAttachShader(shaderProgram, vertexShader);
  glAttachShader(shaderProgram, fragmentShader);
  glLinkProgram(shaderProgram);
  // check for linking errors
  glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
  if (!success) {
    glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n"
              << infoLog << std::endl;
  }
  glDeleteShader(vertexShader);
  glDeleteShader(fragmentShader);

  // set up vertex data (and buffer(s)) and configure vertex attributes
  // ------------------------------------------------------------------
  float vertices[] = {
      -0.5f, -0.5f, 0.0f, // left
      0.5f,  -0.5f, 0.0f, // right
      0.0f,  0.5f,  0.0f  // top
  };

  unsigned int VBO, VAO;
  glGenVertexArrays(1, &VAO);
  glGenBuffers(1, &VBO);
  // bind the Vertex Array Object first, then bind and set vertex buffer(s), and
  // then configure vertex attributes(s).
  glBindVertexArray(VAO);

  glBindBuffer(GL_ARRAY_BUFFER, VBO);
  glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

  glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void *)0);
  glEnableVertexAttribArray(0);

  // note that this is allowed, the call to glVertexAttribPointer registered VBO
  // as the vertex attribute's bound vertex buffer object so afterwards we can
  // safely unbind
  glBindBuffer(GL_ARRAY_BUFFER, 0);

  // You can unbind the VAO afterwards so other VAO calls won't accidentally
  // modify this VAO, but this rarely happens. Modifying other VAOs requires a
  // call to glBindVertexArray anyways so we generally don't unbind VAOs (nor
  // VBOs) when it's not directly necessary.
  glBindVertexArray(0);

  // uncomment this call to draw in wireframe polygons.
  // glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);

  // render loop
  // -----------
  while (!glfwWindowShouldClose(window)) {
    // input
    // -----
    processInput(window);

    // render
    // ------
    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

    // draw our first triangle
    glUseProgram(shaderProgram);
    glBindVertexArray(
        VAO); // seeing as we only have a single VAO there's no need to bind it
              // every time, but we'll do so to keep things a bit more organized
    glDrawArrays(GL_TRIANGLES, 0, 3);
    // glBindVertexArray(0); // no need to unbind it every time

    // glfw: swap buffers and poll IO events (keys pressed/released, mouse moved
    // etc.)
    // -------------------------------------------------------------------------------
    glfwSwapBuffers(window);
    glfwPollEvents();
  }

  // optional: de-allocate all resources once they've outlived their purpose:
  // ------------------------------------------------------------------------
  glDeleteVertexArrays(1, &VAO);
  glDeleteBuffers(1, &VBO);
  glDeleteProgram(shaderProgram);

  // glfw: terminate, clearing all previously allocated GLFW resources.
  // ------------------------------------------------------------------
  glfwTerminate();
  return 0;
}

// process all input: query GLFW whether relevant keys are pressed/released this
// frame and react accordingly
// ---------------------------------------------------------------------------------------------------------
void processInput(GLFWwindow *window) {
  if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
    glfwSetWindowShouldClose(window, true);
}

// glfw: whenever the window size changed (by OS or user resize) this callback
// function executes
// ---------------------------------------------------------------------------------------------
void framebuffer_size_callback(GLFWwindow *window, int width, int height) {
  // make sure the viewport matches the new window dimensions; note that width
  // and height will be significantly larger than specified on retina displays.
  glViewport(0, 0, width, height);
}
```

VBO (Vertex Buffer Object)

What is it?

A VBO is a chunk of memory on the GPU that stores vertex data (positions, colors, normals, texture coordinates, etc.)

Analogy: Think of VBO as a storage container that lives in your graphics card's fast memory. Instead of sending vertex data from your CPU to GPU every frame (which is SLOW), you upload it
once to a VBO and it stays there.

Why do we need VBOs?

Without VBO (the old way):
CPU → sends vertex data → GPU (every single frame!)
This is like driving to the store every time you need to cook, even if you need the same ingredients.

With VBO (modern way):
CPU → sends vertex data ONCE → VBO lives on GPU → GPU uses it repeatedly
This is like buying groceries once and storing them in your fridge.

In your code:
```cpp
  // 1. Generate a VBO (create an empty container)
  unsigned int VBO;
  glGenBuffers(1, &VBO);  // VBO gets a unique ID number

  // 2. Bind it (say "I want to work with THIS buffer")
  glBindBuffer(GL_ARRAY_BUFFER, VBO);

  // 3. Upload data to the VBO
  float vertices[] = {
      -0.5f, -0.5f, 0.0f,  // vertex 1: x, y, z
       0.5f, -0.5f, 0.0f,  // vertex 2: x, y, z
       0.0f,  0.5f, 0.0f   // vertex 3: x, y, z
  };
  glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
  // This copies the 'vertices' array from CPU RAM → GPU memory (VBO)
```

`GL_STATIC_DRAW` tells OpenGL: "This data won't change often, optimize for that."

  ---
VAO (Vertex Array Object)

What is it?

A VAO is a configuration object that remembers how your vertex data is organized and which VBOs to use.

Analogy: If VBO is a storage container, then VAO is a recipe card that says:
- Which container (VBO) to use
- How the data inside is organized
- What format the data is in

Why do we need VAOs?

Without VAOs, you'd have to tell OpenGL how to interpret your vertex data every single time you want to draw something. VAOs save this configuration so you can just say "use recipe #5"
instead of repeating all the setup steps.

Think of it like this:
- VBO = the actual data (ingredients)
- VAO = the instructions for using that data (recipe)

In your code:

```cpp
// 1. Create a VAO
unsigned int VAO;
glGenVertexArrays(1, &VAO);

// 2. Bind the VAO (start recording configuration)
glBindVertexArray(VAO);

// 3. Bind and set up VBO (these steps get recorded in the VAO!)
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 4. Tell OpenGL how to interpret the data
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
//                    ^  ^     ^        ^         ^                  ^
//                    |  |     |        |         |                  |
//        location ---+  |     |        |         |                  |
//        size (x,y,z)---+     |        |         |                  |
//        data type -----------+        |         |                  |
//        normalized -----------------+         |                  |
//        stride (bytes between vertices) ------+                  |
//        offset (where to start reading) -------------------------+

glEnableVertexAttribArray(0);  // Enable this attribute
```

What does `glVertexAttribPointer` do?

It tells OpenGL: "Each vertex has 3 floats (x, y, z), they're not normalized, and the next vertex starts 3 floats away."

Location 0 refers to this line in your vertex shader:
layout (location = 0) in vec3 aPos;  // This receives the data!
