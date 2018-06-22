# Concurrency

## The Middle Number

You should implement a process that holds a state consisting of a set of numbers. The set is initially empty but the process should then accept the following messages:

* `{:add, integer()}`: the number should be added to the set
* `{:get, pid()}`: the process should reply with either `:fai`, if the set is empty, or `{:ok, integer()}` where the integer is the middle number in the set \(if an even number any one of the two middle numbers\) that is also removed from the set.

To complicate matters both operations should be done in _O\(log\(n\)\)_ time, where _n_ is the number of elements in the set. You are not allowed to use any libraries to store the set of elements but should use the implementation of a heap in the previous questions. The process will apart from keeping track of the middle element have two heaps, one for smaller elements and one for larger.

### State Diagram

Start by describing a state diagram. The process should have three states: `empty` when the set is empty, `left` when we have a middle element and possibly one more element to the left \(that are less\) then we have to the right and, `right` when we have a middle element and possibly one more elements to the right than we have to the left.

### No Elements

Implement how we start the process and its behaviour in its empty state. Assume that we have the following functions dened in a module `Heap`.

```elixir
@spec new(cmp()) :: cheap()
```

### Maybe More to the Left

In its left state the process has a middle element, a number of elements that are less \(to the left\) and as many or one less that are greater \(to the right\). Implement the behaviour of the process in its left state. Assume that we have the following functions dened in a module `Heap`.

```elixir
@spec add(cheap(), any()) :: cheap()
@spec pop(cheap()) :: :fail | {:ok, any(), cheap()}
@spec swap(cheap(), any()) :: {:ok, any(), cheap()}
```

You don't have to implement the right state since this state will be identical to the left state apart from doing the opposite.

