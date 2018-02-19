## Introduction

You of course know how to take the derivative of a function, this is something you probably learned already in high school. There are some simple rules that one applies and in five minutes one can find the derivative of even complicated functions.

Our task in this tutorial is to work with functions as symbolic expressions and compute the derivative of functions. Part of the problem is to understand how we can represent functions using the data structures that we have in Elixir. Once we know how to do this the derivation will be quite simple.

## Representing Functions

You might wonder what the problem is; after all we represent a function in Elixir using the syntax:

``` elixir
fn(x) -> x * 2 end
```

This is only partly true, what we do is express what the function should look like, how it is actually represented is hidden to us. If we write:

``` elixir
f = fn(x) -> x * 2 end
```

then `f` is a function of one argument that we can use, but if we only have access to `f` we can not determine that it is a function of one argument nor that the the body of the function is a multiplication of the argument and a constant.

In order to differentiate the function `f`, with respect to `x`, we need to know that the body of the expression is `x*2`. We need to be able to examine the expressions `x*2`, and determine that it is a product of the variable `x` and `2`, only then can we determine that the derivative is 2. So, just because we have *functions* in Elixir, does not mean that we necessarily should use them to represent our “functions”.

## An Expression

Let’s start by finding a representation for constants and variables. We can limit ourselves to the domain of reals and a real number could of course be represented by an Elixir number. This is of course trivial but remember to separate the real number 2.34 from the representation in Elixir by `2.34`. A variable could be represented as an atom so the variable $$x$$ is represented by the Elixir atom `:x`.

This simple mapping of elements in our domain to Elixir data structures is tempting to use but it has some disadvantages, we can not use pattern matching to determine if an element is a number or a variable. In our program we would have to use the built-in recognizers to separate the two cases; we would use code as the following:

``` elixir
def derivative(n) when is_number(n) do: ...

def derivative(n) when is_atom(n) do: ...
```

We would also have to think twice when we want to include constants such as $$\pi$$, could we represent it using the atom `:pi`? Moreover, when we derivatives, is it important to separate the constant $$\pi$$ from the constant 2.34?

A better approach (all though it will turn our expressions into huge data structures) is to be more explicit in our choice of representation. What if we choose to represent all constants using the tuple `{:const, c}` where `c` could be either an atom (`:pi`) or a number (`2.34`). Variables could, in the same way, be represented by tuples `{:var, v}` where `v` is a atom that identifies the variable.

If constants and variables are our `literals`, we have the following definition:

``` elixir
@type literal() :: {:const, number()} 
                | {:const, atom()} 
                | {:var, atom()}
```
