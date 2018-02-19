## Getting Started

In this seminar session we will look at different ways to represent data, using lists, trees and tuples to find the best representation. The *best representation* could of course mean many things, we might need a representation that gives us efficient code or we might want a representation that is easy to explain, implement and maintain. We will start by using quite simple representations and then refine them to gain better performance.

To have something to work with we will implement the *Huffman* encoding and decoding functions. You should do some reading on Huffman coding, this text will not explain the algorithm but how to implement it.

## Huffman Overview

Huffman coding can divided into two parts, one part is how to construct the coding tables and the other, much simpler, is how to encode or decode a text using the tables.

The idea behind Huffman coding is of course to encode frequent characters with few bits and infrequent characters with more bits. To keep things simple we will represent sequences of bits as lists of zeros and ones but this could of course be changes if we intend to do a real implementation that reads and writes to files. For our experiments it is sufficient.

The table should give a one to one mapping from characters to codes but we might use one representation when we encode text and another when we decode text; the information it holds is the same but we might want to do this for efficiency.

Once we are done we will have a module that defines the following functions:

- `tree(sample)`: create a *Huffman tree* given a sample text.
- `encode_table(tree)`: create an *encoding table* containing the mapping from characters to codes given a Huffman tree.
- `decode_table(tree)`: create an *decoding table* containing the mapping from codes to characters given a Huffman tree.
- `encode(text, table)`: encode the text using the mapping in the table, return a sequence of bits.
- `decode(sequence, table)`: decode the bit sequence using the mapping in table, return a text.

Start by defining the module, some compile directives, things that are good to have and dummy code for the functions.

``` elixir
defmodule Huffman do

  def sample do
    'the quick brown fox jumps over the lazy dog
    this is a sample text that we will use when we build
    up a table we will only handle lower case letters and
    no punctuation symbols the frequency will of course not
    represent english but it is probably not that far off'
  end

  def text()  do
    'this is something that we should encode'
  end
  
  def test do
    sample = sample()
    tree = tree(sample)
    encode = encode_table(tree)
    decode = decode_table(tree)
    text = text()
    seq = encode(text, encode)
    decode(seq, decode)
  end
  
  def tree(sample) do
    # To implement...
  end
  
  def encode_table(tree) do
    # To implement...
  end
  
  def decode_table(tree) do
    # To implement...
  end
  
  def encode(text, table) do
    # To implement...
  end
  
  def decode(seq, tree) do
    # To implement...
  end
end
```

## The Table

In order to create the Huffman tree we need first to find out the frequency distribution in our sample text. Once we have the frequency distribution we can start building the tree.

``` elixir
def tree(sample) do
  freq = freq(sample)
  huffman(freq)
end
```

The sample is of course a list of characters (`[102, 111, 111]`), you should run through this list and collect the frequencies of the characters. If “foo” was the sample text we should have the frequencies *f/1*, *o/2*. How would you represent this information? Note that you need not know beforehand which characters that will occur in the sample.

You will probably end up with a structure that looks like this, but how you represent the frequencies is up to you.

``` elixir
def freq(sample) do
  freq(sample, ...)
end

def freq([], freq) do
  ...
end

def freq([char | rest], freq) do
  freq(rest, ...)
end
```

Now once we have the frequencies we will create a Huffman tree. This is simpler than you might think but before you read further you must understand why we create a tree and what properties it should have. If you started to read this with out understanding how Huffman coding works this is the time to stop reading.

## The Huffman Tree

OK, so a Huffman tree is a tree with the characters in the leafs but the low frequency characters have long branches and high frequency characters have short branches. Assume we represent a leaf with a single character and a node as a simple tuple with two branches: `{left, right}`.

If you turn your table into an ordered sequence of leafs where each leaf represents a character and its frequency. The “foo” example above would correspond to the sequence `[{'f', 1}, {'o', 2}]`. You could also view this as a frequency table where each entry is a tuple `{tree, freq}`, after all, a single character is a leaf.

Now, assuming we have such a sequence, what would happen if we took the two smallest elements (lowest frequencies) and combined them into a new node `{{c1, c2}, f1 + f2}` and added the node to the remaining sequence while keeping the sequence sorted? Can we repeat this process, what will the final result be?

How do we represent the sequence so that it is easy to find the two smallest elements? How do we keep this representation? What is the final result when you only have one element in your sequence?

### The Encoding Table

I assume now that you have a Huffman tree and it is time to extract the codes. The codes are of course hidden in the tree in the branches and the code of a character is the path to the leaf holding the character (*left*, *left*, *right*, *left* or  *0, 0, 1, 0*).

Traverse the tree, and collect the characters in the leafs. Keep track of the path to the leaf and record this path as a sequence of zeros and ones. When you’re done you should have something like `[{'f', [1, 1, 0]}, {'o', [1, 0, 1, 0]}, ...]`, or whatever the tree looks like.

Start by writing a function that only collects the characters, once this is mastered you can start to keep track of the path.

### Half Way

Half-way there might be an exaggeration but at lest you’re now done with the first part, you have a mapping from characters to Huffman codes. It’s represented by a list of tuples `{'f', [1, 1, 0]}`, one for each characters. Time to use this table in the encoding and decoding.
