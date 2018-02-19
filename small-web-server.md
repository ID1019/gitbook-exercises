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
                 [ message-body ]           ; Section 4.3
```

So a request consist of: a request line, an optional sequence of headers (the * means zero or more), a carriage-return line-feed (CRLF) and, an optional body (optional since it is enclosed in []). We also note that each header is terminated by a carriage-return line-feed. OK, let's go; we implement each parsing function so that it will parse its element and return a tuple consisting of the parsed result and the rest of the string.

``` elixir
def parse_request(r0) do
  {request, r1} = request_line(String.to_charlist(r0))
  {headers, r2} = headers(r1)
  {body, _} = message_body(r2)
  {request, headers, body}
end
```

### The Request Line
Now take a look at the RFC and the definition of the request line.

```
Request-Line   = Method SP Request-URI SP HTTP-Version CRLF
```

The request line consist of: a method, a request URI and, a http version. All separated by space characters and terminated by a carriage-return and line-feed.

The method is one of: `OPTIONS`, `GET`, `HEAD` etc. Since we are only interested in get requests, life becomes easy.

To understand how we implement the parser you must know how strings are represented in Elixir. Strings are lists of integers and one way of writing an integer is `?G`, that is the ASCII value of the character G. So the string "GET" could be written `[?G, ?E, ?T]`, or (if you know your ASCII) as `[71, 69, 84]`. Now let's do some string parsing.

``` elixir
def request_line([?G, ?E, ?T, 32 | r0]) do
  {uri, r1} = request_uri(r0)
  {ver, r2} = http_version(r1)
  [13, 10 | r3] = r2
  {{:get, uri, ver}, r3}
end
```

We match the input string with a list starting with the integers of G, E and T, followed by 32 (which is the ASCII value for space). After having matched the input string with "GET" we continue with the rest of the string `r0`. We find the URI, the version and finally, the carriage return and line feed that is the end of the request line. 

We then return the tuple `{{:get, uri, ver}, r3}`, the first element is the parsed representation of the request line and `r3` is the rest of the string.

### The URI
Next we implement the parsing of the URI. This requires recursive definitions. I know that you can write this as a tail recursive function but right now I don't think it is worth the trouble.

``` elixir
def request_uri([32 | r0]), do: {[], r0}

def request_uri([c | r0]) do
  {rest, r1} = request_uri(r0)
  {[c | rest], r1}
end
```

The URI is returned as a string. There is of course a whole lot of structure to that string. We will find the resource that we are looking for possibly with query information etc. For now we leave it as a string, but feel free to parse it later.

### Version Information
Parsing the version is simple, it's either version "1.0" or "1.1". We represent this by the atoms `v11` and `v10`. This means that we later can switch on the atom rather than again parsing a string. It does of course mean that our program will stop working when 1.2 is release but that will probably not be this week.

``` elixir
def http_version([?H, ?T, ?T, ?P, ?/, ?1, ?., ?1 | r0]) do
  {:v11, r0}
end

def http_version([?H, ?T, ?T, ?P, ?/, ?1, ?., ?0 | r0]) do
  {:v10, r0}
end
```

### Headers
Headers also have internal structure but we are only interested in dividing them up into individual strings and most important find the end of the header section. We implement this as two recursive functions; one that consumes a sequence of headers and one that consumes individual headers.

``` elixir
def headers([13, 10 | r0]), do: {[], r0}

def headers(r0) do
  {header, r1} = header(r0)
  {rest, r2} = headers(r1)
  {[header | rest], r2}
end

def header([13, 10 | r0]), do: {[], r0}

def header([c | r0]) do
  {rest, r1} = header(r0)
  {[c | rest], r1}
end
```

### The body
The last thing we need is parsing of the body and we will make things very easy (even cheating). We assume that the body is everything that is left but the truth is not that simple. If we call our function with a string as input argument, there is little discussion of how large the body is -  but this is not easy if we want to parse an incoming stream of bytes. When do we reach the end, when should we stop waiting for more? The length of the body is therefore encoded in the headers of the request. Or rather in the specification of HTTP 1.0 and 1.1 there are several alternative ways of determining the length of thebody. If you dig deeper into the specs you will find that it is quite messy. In our little world we will however treat the rest of the string as the body.

``` elixir
def message_body(r), do: {r, []}
```

### A Small Test

You now have all the pieces to parse a request; try the string below. Note how we encode the special characters in a string, `\r` is carriage-return and `\n` is line-feed.

``` elixir
'GET /index.html HTTP/1.1\r\nfoo 34\r\n\r\nHello'
```

### What About Replies
We will not deliver very interesting replies from our server but here is a function that generates a HTTP reply with a 200 status code (200 is all OK). Another function can be convenient to have when we want to generate a request. Also export these from the http module, we are going to use them later.

Note the double `\r\n`, one to end the status line and one to end the header section. A proper reply should containing headers that describe the content and the size of the body but a normal browser will understand what we mean.

``` elixir
def ok(body) do
  "HTTP/1.1 200 OK\r\n\r\n #{body}"
end

def get(uri) do
  "GET #{uri} HTTP/1.1\r\n\r\n"
end
```

Note the double `\r\n`, one to end the status line and one to end the header section. A proper reply should contain headers that describe the content and the size of the body but a normal browser will understand what we mean.
