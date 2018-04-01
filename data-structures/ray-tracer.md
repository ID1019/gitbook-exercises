# Ray Tracer

## Introduction

The goal of this assignment is that your should practice representing data using tuples and structs and, understand how a program can be divided into modules to implement abstract data structures.

This is also an exercise in how to model a complex system and refresh your knowledge of vector arithmetic. Hopefully it's also quite fun to see how you can create your own images of a three dimensional world.

## A Ray Tracer

In order to explain a ray tracer in a three dimensional world, we will describe all necessary steps using a two dimensional model. Since we will describe everything using vector arithmetic it can easily be extended to a three dimensional model. Images generated in a two dimensional world will of course not be very thrilling but we will be able to describe the necessary steps.

In Fig.~\ref{fig:world} we see a sphere in a two dimensional Cartesian coordinate system. A **camera** is also positioned in the room consisting of a **canvas** and an **eye**. When generating an image we need to trace as many rays as possible starting in the eye of the camera and passing through the **pixels** of the canvas. If we can determine that a ray intersect with an object we can colour the pixel thus generating an image on the canvas.

-&gt; Camera image

The beauty of vector arithmetic is that if we understand how to do the necessary calculations in a two dimensional space then we also know how to do it in a three dimensional space. If we extend this model to a three dimensional world, we would have a sphere instead of a circle and the canvas would be a rectangular plane. The eye would still be a point in this space and we would track rays from the eye through each _x-y pixel_ of the canvas.

To model our world and do the necessary computations we need to solve the following problems:

* represent and do calculations on vectors and rays
* represent objects such as spheres
* determine if a ray intersects an object
* represent a camera i.e. the location of the eye and the canvas

When we solve these problems we will try to separate them as much as possible form each other. We will divide our system into modules were each module is responsible for one sub-problem or abstraction. If we do it right the main program can describe operations in a much higher level; instead of talking about x, y and z coordinates it can talk about rays, objects and intersections.

## Vectors and Rays

The first task is to create a module that will handle all vector operations. We will need to represent vectors and be able to do the following operations.

* $a\vec{x}$ : scalar multiplication
* $\vec{x} - \vec{y}$ : subtraction
* $\vec{x} + \vec{y}$ : addition
* $\\|\vec{x}\\|$ : norm, or length, of a vector
* $\vec{x} \cdot \vec{y}$ : scalar product \(dot product\)
* $\|\vec{x}\|$ : normalised vector

If we restrict the system to only work with three dimensional vectors we have a natural way of representation: a tuple with three elements, the $x$, $y$ and $z$ components i.e. `{x, y, z}`. 

Create a new file `vector.ex` and declare a new module with the following exported functions.

```elixir
defmodule Vector do

  def smul({x1, x2, x3}, s) do ... end

  def sub({x1, x2, x3}, {y1, y2, y3}) do ... end

  def add({x1, x2, x3}, {y1, y2, y3}) do ... end

  def dot({x1, x2, x3}, {y1, y2, y3}) do ... end  

  def normalize(x) do ... end

  def norm({x1, x2, x3}) do ... end

end
```

### Vector Arithmetic

The first functions, scalar multiplication, addition and subtraction should be quite easy to implement.

$$\langle x\_1, x\_2, x\_3 \rangle  s =  \langle x\_1s, x\_2s, x\_3s \rangle$$

$$\langle x\_1, x\_2, x\_3 \rangle + \langle y\_1, y\_2, y\_3\rangle = \langle x\_1+y\_1, x\_x+y\_2, x\_3+y\_3 \rangle$$

To implement the norm, dot product and normalisation of a vector you might have to go through your book in linear algebra but you should have it up and running quite quickly.

$$\\|\vec{x}\\| = \sqrt{x\_1^2 + x\_2^2 + x\_3^2}$$

$$ \vec{x} \cdot \vec{y} = \langle x\_1\cdot y\_1 + x\_2\cdot y\_2 + x\_3\cdot y\_3\rangle $$ 

$$ \|\vec{x}\| = \vec{x}/\\|\vec{x}\\|$$

In this implementation we actually expose the representation of a vector i.e. the users of this module will know that vectors are represented by tuples with three elements. This is not the best solution but unfortunately a very convenient solution.

### Rays

