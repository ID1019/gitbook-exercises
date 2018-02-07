# Introduction

In this assignment you should implement the behaviour of five philosophers that are sitting at a round dining table. The [problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem) is to allow each philosopher to get something to eat, a task that does not sound to be very difficult.

The situation is that the five philosophers are sitting at the dinner table with a bowl of noodles in front of them, and a chopstick between each of them. We thus have five philosophers and only five chopsticks, this is the problem.

When a philosopher decides to eat \(they sit and think most of the time\), she will pick up the two chopsticks next to her and eat from the bowl of noodles. When she is done, she will simply return the chopsticks to their places. If the philosophers do not eat that often things will probably work out just fine, the problem occurs when several philosophers decide to eat at the same time.

You should complete a simple program that implements the behaviour of the philosophers and run some experiments to see when things go wrong. You should also provide a solution to the problem that at least allow some of the philosophers to get some food.

## A Chopstick

The location of a chopstick is represented by a process. The state of the process is either:

* **available**: if a chopstick is present
* **gone**: if the chopstick is taken

A location will start in the state available, and then wait for a _request message_. When the location process receives this message it should return a _granted_ message and move to the state _gone_. In this state the location process will accept a return message and can then return to the available state. Messages that are not currently handled remain in the message queue.

<img src="/assets/Screen Shot 2018-02-07 at 23.11.30.png" height="50" />

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

## Dinner at the Table

If you have the two modules working we can seat the philosophers around the table. We first create the locations and then start the philosophers. In a module called `Dinner`, define the following function:

```Elixir
def start(), do: spawn(fn -> init() end)

def init() do
  c1 = Chopstick.start()    
  c2 = Chopstick.start()
  c3 = Chopstick.start()
  c4 = Chopstick.start()
  c5 = Chopstick.start()
  ctrl = self()
  Philosopher.start(n, 5, c1, c2, "Arendt", ctrl, seed + 1)
  Philosopher.start(n, 5, c2, c3, "Hypatia", ctrl, seed + 2)
  Philosopher.start(n, 5, c3, c4, "Simone", ctrl, seed + 3)
  Philosopher.start(n, 5, c4, c5, "Elisabeth", ctrl, seed + 4)
  Philosopher.start(n, 5, c1, c5, "Ayn", ctrl, seed + 5)
  wait(5, [c1, c2, c3, c4, c5])
end
```

We're starting all processes under a controlling process that will keep track of all the philosophers and also make sure that the chopstick processes are terminated when we're done.

```Elixir
def wait(0, chopsticks) do
  Enum.each(chopsticks, fn(c) -> Chopstick.quit(c) end)
end

def wait(n, chopsticks) do
  receive do
    :done ->
      wait(n - 1, chopsticks)
    :abort ->
      Process.exit(self(), :kill)
  end
end
```

If things go wrong and a process terminates with an error it will kill all linked processes. If things are stuck in a deadlock we can send an `:abort` message to the controller process that then will exit with an error and kill all other processes.

Now it's time to see if the philosophers will be able to dream and eat.

## Experiments

> Experiment with the dinner, will the philosophers always be able to eat? What happens if you decrease the time it dreams? What happens if you introduce an artificial delay between the receiving of the first chopstick and requesting the second?

## Break the Deadlock

To break out of a potential deadlock situation we can change the request function in the chopstick module. Let's pass a second argument to the function that specifies how many millisecond we are willing to wait for a chopstick.

```Elixir
def request(stick, timeout) do
  send stick, ...
  receive do
    ... -> 
      :ok
  after ... -> 
    :no
  end
end
```

Change your implementation of the philosophers to use the new interface. At this time it is worth understanding a bit about the random module. It will as you have figured out generate a random number each time we can `:rand.uniform/1` but, the sequence is  the same each time we create a new process. A process will use the same sequence every time it is created. This is very nice when we debug programs since we then eliminate one source of indeterminism, but not as fun when we want processes to behave different each time we run it. Do some reading and provide each philosopher with a unique seed value, if you do it right you can have different execution patterns at will.

> So you have broken the dead-lock, or so you think, but what is actually happening? What happens when a philosopher gives up? You have to do some thinking but the solution is quite simple once you trace what is happening.

### Asynchronous Request

The solution that you have now is quite boring in that a philosopher will first request the left chopstick and only when this is delivered will it try to grab the right chopstick. How about sending a request to both chopsticks first and then wait for the replies? Change the request function and then provide a _granted_ function that does the waiting.

> If a philosopher gives up, how do we keep track of which chopsticks that was actually obtained? Is this a tricky problem or a non-problem?

### A Waiter

> Can you provide a better strategy for the philosophers so that they can eat and dream without ending up in a deadlock? What happens if you provide a waiter that controls how many philosophers that can eat at any given time. How would this help the situation? How many philosophers can try to eat without ending up in a deadlock? How smart does the waiter need to be?

### Avoid the deadlock

> Is there a small change in the system that will avoid ever landing in a dead-lock situation? Can you guarantee that all philosophers will eventually get to eat? Is the system fair?

## Benchmark

Run some benchmarks and try to figure out how long time it takes for a set of philosophers to eat a given number of times. Use the algorithms that do not risk ending up in deadlocks and try to be as aggressive as possible.

> Can you work with a increasing "back-off" time so that a philosopher will wait for a while before trying to grab the chopsticks if it has failed once? Can the system adapt itself so that it runs smoothly without too many failed attempts? Is there a trade-off between being aggressive and over-all throughput?



