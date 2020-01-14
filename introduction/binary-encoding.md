# Binary encoding

You should now be up and running to take on some new challenges. When you try these challenges, first try to express your algorithm using recursion. Think about the simplest case and have this as your base case. Then formulate a rule that will take you from a more complex form one step closer to the base case.

### Integer to binary

Implement a function that takes an integer and return its binary representation coded as a list of ones and zeroes. The binary form of 5 is for example `[1,0,1]`.  Converting should be trivial so the base case should be simple. In the recursive case we can calculate the binary representation of `div(n, 2)` and the append it to either a 1 or 0 depending on if the number is even or odd  \(try using `rem/2`\).

```text
def to_binary(0) do ... end

def to_binary(n) do
  append(..., ...)
end
```

This could be written in a better way by using an accumulator. The accumulator will hold the binary sequence that we have determined so far. We start with a empty list, and add binary digits as we go.

```text
def to_better(n) do to_better(n, []) end

def to_better(0, b) do b end

def to_better(n, b) do
  to_better(div(n, 2), [rem(n, 2) | b])
end
```

Why is this better? Try the following \(`:timer` is a Erlang module anb `tc/1` will apply the function anr report the time in microseconds\) with larger and larger numbers, what is happening?

```elixir
>:timer.tc(fn() -> Binary.to_binary(99999999999999999999999999999) end)
```

