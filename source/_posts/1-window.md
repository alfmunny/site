---
title: 1. Window
date: 2016-11-13 23:15:31
categories: 
- Computer
- Game Engine
tags:
- C++
- OpenGL
---

## Create Project

Create am empty visual C++ project in Visual Studio([Get Visual Studio](https://www.visualstudio.com/downloads/)).

## Add GLFW

To write a game engine, we start with the window at first.

OpenGL doesn't concern about the management of windows and inputs. Therefor we use
GLFW to deal with it. [GLFW](http://www.glfw.org/) is an Open Source, multi-platform library for OpenGL, which provides a simple API for creating window and receiving input.

Following the steps to setup GLFW library.

### add dependencies

Create a "Dependencies" directory in the root folder of the project.

Download GLFW and put the library in "Dependencies".

### include

Add the include directory in the property.

{%asset_img window-1.PNG "Add include directory" %}

### library

Add the library directory in the property.

{%asset_img window-2.PNG "Add library directory" %}

## Main function

Create a `main.cpp` in the root directory.


```C++
#include <iostream>
#include <GLFW/glfw3.h>

int main()
{
    if(!glwInit())
      std::cout << "Failed the initialize GLFW" << std::endl;
    else
      std::cout << "Success!" << std::endl;

    system("PAUSE");
    return 0;
}
```

Run it to see if the GLFW works as we expect.

If it succeed, we can implement our first window.

```C++
#include <iostream>
#include <GLFW/glfw3.h>

int main()
{
  if (!glfwInit())
    return 1;

  //create a window with 800px width and 600px height, name it as what you like
  GLFWwindow *window = glfwCreateWindow(800, 600, "MyEngine", NULL, NULL);

  if (!window)
  {
    std::cout << "Failed to initialize GLFW " << std::endl;
    return 1;
  }
  glfwMakeContextCurrent(window);
  while (!glfwWindowShouldClose(window))
  {
      glfwPollEvents();
      glfwSwapBuffers(window);
  }
  glfwTerminate();
  system("PAUSE");
  return 0;
}
```

Now you should see a window.

In the next chapter we are going to include the OpenGL Extension Wrangler Library and draw our first triangle.
