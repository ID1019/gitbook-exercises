# Sorting

Let's learn how to work with list by implementing different sorting functions. There are several ways to sort a list and you should know them all. We will start with the most basic algorithm and then try some other \(more or less good\).

### Insertion sort

In _insertion sort_, you sort a list of elements by taking them one at
a time and _insert_ them into an already sorted list. The already
sorted list will of course be empty when we start but will when we are
done contain all the elements.

Start by defining a function `insert(element, list)`, that inserts the
element at the right place in the list. Think of the two mayor cases,
what to do if the list is empty and what to do if the list contains at
least one element. Assume that the elements are integers and can be
compared using the regular $$<$$ operator.

Once you have `insert/2` working, implement the sorting function
`isort(list)`; again what should you do if the list is empty, what
should you do if it contains at least one element?

Now all you have to do is provide a function `isort(list)`, that calls the function `insert/2` using the right arguments.

```elixir
def isort(l) do
    [] -> 
      ...
    [h | t] ->
      ...
  end
end
```

Try also to rewrite the `isort` function using the clause syntax; same-same but different.

As an exercise you can try to rewrite the `isort/1` function to be
tail recursive. As you have probably written it now you first do the
recursive call to sort the rest of the list and then do the
insertion. One could work with an accumulating argument that grows as we insert one element after the other.

```elixir
def isort(l) do isort(l, []) end

def isort(l, sofar) do
    [] -> 
      ...
    [h | t] ->
      ...
  end
end
```

In this case it does however not help us much since `insert/2` is not
tail recursive so we will use a lot of stack space anyway. There are
ways around this but it is a rather pointless exercise that will
probably be slower than the version you have now.


### Merge sort

In _merge sort_, you divide the list into two \(as equal as possible\) list. Then you merge sort each of these lists to obtain two sorted sub-lists. These sorted sub-lists are then _merged_ into one final sorted list.

The two lists are merged by picking the smallest of the elements from each of the lists. Since each list is sorted, one need only to look at the first element of each list to determine which element is the smallest.

The skeleton code below will give you an idea of what the solution will look like. Here we do use the clause syntax when defining merge, you can try to define it using _case expressions_ but it becomes a bit messy.

```elixir
def msort(l) do 
    case ... do 
        ... -> ... 
        ... -> 
            {.., ...} = msplit(l, [], []) 
            merge(msort(...), msort(...)) 
    end 
end

def merge(..., ...) do ... end 
def merge(..., ...) do ... end 
def merge(..., ...) do 
    if ... 
        merge(.., ...) 
    else 
        merge(.., ...) 
    end 
end

def msplit(..., ..., ...) do 
    case ... do 
        ...  -> {..., ...} 
        ... -> msplit(..., ..., ...) 
    end
end
```

### Quicksort

The _quicksort_ algorithm sounds even quicker than merge sort but this is not true. The idea is similar but now we "do our sorting on the way down". First split the list into two parts, one containing low elements and one containing high elements. Then sort the two lists and when you're done append the results.

Splitting the lists is done using the first element in the list as a _pivot element_, all smaller or equal than this is added in one list and all larger in one list. When you're appending the final result, remember to put the pivot element in the middle.

```elixir
def qsort(...) do ... end
def qsort([p | l]) do 
  {..., ...} = qsplit(p, l, [], [])
  small = ...
  large = ...
  append(small, [p | large])
end


def qsplit(_, [], small, large) do ... end
def qsplit(p, [h | t], small, large) do
  if ...  do
    ...
  else
    ...
  end
end

def append(..., ...) do
  case ... do
    [] -> ...
    [h | t] -> ...
  end
end
```

