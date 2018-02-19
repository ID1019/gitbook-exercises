## Introduction

This exercise is partly about breaking up a problem in layers of abstractions and implement each layer as a process or a set of processes. We will model our solution as communicating finite state machines and each machine is of course implemented as an Elixir process.

The problem that we will look at is how to create a communication abstraction that provides addressing and FIFO delivery over a lossy channel. The situation will look similar to that of TCP/IP but our problem statement and solution will be much simpler.

## A Transport Abstraction

Assume that we want to create a *transport service*. The service should provide the following functionality:

- **identity**: The service should provide addresses, we should be able to send and receive messages to other processes given an address.
- **flow control**: The service should take care of flow control so we should not be able to send more messages than the receiver can consume.
- **ordered delivery**: Packets should be delivered in order.
- **reliable delivery**: Packets should be delivered even if the underlying communication channel is lossy.

This is not that hard to achieve and the solution could easily be be described in a state diagram with 15 states and 123
transitions... The problem we would have is that, if we want to solve all these properties in one go, we would have so many things to keep track of that the solution would look like a bowl of spaghetti.

Instead of solving all problems at once we divide the solution into layers, each layer providing a service that brings us closer to the final solution. The layers that we will implement have similarities with the ISO communication layers or the layers in the IP stack. The layers that we will work with are:

- **link**: This layer is given, it will send a *frame* on a wire but does not know very much more. There are no guarantees that the frame will arrive nor that frames will arrive in order.
- **network**: This layer will introduce *network addresses* so we can have several nodes connected together. It uses the link layer straight off so we do not add ordered delivery nor guarantees of message delivery.
- **order**: This layer will add re-transmissions of missing messages and keep track of ordering of messages. 
- **flow**: This layer will solve the flow-control problem.

The TCP protocol is similar to our order and flow layers but also implements congestion avoidance, process addressing, stream to datagram segmentation etc. In this implementation we only provide reliability and flow control to make things a bit easier. We will also implement them as separate layers to show how simple each layer can be implemented.

If we draw a diagram over the complete system it will look like Fig. 1. Each layer will have one or more processes that implements the right abstractions. In its first incarnation each layer will be handled by a single process but when we extend the system we might want to have separate processes for different directions.
