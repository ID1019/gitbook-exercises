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

Pew $\(3+4\)$

