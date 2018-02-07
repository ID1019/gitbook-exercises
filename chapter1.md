# Introduction

In this assignment you should implement the behaviour of five philosophers that are sitting at a round dining table. The problem is to allow each philosopher to get something to eat, a task that does not sound to be very difficult.

The situation is that the five philosophers are sitting at the dinner table with a bowl of noodles in front of them, and a chopstick between each of them. We thus have five philosophers and only five chopsticks, this is the problem.

When a philosopher decides to eat \(they sit and think most of the time\), she will pick up the two chopsticks next to her and eat from the bowl of noodles. When she is done, she will simply return the chopsticks to their places. If the philosophers do not eat that often things will probably work out just fine, the problem occurs when several philosophers decide to eat at the same time.

You should complete a simple program that implements the behaviour of the philosophers and run some experiments to see when things go wrong. You should also provide a solution to the problem that at least allow some of the philosophers to get some food.

## A Chopstick

The location of a chopstick is represented by a process. The state of the process is either:

* **available**: if a chopstick is present or
* **gone**: if the chopstick is taken

A location will start in the state available, and then wait for a _request message_. When the location process receives this message it should return a _granted_ message and move to the state _gone_. In this state the location process will accept a _return message _and can then return to the available state. Messages that are not currently handled remain in the message queue.

Implement this process in a module `Chopstick` with a function `start/0` that spawns the process and returns the process id. When you spawn the process use `spawn_link/1` to make sure that the chopstick process dies if the mother process dies \(and vice verse\). This is some skeleton code to give you the structure of the implementation.

```elixir
def start do
  stick = spawn_link(fn -> ... end)
end

def available() do
  receive do
    ... -> ...
    :quit -> :ok
  end
end

def gone() do
  receive do
    ... -> ...
    :quit -> :ok
  end
end
```



