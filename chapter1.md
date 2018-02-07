# Philosophers and Concurrency

## Introduction

In this assignment you should implement the behaviour of five philosophers that are sitting at a round dining table. The problem is to allow each philosopher to get something to eat, a task that does not sound to be very difficult. 

The situation is that the five philosophers are sitting at the dinner table with a bowl of noodles in front of them, and a chopstick between each of them. We thus have five philosophers and only five chopsticks, this is the problem.

When a philosopher decides to eat \(they sit and think most of the time\), she will pick up the two chopsticks next to her and eat from the bowl of noodles. When she is done, she will simply return the chopsticks to their places. If the philosophers do not eat that often things will probably work out just fine, the problem occurs when several philosophers decide to eat at the same time.

You should complete a simple program that implements the behaviour of the philosophers and run some experiments to see when things go wrong. You should also provide a solution to the problem that at least allow some of the philosophers to get some food. 

