# Recursion

Assume that all we have is addition and subtraction but need to define multiplications. How would you do? You will have to use recursion and you solve it by first describing the multiplication functions by words.

_The product of_ $$m$$ _and_ $$n$$ _is: 0 if_ $$m$$ _is equal to 0, otherwise the  
product is_ $$n$$ _plus the product of_ $$m-1$$ _and_ $$n$$_._

Once you have written down the definition, the coding is simple.

## Simple arithmetic functions

Implement the following arithmetic functions and save them in a file `recursion.ex`.  In order not to create any conflicts with built-in function, you will have to call your functions something different i.e. `prod` instead of `product` etc.

When you implement them write a small documentation. For more information regarding documenting Elixir check the the following [documentation](https://hexdocs.pm/elixir/writing-documentation.html).

```elixir
defmodule Recursion do

  @doc """
  Compute the product between of n and m.

  product of n and m :
    if n is 0
      then ...
    otherwise
      the result ...
  """
  def prod(m, n) do
    case m do
      0 -> ...
      ... -> ...
    end
  end

end
```

* `prod`: complete the above implementation of product
* `prod`: change the above definition so that it also works for negative numbers
* `power`: implement the power function for non-negative exponents
* `qpower`: use the two functions _div_ and _rem_ to implement a faster version of the power function by dividing the exponent by two in each recursive step \(what must be done if the exponent is odd?\)

### alternative syntax

There are several ways of expressing the same thing in Elixir . The function product could be writen using the _cond-expression_ below. We would then use arithmetic comparisions that would evaluate to `true` or `false`.  

```elixir
def product(m, n) do
  cond do
    m == 0 ->
      ...
    true ->
      ...
  end
end
```

We could also divide the alternatives into different clauses as in the code below.

```elixir
def product(0, n) do .. end
def product(m, n) do 
    :
end 
```

This should be read: if we call product, and the first argument matches _0_, then the result is .... If we can not use the first clause then we try the second clause.

Sometimes the code becomes easier to understand, especially if we have many conditions that should be tested. Remember though that the clauses of a function need to be after each other. You can not spread the clauses around in a program.

## Fibonacci

Try some more complex functions, for example the Fibonacci function:

$$fib(n) = \left\{   \begin{array}{l l}     0 & \quad \text{if $n$ is 0}\\     1 & \quad \text{if $n$ is 1}\\     fib(n-1)+fib(n-2) & \quad \text{otherwise}\\   \end{array} \right.$$ 

The Fibonacci sequence is the sequence $$0,1,1,2,3,5,8,13,21,\ldots$$. The two first numbers are 0 and 1 and the following numbers are calculated by adding the two previous number. To calculate the Fibonacci value for n , all you have to do is find the Fibonacci number for n-1 and n-2 and then add them together.

Write simple Fibonacci function fib/1 and do some performance measurements. 

```elixir
defmodule Fib do

def fib(0) do ...  end
def fib(1) do ...  end
def fib(n) do ...  end

def bench() do
  ls = [8,10,12,14,16,18,20,22,24,26,28,30,32]
  n = 10

  run = fn(l) ->
    t = time(n, fn() -> fib(l) end)
    :io.format("n: ~4w  fib(n) calculated in: ~8w us~n", [l, t])
  end

  Enum.each(ls, run)
end

def time(n, call) do
   {t, _} = :timer.tc(fn -> loop(n, call) end)
   trunc(t/n)
end
   
def loop(0, _ ) do :ok end
def loop(n, call) do 
   call.()
   loop(n-1, call)
end   
```

Find an arithmetic expression that almost describes the computation time for $$fib(n)$$. Can you justify this arithmetic expression by looking at the definition of the function? How large Fibonacci number do you think you can compute if you start now and let your machine run until tomorrow? First make a guess, don't try to do the calculation in your head just make a wild guess, then try to estimate how long time that would take using your arithmetic function, would you be able to make it?

## Ackermann

You can also give the [Ackermann function](https://en.wikipedia.org/wiki/Ackermann_function) a try:

$$ackerman(m,n) = \left\{   \begin{array}{l l}     n+1 & \quad \text{if $m=0$}\\     ackerman(m-1,1) & \quad \text{if $m>0$ and $n=0$}\\     ackerman(m-1,ackerman(m,n-1)) & \quad \text{otherwise}\\   \end{array} \right.$$ 

This looks like an innocent little function but don't try too high numbers.

## 

