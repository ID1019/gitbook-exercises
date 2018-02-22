## Introduction

Morse codes were used in the days of telegraphs and manual radio communication. It is similar in the idea to Huffman coding in that it uses fewer symbols for frequent occurring characters and more symbols for the infrequent ones. You task is to write a decoder for Morse signals and decode two secret messages. 

## Morse Codes

There are several standards for Morse codes and we will here use a slightly extended version since we also want to code some special character. The Morse code uses, as you probably know, long and short (often pronounced *di* and *da*) to encode characters. You might therefore think that is identical to Huffman codes but there is a difference. In Morse coding we have a special signal that tells us when one character ends and the next start. The pause between characters is necessary in order to decode a message.

The code for 'a' is *di-da*, 'i' is *di-di* and 'l' is *di-da-di-di*.  If we just had the sequence *di-da-di-di* we would not know if this was "ai" or "j"; we need a third signal, the pause, to tell the difference. A sequence {di-da-pause-di-di-pause} is then decoded as "ai".

How does this change the structure of our decoding tree? In a Huffman tree we only have characters in the leafs and when we hit a leaf we know that we have a complete character and can start from the root again. In a Morse tree we can finish anywhere along the path to a leaf. We thus have characters in every node of the tree (apart from the root).

## The Decoder

The Morse codes that we will use are given in the decoding table in the Appendix. As you see we have represented the table as a tree were each node is on the form:

``` elixir
{:node, character, left, right}
```

An empty tree is represented by the `nil` value and the root holds dummy value instead of a character; if everything works fine we will never have to see the nil nor the dummy value.

To decode a message you only have to choose the left branch when you hear a long signal and a right branch when you hear a short signal. When you hear the pause you have decoded a character and can start from the root again. 

Implement a function `decode(signal, table)` that takes a *signal* and the decoding table and returns a decoded message. The signal is in the form of a string with dots and dashes `.-. .-.. .-.. ---` (i.e. a list with ASCII characters 45, 46 and 32 (dash, dot and space)).

Decode the secret messages below. If you cut and paste the code, make sure that you don't have carriage-return etc in the string. The string should only contain the dash, dot and space characters. 

```
  '.- .-.. .-.. ..-- -.-- --- ..- .-. ..-- 
    -... .- ... . ..-- .- .-. . ..-- 
    -... . .-.. --- -. --. ..-- - --- ..-- ..- ... '
```

```
  '.... - - .--. ... ---... .----- .----- .-- .-- 
    .-- .-.-.- -.-- --- ..- - ..- -... . .-.-.- -.-. 
    --- -- .----- .-- .- - -.-. .... ..--.. ...- .----. 
    -.. .--.-- ..... .---- .-- ....- .-- ----. .--.-- 
    ..... --... --. .--.-- ..... ---.. -.-. .--.-- 
    ..... .---- '
```

If you by accident have two spaces between to characters this might be decoded as `na` which then will generate a string that is not a proper string. To solve this problem you might add a rule for the case where you're in the root of the tree (the character is `na`) and where you're in the root of the tree (the character is `na`) and you hear a space. Then you simply do nothing and just start from the root again. The rule can be made general so that any unknown character will simply make the decoder start from the root.

## The Encoder

To encode messages we simply need the table that gives us codes for each character. You could of course extract this from the decode tree but a more convenient form is given in the Appendix. 

To encode a message you simply have to look up each character in the message and append all the codes together. This is of course a very simple exercise but we can of course make it more complicated that it has to be in order to learn something.

We first define two functions, one that will create a encoding table and one that will do look-up operations. We assume that `codes/1` returns a list of tuples `{char, code}`.

``` elixir
defp lookup(char, table) do
  encoding = List.keyfind(table, char, 0)
  elem(encoding, 1)
end
```

We can now implement the encoder and this can now be free from any knowledge of how the table is represented. Try to encode a message and the use the decoder to see that you got it right.

``` elixir
def encode(text) do
  table = encode_table()
  encode(text, [], table)
end

def encode([], _), do: []
def encode([char | message], table) do 
  code = lookup(char, table),
  append(code, encode(message, table))
end
\end{minted}
