# Meta-Interpreter

## Introduction

In this assignment you will implement an interpreter for a small functional language. The language could be the functional subset of Elixir but it does not match exactly how Elixir is defined \(but maybe it should\). The exercise will hopefully give you a better understanding of how a functional programming language is defined and how powerful a functional programming language can be as tool; the size of our interpreter is surprisingly small.

## The Overall Picture

Before staring the implementation you need to get an overall picture of the goal and what is actually needed. Never start coding one piece in a jigsaw puzzle if you don't know what the final picture should look like or how the piece should fit in.

### A Meta-Interpreter

An interpreter, in computer engineering terminology, is a program that takes a program as input and executes the statements of the program to produce a result. Some programming systems use only a interpreter to execute programs but most systems today use a compiler to produce machine code that is then executed either directly or in a virtual machine. The advantage of an interpreter is that we do not have to wait for a compilation phase, the disadvantage is that the interpreter is much slower \(typically a factor ten to one hundred\). Do some reading and find out if the programming languages that you know uses a compiler or an interpreter.

A meta-interpreter, also called self-interpreter, is an interpreter that is implemented in the language that it interprets. The reason for this not obvious but if you have a compiled system and need an interpreter of the same language, why not implement it in the language itself - this is probably the language you know best. We will develop an interpreter for a subset of the Elixir language. Since it is not the complete language it is not a true meta-interpreter but it's close.

### Expressions

Our target programming language is a limited functional language and in its first incarnation it will be limited to simple expressions; We will not have any function calls nor any program with function definitions \(you might not even call this much of language but it will be a good starting point\). This is an example of a **sequence** in the language:

```elixir
x = _foo; y = :nil; {z, _} = {:bar, :grk}; {x, {z, y}}
```

A **sequence** consist of a, possibly empty, sequence of pattern matching expression followed by a single expression. This is a bit more restrictive compared to Elixir sequences but it does not limit the expressiveness of our language. Simple expressions will for now be limited to **terms**. If we use a BNF grammar we can describe a sequence in the following way:

```text
<sequence> ::= <expression>
               <match> ';' <sequence>

<match> ::= <pattern> '=' <expression> 

<expression> ::= <atom>
                 <variable>
                 '{' <expression> ',' <expression> '}'
```

### Terms, data structures and patterns

Expressions of the form that we have just described are also referred to as **terms**. We will later see examples of expression that are more complicated and we will talk about expressions in general and **term expressions** but for now we will simply call them **terms**.

It is important that we are clear on the terminology when we discuss our language. There is a difference between **terms**, **patterns** and **data structures**. In the sequence previously shown, the following constructs are terms: `:foo`, `:nil`, `{:bar, :nil}` and, `{x, {y, z}}`. Terms are syntactical constructs that, _when evaluated_, will result in **data structures**; data structures are thus something that we handle during execution, the terms are only recipes for how these structures should be constructed.

If we _evaluate_ the sequence above we will obtain the data structure _{:foo, {:bar, :nil}}_. We will in this text use _italics_ when we refer to the data structures and `teletype` when we talk about the terms.

For now, terms are restricted to atoms, variables and the binary compound **cons** structure. Once we understand how to handle this subset it's fairly easy to extend the language.

The patterns in the sequence are: `x`, `y` and `{z, _}`. The syntax we use for patterns, is the same as the one that we use for terms but we are also allowed to use an underscore \(`_`\) to represent a **don't-care** variable. This variable acts as a placeholder for a data structure that we have no interest in.

