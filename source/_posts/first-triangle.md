---
title: 2. First Triangle
date: 2016-11-21 20:12:40
categories: 
- Computer
- Game Engine
tags:
- C++
- OpenGL
---

## Add GLEW

[GLEW](http://glew.sourceforge.net/)

> The OpenGL Extension Wrangler Library (GLEW) is a cross-platform open-source C/C++ extension loading library. GLEW provides efficient run-time mechanisms for determining which OpenGL extensions are supported on the target platform.

Download the library from the above link and add it to your project.

*Note: If you use the static library of GLEW - glew32s.lib, you have to set the GLEW_STATIC in the preprocessor. Otherwise it won't work*

{%asset_img 1.PNG "Set the preprocessor for static linker" %}


## Point  

Now all is set, let's draw one single point in the window to get a sense of how OpenGL works.

#### Include your library

`#include <GL/glew.h>`

#### Initialize GLEW

```c++
if (glewInit() != GLEW_OK)
{
  return 1;
}
```

#### Create the point

Consider the point is 3-dimensional point at (0.0, 0.0, 0.0).

```c++
static const GLfloat vertices[] = {
    0.0f, 0.0f, 0.0f,
}
```

#### Generate a Vertex Buffer Object

Vertex Buffer Object is the way you send your data to GPU. We store our vertices data in the buffer and submit the buffer to GPU.

```c++
GLuint VBO;
glGenBuffers(1, &VBO)
// the first parameter is the how many Vertex Buffer Objects you want to generate. The second parameter is the buffer.
```

#### Bind buffer
The buffer is generated in a generic way. It doesn't know what type of the data is. It's the job of the next function to tell the buffer what we intend to do with the buffer.

```c++
glBindBuffer(GL_ARRAY_BUFFER, VBO);

// Tell GPU the data to store in it is an array of vertices.
```

#### Bind data

Now we are going to bind our data to the buffer.

```c++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

#### Enable Vertex Attributes

Following code is to write in the main loop.

By default of the pipeline, for vertices without shader the index we are referring to is 0.
```c++
glEnableVertexAttribArray(0);
```

#### Interpretation of the data

Tell GPU how to interpret the data in buffer.

```c++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, 0);
```
The first parameter: index of the attribute
The second parameter: number of components(3 for X, Y and Z)
The third parameter: data type of each component
The forth parameter: weather to be normalized before it is used in the pipeline.
The fifth parameter: stride
The sixth parameter: array buffer offset.


#### Draw the Point

```c++
glDrawArrays(GL_POINTS, 0, 1);
```

Now you should see a point in the middle of the window.


#### Draw the triangle

replace the data point with

```c++
static const GLfloat vertices[] = {
    0.0f, 1.0f, 0.0f,
    -1.0f, 0.0f, 0.0f,
    1.0f, 0.0f, 0.0f,
};
```


And the draw function.

```
glDrawArrays(GL_TRIANGLES, 0, 3);
```

Then you should see

{%asset_img 2.PNG "The first triangle" %}
