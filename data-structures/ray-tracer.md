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

## Vectors and rays

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

