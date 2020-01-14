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

## Recursive Definitions

Assume that all we have is addition and subtraction but need to define multiplications. How would you do? You will have to use recursion and you solve it by first describing the multiplication functions by words.

_The product of_ $$m$$ _and_ $$n$$ _is: 0 if_ $$m$$ _is equal to 0, otherwise the  
product is_ $$n$$ _plus the product of_ $$m-1$$ _and_ $$n$$_._

Once you have written down the definition, the coding is simple.

```elixir
def product(m, n) do
  if m == 0 do
    ...
  else
    ...
  end
end
```

There are alternative ways of writing this, we could use a _case_ expression or the _cond-do_ expression. These techniques are often preferred over _if-else_ in Elixir.

```elixir
def product_case(m, n) do
  case m do
    0 ->
      ...
    _ ->
      ...
  end
end

def product_cond(m, n) do
  cond do
    m == 0 ->
      ...
    true ->
      ...
  end
end
```

We could also have used the a style that sometimes is very handy. Here we break the definition up into two _clauses_ that are tried one after the other.

```elixir
def product_clauses(0, _) do 0 end
def product_clauses(m, n) do
  product_clauses(..., ...) + ...
end
```

This should be read: if we call product, and the first argument matches _0_, then the result is _0_. If we can not use the first clause then we try the second.

Sometimes the code becomes easier to understand, especially if we have many conditions that should be tested. Remember though that the clauses of a function need to be after each other. You can not spread the clauses around in a program.

Define a function, `exp/2`, that computes the exponentiation, $$x^2$$. Use only the addition and subtraction and the function `product/2`, that you defined.

```elixir
def exp(x, n) do
  case ... do
    ...
  end
end
```

Use the built-in arithmetic functions _rem_, _div_ and multiplication _\*_  to implement a much faster exponentiation using the following definition:

* $$x$$ raised to 1 is $$x$$
* $$x$$ raised to $$n$$, if $$n$$ is even, is $$x$$ raised to $$n/2$$ multiplied by itself
* $$x$$ raised to $$n$$, if $$n$$ is odd, is $$x$$ raised to $$(n-1)$$ multiplied by $$x$$

### 

```elixir
def to_binary(0) do ... end

def to_binary(n) do
  append(..., ...)
end
```

This could be written in a better way by using an accumulator. The accumulator will hold the binary sequence that we have determined so far. We start with a empty list, and add binary digits as we go.

```elixir
def to_better(n) do to_better(n, []) end

def to_better(0, b) do b end

def to_better(n, b) do
  to_better(div(n, 2), [rem(n, 2) | b])
end
```

Why is this better than the previous one? Can you notice the difference? Try the following:

```elixir
> Test.time(1000, fn -> Test.to_binary(123489879809809809887) end)
```

Now try the better version - any difference? Why?

### Binary to integer

Now try the revers and implement a function that takes a binary representation of a number and returns an integer. To solve this it is a lot easier to use an accumulator that holds the number of what we have seen so far.

```elixir
def to_integer(x) do to_integer(x, ...) end

def to_integer([], n) do ... end

def to_integer([x | r], n) do
  to_integer(..., ...)
end
```

### Fibonacci

The Fibonacci sequence is the sequence $$0,1,1,2,3,5,8,13,21,\ldots$$. The two first numbers are and and the following numbers are calculated by adding the two previous number. To calculate the Fibonacci value for , all you have to do is find the Fibonacci number for and and then add them together.

Write simple Fibonacci function fib/1 and do some performance measurements. Adapt the benchmark program above and run some tests.

```elixir
def bench_fib() do
  ls = [8,10,12,14,16,18,20,22,24,26,28,30,32]
  n = 10

  bench = fn(l) ->
    t = time(n, fn() -> fib(l) end)
    :io.format("n: ~4w  fib(n) calculated in: ~8w us~n", [l, t])
  end

  Enum.each(ls, bench)
end
```

Find an arithmetic expression that almost describes the computation time for $$fib(n)$$. Can you justify this arithmetic expression by looking at the definition of the function? How large Fibonacci number do you think you can compute if you start now and let your machine run until the seminar? First make a guess, don't try to do the calculation in your head just make a wild guess, then try to estimate how long time that would take using your arithmetic function, would you be able to make it?

Calculate a Fibonacci number as high as you possibly can.

