---
title: A Basic Ray Tracing Renderer
categories:
- Computer
- Game Engine
tags:
- C++
---

['Tinny Ray Tracer'](https://github.com/ssloy/tinyraytracer) introduces the ray tracing under 300 lines code.
It was a great fun to follow the instructions and made the renderer. 
We learn the algorithm layer by layer, with one color at beginning, then with diffusion, reflection and refraction at last.

This article add some extended notes and resources together 
for a thorough understanding of geometric calculation along with ray tracing.
{% asset_img final.jpg "Tiny Renderer" %}

<!-- more -->

## Preparation

All we need is C++ and a bit theory of ray tracing, no other fancy libraries.
We can dive into the core directly without any configuration of OpenGL.
In order to keep it simple, we will render the image into a file, instead of using OpenGL to draw in the window.

To make the programming with multi-dimensional vectors easy, we would better to define some templates for vectors.

You can skip this part and just include "geometry.h". 
It is not essential, for implementing the main part.

```c++
#include <cassert>
#include <cmath>
#include <vector>
template <size_t DIM , typename T> struct vec {
  vec() { for (size_t i=DIM; i--; data_[i] = T()); }
  T& operator[](const size_t i) { assert(i<DIM); return data_[i]; }
  const T& operator[](const size_t i) const { assert(i<DIM); return data_[i]; }
private:
  T data_[DIM];
};

typedef vec<2, float> Vec2f;
typedef vec<3, float> Vec3f;

template <typename T> struct vec<3, T> {
  vec() : x(T()), y(T()), z(T()) {}
  vec(T X, T Y, T Z) : x(X), y(Y), z(Z) {}
  T& operator[](const size_t i) { assert(i<3); return i<=0 ? x : (1==i ? y : z); }
  const T& operator[](const size_t i) const { assert(i<3); return i<=0 ? x : (1==i ? y : z); }
  T x,y,z;
};
```

## Step 1: Draw an image with colors

Let's simply paint some color first, and output it to a pixmap file. 
We use [PPM](https://en.wikipedia.org/wiki/Netpbm). It is a very simple format for pixmap.

The file format looks like this:

    P6        // this is the format name
    1024 768  // height and width of the image
    255       // use 256-color
    0 0 0 120 120 0 .... // RGB color number

```c++
<<geometry>>

#include <iostream>
#include <cmath>
#include <vector>
#include <fstream>

void output(const int width, const int height, std::vector<Vec3f> framebuffer) {
  std::ofstream ofs; // save the framebuffer to file
  ofs.open("./out.ppm");
  ofs << "P6\n" << width << " " << height << "\n255\n";

  for (size_t i = 0; i < height*width; ++i) {
    for (size_t j = 0; j < 3; ++j) {
      ofs << (char)(255 * std::max(0.f, std::min(1.f, framebuffer[i][j])));
    }
  }
  ofs.close();
}

void render() {
  const int width = 1024;
  const int height = 768;

  // framebuffer to store RGB data of all pixels
  std::vector<Vec3f> framebuffer(width*height);

  for (size_t j = 0; j < height; j++) {
    for (size_t i = 0; i < width; i++) {
      framebuffer[i+j*width] = Vec3f(j/float(height), i/float(width), 0);
    }
  }

  output(width, height, framebuffer);
}

int main() {
  render();
  return 0;
}

```

You should see this result:

{%asset_img step1.jpg "Step 1: create an image" %}

## Step 2: Draw a sphere

Here comes the main part: Ray Tracing.

![How Ray Tracing Works](https://upload.wikimedia.org/wikipedia/commons/8/83/Ray_trace_diagram.svg)

> It works by tracing a path from an imaginary eye through each pixel in a virtual screen,
> and calculating the color of the object visible through it.


Each ray must be tested for intersection with some object in the scene. 
Once the nearest object has been identified, the algorithm will estimate 
the incoming light at the point of intersect and calculate the final color of that pixel.

If the ray encounters a reflective or translucent materials, 
it requires more rays to re-cast(recursion problem) from that point into the scene.

We need a sphere. To represent a sphere, we only need a 3-dim vector to mark its center,
and a scalar to determine its radius.

We also need a function to check the intersection.


```c++
struct Sphere {
    Vec3f center; 
    float radius;

    Sphere(const Vec3f &c, const float &r) : center(c), radius(r) {}

    bool ray_intersect(const Vec3f &orig, const Vec3f &dir, float &t0) const {
        Vec3f L = center - orig;
        float tca = L*dir;
        float d2 = L*L - tca*tca;
        if (d2 > radius*radius) return false;
        float thc = sqrt(radius*radius - d2);
        t0 = tca - thc;
        float t1 = tca + thc;
        if (t0 < 0) t0 = t1;
        if (t0 < 0) return false;
        return true;
```

To check if a ray intersect a sphere, we draw a line through center, perpendicular to the ray.
The length of of this line is `d`. We only have to check if `d` larger or smaller then the radius.

{% asset_img raysphereisect1.png "Line Sphere Intersection" %}


There are three types of intersection with the ray:

1. light source is before the sphere
2. light source is in the sphere
3. light source is behind the sphere

{% asset_img rayspherecases.png "Types of Intersection" %)


