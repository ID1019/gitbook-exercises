# Introduction

In this assignment you should implement the behaviour of five philosophers that are sitting at a round dining table. The [problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem) is to allow each philosopher to get something to eat, a task that does not sound to be very difficult.

The situation is that the five philosophers are sitting at the dinner table with a bowl of noodles in front of them, and a chopstick between each of them. We thus have five philosophers and only five chopsticks, this is the problem.

When a philosopher decides to eat \(they sit and think most of the time\), she will pick up the two chopsticks next to her and eat from the bowl of noodles. When she is done, she will simply return the chopsticks to their places. If the philosophers do not eat that often things will probably work out just fine, the problem occurs when several philosophers decide to eat at the same time.

You should complete a simple program that implements the behaviour of the philosophers and run some experiments to see when things go wrong. You should also provide a solution to the problem that at least allow some of the philosophers to get some food.

---

## A Chopstick

The location of a chopstick is represented by a process. The state of the process is either:

* **available**: if a chopstick is present
* **gone**: if the chopstick is taken

A location will start in the state available, and then wait for a _request message_. When the location process receives this message it should return a _granted_ message and move to the state _gone_. In this state the location process will accept a \_return message \_and can then return to the available state. Messages that are not currently handled remain in the message queue.

Implement this process in a module `Chopstick` with a function `start/0` that spawns the process and returns the process id. When you spawn the process use `spawn_link/1` to make sure that the chopstick process dies if the mother process dies \(and vice verse\). This is some skeleton code to give you the structure of the implementation.

```Elixir
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

We should try to keep the internals of the location process as hidden as possible from the users of the module. We therefore provide a functional interface so that a user of the module does not need to know the structure of all messages.

```Elixir
def request(stick) do
  send stick, ...
  receive do
    ... -> :ok
  end
end
```

Provide similar functions for returning the stick and terminating the process. We will change things later so you will see that it is very nice to only allow the philosophers to use the functional interface.

---

## A Philosopher

A philosopher is either dreaming \(some call it thinking\), waiting for a chopstick or eating. In the dreaming state the philosopher does nothing until she decides that it is time to eat. She will then request her left chopstick and her right chopstick, if everything works she can start to eat. A philosopher will eat for while and then return the chopsticks.

You can implement the dreaming and eating time by using the library function `:timer.sleep/1` that will simply wait for a number of milliseconds before continuing. If you want to have some randomness you can use the library function `:rand.uniform/1`. This sequence will make a process sleep for a random time.

```Elixir
def sleep(t) do 
  :timer.sleep(:rand.uniform(t))
end
```

Implement the philosopher in a module called `Philosopher` and provide a function `start/5` that spawns a philosopher process \(use `spawn_link/1`\). The procedure should take the following arguments.

* **hunger**: the number of times the Philosopher should eat before it sends a `:done` message to the controller process.
* **right** and **left**: the process identifiers of the two chopsticks.
* **name**: a string that is the name of the philosopher, used for nice logging.
* **ctrl**: a controller process that should be informed when the philosopher is done.

Add some nice logging information to your process so that you can track what is happening. A philosopher could for example print a message when it receives a chopstick:

```Elixir
IO.puts("#{name} received a chopstick!")
```

Elixir also supports string interpolation; in the code fragment above the content of the variable `name` is interpolated with the rest of the string.

