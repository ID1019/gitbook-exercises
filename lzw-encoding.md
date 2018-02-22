## Getting Started

Lempel-Ziv-Welch (LZW) is a compression algorithm that takes advantage of frequent occurrence of sequences of characters. It will detect sequences on the fly while doing the compression and thus create individual codes for sequences as it goes along. The beauty of the algorithm is that the decoder must not be told what these new codes mean - it will learn as it does the decoding.

The encoder that we will implement will not use binary encoding i.e. codes are fixed size and are represented by an integer. A real implementation would start off by using, for example, a five-bit code and then increase the code length as needed. By implementing this simpler form you will understand the principles of the algorithm and you can easily extend it to use variable size codes.

Before you start to implement this encoder and decoder you should do some reading on the LZW algorithm so that you have a basic understanding of the process. The devil is as always in the detail and we will see how these are handled as we implement the encoder.

## The table

The encoder and decoder have to agree on an initial alphabet (and in the general case, the code size). We will here use a very small alphabet that consists of the smaller cap letters and the space character. Given this we construct an initial encoder/decoder table that is represented as a list of character sequences and codes.

``` elixir
defmodule LZW do

  @alphabet 'abcdefghijklmnopqrstuvwxyz '
  
  def table do
    n = length(@alphabet)
    numbers = Enum.to_list(1..n)
    map = List.zip([@alphabet, numbers])
    {n + 1, map}
  end
  
end
```

The only sequences we know of in the beginning are the sequences consisting of single characters. We have $28$ characters in total so our table will look like follows:

``` elixir
{28, [{97, 1}, {98, 2}, {99, 3}, ...]}
```

The number of sequences in the table is important to keep track of since we will add new codes as we encode our text. Have in mind that the encoder and decoder will both know the state of the initial table.
