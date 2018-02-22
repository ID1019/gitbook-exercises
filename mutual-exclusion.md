## Getting started

In this assignment you will learn about the concept of *mutual exclusion* and how locks, semaphores and monitors can give us what we want. These concepts are not frequently used in Elixir programming but you should know about them and understand why they are not often explicitly used in Elixir.

The main idea with mutual exclusion is that we want to limit theconcurrency to at most one executing process in a *critical section*. A critical section could be a section in the program where we modify some data structure and we do not want any other process to see what we have done until we are completely done. Since we do not have any mutable data structures in Elixir the need for locks is limited but think about updating a set of files where you want to do all the changes before you let another process see what you have done or do their modifications.

## Let's Implement a Lock

We will start by implementing a *lock process* (or rather try to implement it). A lock is something that can only be held by one process, the process that {\em takes the lock} knows that it is the sole owner of the lock and can proceed to a critical section.

Our first attempt to implement a lock process is quite straight forward - we will implement the lock as a process that only holds one value and accepts two messages: **set** and **get**.

``` elixir
defmodule Cell do
  
  def new(), do: spawn_link(fn -> cell(:open) end)

  defp cell(state) do
    receive do
      {:get, from} ->
        send(from, {:ok, state})
        cell(state)

      {:set, value, from} ->
        send(from, :ok)
        cell(value)
    end
  end

end
```

To make it easier to use the lock we also provide two functions that hide the fact that we do asynchronous communication.

``` elixir
def get(cell) do
  send(cell, {:get, self()})
  receive do
    {:ok, value} -> value
  end
end

def set(cell, value) do
  send(cell, {:set, value, self()})
  receive do
    :ok -> :ok
  end
end
```

If we have created a cell we could use it to protect a critical operation using the codes that follows (not true so don't stop reading here).

``` elixir
def do_it(thing, lock) do
  case Cell.get(lock) do
    :taken ->
      do_it(thing, lock)
    :open ->
      Cell.set(lock, :taken)
      do_ya_critical_thing(thing)
      Cell.set(lock, :open)
  end
end
```

Perfect, case closed ... ehhh, there is something wrong - what happens if...? Before you continue think about what would happen if two processes called the `do_it/2` procedure with the same lock; do we guarantee that the two processes will never ever execute the critical section at the same time?

## The Atomic Swap

There are two solutions to the problem of the lock in the first section: *atomic swap* and *Peterson's algorithm*. Using atomic swap we implement a new message that will read and write to the cell in the same operation. This feature is often found in hardware and programs written in C or C++ can often make direct use of this. In our implementation we will implement it ourselves with a small extension to the lock. 

``` elixir
defp cell(state) do
  receive do
    {:swap, value, from} ->
      send(from, {:ok, state})
      cell(value)
    {:set, value, from} ->
      send(from, :ok)
      cell(value)
  end
end
```

Assuming we also provide a functional interface we could now use the lock as follows:

``` elixir
def do_it(thing, lock) do
  case Cell.swap(lock, :taken) do
    :taken ->
      do_it(thing, lock)
    :open ->
      do_ya_critical_thing(thing)
      Cell.set(lock, :open)
  end
end
```

In this version it does not matter if two processes calls the procedure at the same time; both of them will swap in the value `:taken` but only one of them would receive the `:open` value in return. The process that loses the race will have to retry to take the lock and will succeed once the holding process sets the lock to `:open`.
