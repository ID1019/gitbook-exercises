# Network Transport Layer

## Introduction

This exercise is partly about breaking up a problem in layers of abstractions and implement each layer as a process or a set of processes. We will model our solution as communicating finite state machines and each machine is of course implemented as an Elixir process.

The problem that we will look at is how to create a communication abstraction that provides addressing and FIFO delivery over a lossy channel. The situation will look similar to that of TCP/IP but our problem statement and solution will be much simpler.

## A Transport Abstraction

Assume that we want to create a _transport service_. The service should provide the following functionality:

* **identity**: The service should provide addresses, we should be able to send and receive messages to other processes given an address.
* **flow control**: The service should take care of flow control so we should not be able to send more messages than the receiver can consume.
* **ordered delivery**: Packets should be delivered in order.
* **reliable delivery**: Packets should be delivered even if the underlying communication channel is lossy.

This is not that hard to achieve and the solution could easily be be described in a state diagram with 15 states and 123  
transitions... The problem we would have is that, if we want to solve all these properties in one go, we would have so many things to keep track of that the solution would look like a bowl of spaghetti.

Instead of solving all problems at once we divide the solution into layers, each layer providing a service that brings us closer to the final solution. The layers that we will implement have similarities with the ISO communication layers or the layers in the IP stack. The layers that we will work with are:

* **link**: This layer is given, it will send a _frame_ on a wire but does not know very much more. There are no guarantees that the frame will arrive nor that frames will arrive in order.
* **network**: This layer will introduce _network addresses_ so we can have several nodes connected together. It uses the link layer straight off so we do not add ordered delivery nor guarantees of message delivery.
* **order**: This layer will add re-transmissions of missing messages and keep track of ordering of messages. 
* **flow**: This layer will solve the flow-control problem.

The TCP protocol is similar to our order and flow layers but also implements congestion avoidance, process addressing, stream to datagram segmentation etc. In this implementation we only provide reliability and flow control to make things a bit easier. We will also implement them as separate layers to show how simple each layer can be implemented.

If we draw a diagram over the complete system it will look like Fig. 1. Each layer will have one or more processes that implements the right abstractions. In its first incarnation each layer will be handled by a single process but when we extend the system we might want to have separate processes for different directions.

> trasnport picture

We now start form the ground up and implement layer by layer. We should be able to test that a layer works before implementing the next layer.

## Link Layer

The link layer process is, as shown in Fig. 2 quite simple. We will first start the process and give it the process identifier of the master process. We will then send it a message that connect the process with a destination link layer process.

> link picture

Note the order here; we can not provide the link layer process with its connection point when we create the process. If we tried we would end up in a chicken or the egg question. We solve this by first creating both link processes and the send them a message with the process identifier of their peer.

The link layer should be able to handle the following messages:

* `{:send, msg}`: A message from the master \(the network layer process\), telling the link process to send the message, `msg`, to the destination process. The message is wrapped in a `Frame` and sent using the regular Elixir send primitive.
* `%Frame{data: msg}`: A frame from the peer link process. This message should be forwarded to the master process using the regular Elixir send primitive. The message is sent as is, `msg`, without being wrapped in any special data structure.

The Elixir implementation is given in Appendix A. Before we continue we write a small test program and connect two nodes together.

```elixir
def test_link() do
  sender = spawn(fn() -> sender() end)
  receiver = spawn(fn() -> receiver() end)
  {:ok, ls} = Link.start(sender)
  {:ok, lr} = Link.start(receiver)
  send(ls,{:connect, lr})
  send(lr, {:connect, ls})
  send(sender, {:connect, ls})
  send(receiver, {:connect, lr})
  :ok
end
```

This program will start two processes, the `sender` and the `receiver`;  note the order in which we have to start the processes. The two master processes are started first in order to provide the link processes with their respective master. Once the link processes are created we can connect them together and then connect the master processes to the link processes.

The `link_sender` process could look like follows. Implement the corresponding receive process and see if your systems runs.

```elixir
def sender() do
  receive do
    {:connect, lnk} ->
      :io.format("sender connected to link ~w ~n", [lnk]),
      :io.format("sending hello~n", [])
      send(lnk, {:send, :hello})
  end
end
```

