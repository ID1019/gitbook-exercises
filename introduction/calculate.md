# Calculate

Let's implement a small calculator. The function `eval/1` should take a representation of a arithmetic expression and return the result. The question is of course what expressions we allow and how these are represented. We will keep it simple and only allow addition, subtraction and multiplication over integers. 

We could of course represent integers as regular integers but let us complicate things.  An integer n is represented by the tuple {:int, n} . If we know this we can start our implementation with the following definition:

```elixir
def eval({:int, n}) do  ... end
```

Try it \(assuming te moduel is called Calc\): 

```elixir
> Calc.eval({:int, 5}) 
5 
```

Ok, so now let's assume that we represent addition with a tuple {:add, a, b} where a and b are arithmetic expressions \(for now the only thing we have a simple integers or addition\).  Extend your definition of eval/1 so that it also can handle addition.

```elixir
def eval({:int, n}) do  ... end
def eval({:add, a, b}) do 
    ...  +  ...
end
```

> Does it work?

```elixir
> Calc.eval({:add, {:int, 4}, {:int, 5}}) 
9

> Calc.eval({:add, {:add, {:int, 1}, {:int 3}}, {:int, 5}})
9
```

Extend the function so that it will also handle subtraction and multiplication.

Now for something more complicated - assume that we also have variabels in our expressions and that these are represented as `{:var, name}` where name is an atom.  Also assume that we have a list of bindings that will give us the value of variables. The bindings look like  this:  `{:bind, name, value}`. 

First implementa function that find the value of a variable given a list of bindings. We can assume that the variable is found in the list of bindings.

```elixir
def lookup(var, [{:bind, var, value} | _] ) do ... end
def lookup(var, [_ | rest]) do ... end
```

Now, extend the calculater so that the function eval/2 takes an arithmetic expression and s lits of bindings and evaluates the result. Most  things shoudl be as before but when we stumble on a variable we shoudl do something:

```elixir
def eval({:var, name}, bindings) do  ... end
```

Does it work?

```elixir
> Calc.eval({:var, :x}, [{:bind, :x, 5}])
5

> Calc.eval({:add, {:var, :x}, {:var, :y}}, [{:bind, :x, 5}, {:bind, :y, 2}])
7
```

