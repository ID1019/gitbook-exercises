# Binary trees

The simplest form of tree structurs are binary trees. The simplest
form of representing trees i by using three data structures: one for a empty tree, one for a leaf and and for a node with two branches.

```elixir

 :nil                              ## the empty tree
 {:leaf, value}                    ## a leaf
 {:node, value, left, right}       ## a node 

```

Assume we have this simple form of tree representation and also assume
that the trees is ordered with smaller values to the left and larger
values to the right.

## member/2

We can then start by implement a function
`member/2` that takes an element and a tree and determines if the
element is found in the tree or not. Let the function return `:yes` or
`:no` depending on if the element is found.

The fist thing you should think about is what the base cases are. An
element is of course not found in an empty tree but could be present
in a leaf. Note how we use `_` (don't care) in the third clause; if
the element is not found in the leaf it does not matter what we are
looking for nor what is in the leaf. 


```elixir

def member(_, :nil) do :no end
def member(e, {:leaf, e}) do :yes end
def member(_, {:leaf, _}) do :no end


```

If we are looking for an element in a node we first have the situation
when we actually find the element, but what should we do if we don't?


```elixir

def member(e, {:node, e, _, _}) do :yes end
def member(e, {:node, v, left, _}) when e < v do
       :
end
def member(e, {:node, _, _, right})  do
       :
end


```

We could have used an `if-expression` for the two last clauses but we
should learn to love dividing things up in clauses.

## insert/2

Now let's do something slightly more complicated. Define a function
`insert/2` tha takes an element and a tree and returns a new tree
where the element has been inserted at the right place (remember, itś
a sorted tree). We can assume that the value does not already exist in
the tree.


```elixir

def insert(e, :nil)  do  ...  end
def insert(e, {:leaf, v}) when e < v  do  ...   end
def insert(e, {:leaf, v}) do  ...   end
def insert(e, {:node, v, left, right }) when e < v do
   :
end
def insert(e, {:node, v, left, right })  do
   :
end


```

### delete/2

Ok, how hard can it be to remove an element from a tree (or rather
contruct a new tree where the element has been removed. It turn out
that it is rather simple, but only to start with. We can assume that
the element does exist in the tree.

```elixir

def delete(e, {:leaf, e}) do  ...  end
def delete(e, {:node, e, :nil, right}) do  ...  end
def delete(e, {:node, e, left, :nil}) do  ...  end
def delete(e, {node, e, left, right}) do
   :
   hmmm, this is tricky
   :
end
def delete(e, {node, v, left, right}) when e < v do
    {:node, c,  ...,  ...}
end
def delete(e, {node, v, left, right})  do
    {:node, c,  ...,  ...}
end


```

The first three clauses should be straight forward and also the
recursive clauses in the end but what about the tricky case where we
find the element in a node? In order to solve this you first need to
figure out the algorithm to use.

How about this: if you find the element in a node, you remove the
largest element from the left branch and then use this as the value
for the new node.

You shoudl do som drawings and write down some examples so that you
understand what you should do. When you know what you want to do you
first implement a function `rightmost/1´ that finds the rightermost
element in a tree. You can then remove this element using `delete/2`
and you're almost there.


```elixir

def reightmost({:leaf, e}) do ... end
def reightmost({:node, _, _ , right}) do  ...  end

```

## a key-value store

Start from the beginning but this time represent a key-value store as
a binary tree. Let's make things a bit easier by not having a diffeent
representation for a leaf; a leaf is simply a node with to empty
branches. The representation could look like this:

```elixir

:nil                                # an empty tree
{:node, key , value, left, right}   # a node with a key (integer) and value (any)


```

Now with this new representation we could define the functions:

* _lookup/2 : find the value of a key and return either `{:ok, value}` or `:no`
* _remove/2 : given a key, remove the key-value pair (assume it exists)
* _add/3 : add a key-value pair to the tree (modify it if it exists)








