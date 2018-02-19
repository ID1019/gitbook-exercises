# Introduction

Your task is to implement a small web server in Elixir. The aim of this exercise is that you should be able to:

- describe the procedures for using a socket API
- describe the structure of a server process

As a side effect you will also learn a bit about the HTTP protocol.

## A HTTP Parser

Let's start by implementing a HTTP parser. We will not build a complete parser nor will we implement a server that can reply to  queries (yet) but we will do enough to understand how Elixir can parse a HTTP request.

### A HTTP Request
Open a file `http.ex` and declare a module `HTTP` on the first line.

``` elixir
defmodule HTTP do
  
end
```

We're only going to implement the parsing of a HTTP get request; and avoiding some details to make life easier. If you download the RFC 2616 from [www.ietf.org](https://www.ietf.org) you will find this descriptions of a request. From the RFC we have:

```
  Request        = Request-Line               ; Section 5.1
                   *(( general-header         ; Section 4.5
                     | request-header         ; Section 5.3
                     | entity-header ) CRLF)  ; Section 7.1
                   CRLF
                   [ message-body ]           ; Section 4.3
\end{verbatim}
