# Functional Programming

## A Heap

### A New Heap

A **heap** is a tree structure where the largest element is in the root of the tree and where the left and right branch are heaps. Define a data structure that is suitable to represent a heap and implement a function `new/0` that returns a heap. Assume that we only should handle integers.

```elixir
@spec new() :: heap()
```

### Insert

Implement the function `add/2` that adds an integer to a heap.

```elixir
@spec add(heap(), integer()) :: heap()
```

To keep the heap balanced you should switch the left and right branches that is, when you add an element to a branch you add it to the right branch but make the result the left branch of the heap.

### Pop

Implement the function `pop/1` that removes the highest element in an heap and returns either `:fail`, if the heap is empty or `{:ok, integer(), heap()}`

```elixir
@spec pop(heap()) :fail | {:ok, integer(), heap()}
```

### Swap

Implement the function `swap/2` that takes a heap and a number and returns `{:ok, integer(), heap()}` where the number is the highest number and the heap the remaining heap. The function should have the same meaning as first `add/2` a number to a heap and then `pop/1` the highest but we should do this in one function, not by calling the two functions.

```elixir
@spec swap(heap(), integer()) {:ok, integer(), heap()}
```

### A General Heap

The heap we now have will have the largest element in the root and be limited to integers \(or what is comparable with `<`\). Implement a function `add/3` that takes a heap, an element and a function that can be used to compare two elements. The function `add/3` should as before add an element to a heap but now used the provided function when doing the comparison.

```elixir
@type cmp() :: (any(), any() > bool())
@spec add(heap(), any(), cmp()) :: heap()
```

Specify a type `cheap()` that holds a function for comparison and a heap. Implement a function `new/1` that takes a function and returns a structure of the type `cheap()`.

```elixir
@spec new(cmp()) :: cheap()
```

Implement a function `add/2` that takes a structure of type `cheap()`, that calls `add/3` with the correct arguments and returns a structure of the same form.

```elixir
@spec add(cheap(), any()) :: cheap()
```

## Mirror a Tree

If we have the below definition of a function that mirrors a tree, what is the asymptotic time complexity of the function?

```elixir
def mirror(nil) do nil end

def mirror({:node, left, right}) do
  {:node, mirror(right), mirror(left)}
end
```

## Complexity of a Queue

Assume that we represent a queue with the help of two lists and have the below implementation of `enqueue/2` and `dequeue/1`. What is the amortised time complexity for **adding and then removing an element from a queue**?

```elixir
def enqueue({:queue, head, tail}, elem) do
  {:queue, head, [elem|tail]}
end

def dequeue({:queue, [], []}) do :fail end

def dequeue({:queue, [elem|head], tail}) do
  {:ok, elem, {:queue, head, tail}
end

def dequeue({:queue, [], tail}) do
  dequeue({:queue, reverse(tail), []})
end
```

