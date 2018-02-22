## Introduction

In this exercise you will implement a Mandelbrot set generator, or rather an image generator. You should do some reading so that you understand the basics of what the Mandelbrot set is and why it can generate some beautiful images; this text only contains a minimal explanation.

## Mandelbrot

The Mandelbrot set is defined as the set of complex numbers $$c$$ for which the sequence $$z_n$$ does not approach infinity. The value $$z_n$$ is defined as follows:

$$
    z_0 &= &0 \\ 
    z_{n+1} & = &z_n^2 + c
$$

If you remember how to do the square of a complex numbers you know everything there is to know to start:

$$ (x + yi)^2 = x^2 - y^2 + 2xyi$$

How do we know if a complex number $$(x + yi)$$ belongs to the Mandelbrot set? We could of course start to compute $$z_n$$ for higher values and see if we approach infinity but that would of course take a very long time (to say the least). 

An observation saves us from spending the rest of our lives computing
$z_n$: {\em if $|z_n| > 2$ then there is no turning back, $z_n$ will
  only increase in size}. The absolute value of a complex number is of
course:

$$|(x + yi)| = \sqrt{x^2 + y^2}  $$

We still do not want to compute forever; if the number actually does
belong to the Mandelbrot set we will of course never hit the
threshold. Therefore, we set an upper limit $n$ that will be the
maximum {\em depth} of our computation.

So given a maximum value of $n$, we can for any complex number $c$ say
if it {\em definitely does not belong to} or if it {\em could possibly
  belong to} the Mandelbrot set. In the case where we know for sure
that the number does not belong to the set we also have a value $i$
which was the point where $|z_i| > 2$. This value $i$ is the color we
need to generate a beautiful Mandelbrot image.