We will however create a more proper data structure to represent a **ray**. A ray has an origin, represented by a vector, and a direction, represented by a normalised vector. When we implement rays we will use Elixir structs. A struct is always associated with a module and gives us a convenient way of referencing elements of a data structure. Instead of having to remember which element of a tuple a particular element is we can refer to them by name. 

Create a module `Ray` and define the following struct. We provide a default position `{0, 0, 0}` and direction `{1, 1, 1}`. If none is given when we create a ray the default values will be used.

```elixir
defmodule Ray do

  defstruct pos: {0, 0, 0}, dir: {1, 1, 1}
  
end
```

We now have the mathematical tools to describe a world of objects.

## Objects

Our next task is to create data structures that can represent the objects in our world. We will keep things simple and only handle spheres. We should however implement the spheres in a way that makes it easy to add new objects in the future.

We will use Elixir **protocols** to define a common interface that all objects should implement. Similarly to defining a module we define a protocol called `Object`. The protocol will so far only specify one function `intersect/2` that will determine if a ray intersects an object.

```elixir
defprotocol Object do

  def intersect(object, ray)
  
end
```

As we define different object we will instantiate the `Object` protocol by defining the `intersect/2` function. 

### Spheres

When we decide on the representation we need to think about the operations we should perform; a representation is efficient if it allows the operations to be efficiently implemented. When we are talking about rays and spheres, we might not have much choice but it's important to start thinking about the operations that should be performed.

The operations that we will perform over and over again is to determine if a ray intersects with another object such as a sphere. If we model our objects in a Cartesian space we will be able to determine intersections quite easily.

A sphere will have an position and a radius. The position is represented by a vector and the radius by a number. We create a struct with two properties and default values.

```elixir
defmodule Sphere do

  defstruct pos: {0, 0, 0}, radius: 2

end
```

### Intersection

The tricky part is of course to determine if a ray will intersect a sphere but this is actually easily determined if we remember our linear algebra.

-&gt; Fig 2

In Fig.~\ref{fig:intersection} we see a ray intersecting a circle. We want to find the intersection points $\vec{i\_1}$ and $\vec{i\_1}$. We can do this by first calculate the length $a$ and this is done by taking the dot product of $\vec{k}$ and $\vec{l}$. The dot product will project the vector $\vec{k}$ on $\vec{l}$ thus giving us the length $a$. The vector $\vec{k}$ is of course easily calculated since we know the origin of the ray $\vec{o}$ and the centre of the circle $\vec{c}$.

Note that we here talk about the circle while our real model would contain a sphere - this is fine, the operations are the same.

The length of the vector $\vec{k}$ is of course $\\|\vec{k}\\|$ and if we know this we can calculate $h$ using Pythagoras' theorem. Since we know that the radius of the sphere is $r$ we can again rely on Pythagoras and calculate $t^2$.

$$ t^2 = r^2 - h^2 $$

If it turns out that $t^2$ is a negative value, it means that the ray does not intersect the sphere. This is our criteria for answering if we intersect the object or not. If $t^2$ is positive we calculate $t$ and then of course obtain two alternatives $t$ and $-t$.  We now calculate two distances $d\_1 = a - t$ and $d\_2 = a + t$.  This is the distance to the points of intersections from the origin of the ray $\vec{o}$.  If either value is negative it means that the intersection point is behind us; if only one value is negative we are actually inside the sphere. If both values are positive we return the smallest value since this is the surface that we will actually see.

### Object Protocol

Implement the function `intersect/2` that checks if ray intersects a sphere; return `:no` if it does not and `{:ok, d}`, where `d` is the closest distance, if it does. We implement the function in the `Sphere` module but use the `defimpl` construct to declare this as the `Object` protocol implementation.

```elixir
defimpl Object do

  def intersect(sphere = %Sphere{}, ray = %Ray{}) do
    ...
  end

end
```

 The function `intersect/2` is now reached using the call `Object.intersect/2` since it is defined as an `Object` protocol.

## The Camera

We have now done half of the job, you will soon create your first image but we first need to represent the camera. It turns out that we have a lot of options when defining what the camera looks like; we of course need to define where in the room it is and where it is pointing but also what kind of lens it has. This is probably the most complicated part of the implementation but we will give it a try.

### The Name of the Game

