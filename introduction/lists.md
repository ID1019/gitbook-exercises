# Lists

You need to learn how to work with linked lists. It's the number one data structure that you wan to represent a sequence of data. First try the following in  a terminal, you need to understand how lists are constructed and decomposed uing pattern matching.

```elixir
> [1 | []]

> [1 | [2 | []]]

> [1, 2]

> [1, 2] = [1 | [2 | []]]

> [x, y, z] = [1, 2, 3]

> [head | tail] = [1, 2, 3]

> [_, {x, y} | _] = [{:a, 1}, {:b, 2}, {:c, 3}, {:d, 4}]

> [z] = [1, 2, 3]
```



Open up new module and implement the following list processing functions \(again we will have to call them something not that obvious in order not to create a conflict with the built-in functions\):

### Take and drop 

Implement a function tak/1  \(a function called tak taking one argument\)  that returns the first element of a list. 

```elixir
> tak([1,2,3,4])
1
```

Implement a function drp/1  that returns a list where the first element has been removed.

```elixir
> drp([1,2,3,4])
[2,3,4]
```

What should the above functions return if you call them with a emty list `[ ]` ?  If you have not done anything a call will return an error. This i perfectly fine but let's change the code so it either returns `{:ok, res}` or `:no` if we provide anything but a list with at least one element.

### length and sum 

These are some simple functions that you should implement. They will all use recursion so first try to formulate in words what the definition should look like, then implement it.

* `len(l)`: return the number of elements in the list _l_
* `sum(l)`: return the sum of all elements in the list _l_, assume that

  all elements are integers

some more thinking

These functions take some more thinking. You should return a list as a result of evaluating the function.

* `duplicate(l)`: return a list where all elements are duplicated
* `add(x, l)`: add the element _x_ to the list if it is not in the list
* `remove(x, l)`: remove all occurrences of _x_ in _l_
* `unique(l)`: return a list of unique elements in the list _l_ 

### append two lists

Implement a function that appends two list into one list. 

```elixir
> append([], [4,5])
[4,5]

> append([3],[4,5])
[3,4,5]

> append([1,2,3],[4,5])
[1,2,3,4,5]
```

### reverse a list 

One interesting problem to look at is how to reverse a list. The _naive_ way to do it is quite straight forward. We do it recursively by removing the first element of the list, reversing the rest and then appending the reversed list to a list containing only the first element.

```elixir
def nreverse([]) do [] end

def nreverse([h | t]) do
  r = nreverse(t)
  append(r, [h])
end
```

A smarter way to do it, is to use an _accumulator_ and add the first element to this accumulator. When we have added all elements in the lists the accumulated list is the reversed list.

```elixir
def reverse(l) do
  reverse(l, [])
end

def reverse([], r) do r end
def reverse([h | t], r) do
  reverse(t, [h | r])
end
```

Ok, so what is so smart by doing this? This is your assignment, you should do some performance analysis of these two functions and describe what is happening. To have some data lead you in the right direction and to back up your findings you should start by doing some performance measurements.

We have here used some library functions and higher order programming that you might not have seen so far but don't worry, you will get use to it.

```elixir
def bench() do
  ls = [16, 32, 64, 128, 256, 512]
  n = 100
  # bench is a closure: a function with an environment.
  bench = fn(l) ->
    seq = Enum.to_list(1..l)
    tn = time(n, fn -> nreverse(seq) end)
    tr = time(n, fn -> reverse(seq) end)
    :io.format("length: ~10w  nrev: ~8w us    rev: ~8w us~n", [l, tn, tr])
  end

  # We use the library function Enum.each that will call
  # bench(l) for each element l in ls
  Enum.each(ls, bench)
end

# Time the execution time of the a function.
def time(n, fun) do
  start = System.monotonic_time(:milliseconds)
  loop(n, fun)
  stop = System.monotonic_time(:milliseconds)
  stop - start
end

# Apply the function n times.
def loop(n, fun) do
  if n == 0 do
    :ok
  else
    fun.()
    loop(n - 1, fun)
  end
end
```

