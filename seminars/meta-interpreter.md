# Meta-Interpreter

## Introduction

In this assignment you will implement an interpreter for a small functional language. The language could be the functional subset of Elixir but it does not match exactly how Elixir is defined \(but maybe it should\). The exercise will hopefully give you a better understanding of how a functional programming language is defined and how powerful a functional programming language can be as tool; the size of our interpreter is surprisingly small. 

## The Overall Picture

Before staring the implementation you need to get an overall picture of the goal and what is actually needed. Never start coding one piece in a jigsaw puzzle if you don't know what the final picture should look like or how the piece should fit in.

### A Meta-Interpreter

An interpreter, in computer engineering terminology, is a program that takes a program as input and executes the statements of the program to produce a result. Some programming systems use only a interpreter to execute programs but most systems today use a compiler to produce machine code that is then executed either directly or in a virtual machine. The advantage of an interpreter is that we do not have to wait for a compilation phase, the disadvantage is that the interpreter is much slower \(typically a factor ten to one hundred\). Do some reading and find out if the programming languages that you know uses a compiler or an interpreter.

A meta-interpreter, also called self-interpreter, is an interpreter that is implemented in the language that it interprets. The reason for this not obvious but if you have a compiled system and need an interpreter of the same language, why not implement it in the language itself - this is probably the language you know best. We will develop an interpreter for a subset of the Elixir language. Since it is not the complete language it is not a true meta-interpreter but it's close.

### Expressions

Our target programming language is a limited functional language and in its first incarnation it will be limited to simple expressions; We will not have any function calls nor any program with function definitions (you might not even call this much of language but it will be a good starting point). This is an example of a **sequence** in the language:

``` elixir
x = _foo; y = :nil; {z, _} = {:bar, :grk}; {x, {z, y}}
```
