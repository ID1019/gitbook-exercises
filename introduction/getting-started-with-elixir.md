# Getting Started with Elixir

## Getting started

We assume that you have Elixir up and running and an editor to write your first Elixir programs. You don't need a full IDE, rather the less you have to think about the better.

In this tutorial we will write input to the Elixir shell as follow:

```elixir
> x + 2
```

You will actually see a number `iex(1)>`, but we will simply show a single `>`.

## Simple Arithmetic

Open up a Elixir shell and try some simple arithmetic. Try these examples:

```elixir
> 6 + 2

> 6 / 2

> div(7,2)

> rem(7,2)

> rem(-1,5)
```

Some simple comparisons:

```elixir
> 3 == 3

> 3 != 4

> 4 < 7
```

Try this:

```elixir
> h()
```

## A First Program

Open up a file `test.ex` and create a module called Test. In this module we define a function called `double/1` that takes one argument and returns the double of that argument.

```elixir
defmodule Test do

  # Compute the double of a number.
  def double(n) do
    ...
  end

end
```

In a regular shell you can now compile this program using the stand alone compiler:

```bash
$ elixirc test.ex
```

You can also compile and run the program from within the Elixir shell:

```elixir
> c("test.ex")

> Test.double(4)
```

Now in the same module define the following functions:

* a function that converts from Fahrenheit to Celsius \(the function is as follows: $$C = (F-32)/1.8$$\)
* a function that calculates the area of a rectangle give the length of the two sides
* a function that calculates the area of a square, using the previous function
* a function that calculates the area of a circle given the radius

Sooner or later you will have to think about which programming environment that you are going to use. We will not write very large programs in the course, but you will need to be able to quickly re-compile programs and shift between the editor and the Elixir shell. There are many environments to choose from and you are of course free to use whichever you find best.



### 



