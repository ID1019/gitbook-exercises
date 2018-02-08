## Simple arithmetic functions

Implement the following arithmetic functions and save them in a file `recursion.ex`. Start with simple recursive implementations that are not necessarily tail-recursive. In order not to create any conflicts with built-in function, you will have to call your functions something different i.e. `prod` instead of `product` etc.

When you implement them write a small documentation. For more information regarding documenting Elixir check the the following [documentation](https://hexdocs.pm/elixir/writing-documentation.html).

```Elixir
defmodule Recursion do

  @doc """
  Compute the product between of n and m.

  product of n and m :
    if n is 0
      then ...
    otherwise
      the result ...
  """
  def prod(..., ...) do
    case ... do
      ... -> ...
      ... -> ...
    end
  end

end
```

* `prod`: complete the above implementation of product
* `prod`: change the above definition so that it also works for negative numbers
* `power`: implement the power function for non-negative exponents
* `qpower`: use the two functions _div_ and _rem_ to implement a faster version of the power function by dividing the exponent by two in each recursive step \(what must be done if the exponent is odd?\)

## Binary

Implement a function `binary/1` that takes an integer and produces its binary encoding, represented ad a list of ones and zeros. Your first solution should look something like this:

```Elixir
def binary(0), do: []

def binary(...), do: binary(...) ++ [...]
```

What is the deficiency of this implementation. What is the runtime complexity? Hint: check [this page](https://hexdocs.pm/elixir/Kernel.html#++/2) about Elixir kernel. 

Change your implementation so that it has a $$O(n)$$ complexity. The solution will look something like this:

```Elixir
def binary2(i), do: binary2(i, ...)

def binary2(0, sofar), do: Enum.reverse(sofar)

def binary2(..., ...), do: binary2(..., ...)
```

We're using the [library function](https://hexdocs.pm/elixir/Enum.html#reverse/1) `Enum.reverse/1` to reverse the accumulated value.

## Fibonacci

Try some more complex functions, for example the Fibonacci function:

{% math %}

fib\(n\)=

        \begin{cases}

        0 & \text{if } n \text{ is }0\\

        1 & \text{if } n \text{ is }1\\

        fib\(n-1\)+fib\(n-2\) & \text{otherwise}

        \end{cases}  
{% endmath %}