In the end we would like to have a representation of a camera that will allow us to ask for a ray that starts in the focal point \(or origin\) of the camera and runs through a given $\langle x, y\rangle$ coordinate of the canvas. If we know that the canvas is of size $800 \times 600$ then we can ask for the ray that runs through $\langle 230, 170\rangle$ and be given a a ray. This ray will then be compared to all the objects in the world and the closest intersection point will determine the color of the $\langle 230, 170\rangle$ pixel.

When you think about representation, then always think about what you're going to use the object for. This will allow you to represent the object in a way that makes the operations easier to perform. Also think about how you would like to talk about the object, the easiest way to describe an object might not be the best way to represent it.

### Properties of a Camera

If you have not used a large camera you might not have thought about how different lenses changes the picture but think about the difference between a "fish-eye'' and telephoto lens. The difference has to to with the **focal length**, the length from the lens to the focal point; the important factor is the ratio between the width of the **film** and the focal length. When using a 35mm film a focal length of 50mm gave a "normal'' lens i.e. a lens that gave images that looked normal.

It is thus important that we can describe a **canvas**: its size, orientation and position in relation to the **origin** of the camera. In Fig.~\ref{fig:camera} we see the elements that we need to represent: the origin described by a vector $\vec{o}$, a vector $\vec{f}$ that give us the direction and distance to the centre of the canvas and two vectors that give us the vertical, $\vec{v}$, and horizontal, $\vec{h}$, direction of the canvas. 

We might take for granted that the plane of the canvas is orthogonal to the direction; this is not strictly necessary but if it is not, we will have very strange projections of the image \(a technique that is actually used and if you want to know more you can search for the "Scheimpflug principle''\).

Note that the vertical and horizontal orientation are represented as two vectors. This will allow us to create a ray from the origin through any coordinate of the canvas. 

-&gt; Fig camera

A camera with a normal lens, positioned at $\langle 0,0,0\rangle$ and pointing straight into the picture, could be described as follows:

* position: $\langle 0,0,0\rangle$
* direction: $\langle 0,0,1200\rangle$
* horizontal: $\langle 960,0,0\rangle$
* vertical: $\langle 0,540,0\rangle$

This would give us a canvas of size $1920 \times 1080$ at a distance from the origin of $1600$ \(which will approximately give us a "normal'' lens\).

To minimise the computation needed when calculating the rays we could represent the camera by a position and a vector to the upper left corner of the canvas $\vec{c}$. If we then have two vectors that represent the distance between pixels moving to the right $\vec{r}$ and moving down $\vec{d}$, we can easily calculate the normalised vector to any pixel in the canvas.

$$pixel\(x,y\) = \|\vec{c} + x\*\vec{r} + y\*\vec{d}\|$$

We therefore represent the camera by position, direction to the upper left corner and the two vectors that describes the distance to the first pixel to the right and the first pixel down. Why the upper corner, why move down, why not lower left corner? Turns out that when we talk about images we often count the rows going down so this will makes things easier. We also keep the size of the canvas so we know which rays that we should produce.

Open up a module `Camera` and defined the following struct.

```elixir
defmodule Camera do

  defstruct pos: nil, corner: nil, right: nil, down: nil, size: nil
  
end
```

It will also be very handy to have a function that returns a default camera so that we don't have to think about the different parameters. This default camera can be positions at $\langle 0,0,0\rangle$ and point straight forward \(in the z direction\). We can give it a parameter that is the size of the image that we want to generate; you will have to calculate the rest of the parameters.

```elixir
def normal(size) do
  {width, height} = size
  d = width * 1.2
  h = width / 2
  v = height / 2
  corner = ...
    :
    :
  %Camera{pos: {0, 0, 0}, corner: corner, ..., ..., size: size)
end
```

Given a camera we now need to calculate a ray that passes through a given coordinate or pixel. This should be straight forward given that we created our representation with this in mind.

```elixir
def ray(camera, x, y) do
  origin = ...   # the origin of the ray
  x_pos = ...    # a vector from the corner to the x column
  y_pos = ...    # a vector from the corner to the y row
  pixle = ...    # a vector from origin to the pixle
  dir = ...      # the normalized vector
  %Ray{pos: origin, dir: dir}
end
```

### The World

The world is simply a list of objects. We will extend the world to also hold other things but for our first test this will be sufficient.

```elixir
defmodule World do

  defstruct objects: []

end
```

That completes all the modules that we need to represent the world, high time to generate some images.

