---
layout: post
title: "An Atomic Fibonacci Server: Exploring the Atom (Haskell) DSL"
date: 2009-05-05
categories: 
  - "software"
tags: 
  - "atom"
  - "embedded-c"
  - "fibonacci"
  - "haskell"
---

**This post is consistent with [Atom 0.0.1](http://hackage.haskell.org/package/atom-0.0.1) and not the latest version, [Atom 0.0.5](http://hackage.haskell.org/package/atom-0.0.5) (the author went off and implemented changes I and others suggested :)).  I'll update the post... soon.**

Tom Hawkins has open-sourced [Atom](http://hackage.haskell.org/cgi-bin/hackage-scripts/package/atom), a domain-specific language (DSL) for writing embedded real-time software. Atom is actually an "embedded DSL" (I prefer the term "lightweight DSL") in the functional language [Haskell](http://en.wikipedia.org/wiki/Haskell_\\(programming_language\\)). It's a lightweight DSL (LwDSL) because you write legal Haskell and let the Haskell compiler do all the heavy lifting. The DSL is a set of special functions and data types and a "compile function" that generates embedded (i.e., no dynamic memory) C code.  You don't have to write your own compiler from scratch.

John Van Enk has already posted a couple of blog entries on using Atom; first on [adding slightly to the LwDSL](http://blog.sw17ch.com/wordpress/?p=84) (one _major_ advantage of a LwDSL is that it's easy to extend the language---you don't have to re-engineer a standalone compiler) and then on [using Atom to blink some LEDs on the Arduino](http://blog.sw17ch.com/wordpress/?p=111).  Keep checking his blog for more updates!

Here, I write a little device and driver program in Atom: the driver sends an index _i_, and the device returns the _i_th [Fibonacci number](http://en.wikipedia.org/wiki/Fibonacci_number). The little bit of challenge in doing this is that the device and driver may run at different rates, so their communication is asynchronous.  How does this work in a language like Atom?

## Writing in the Atom DSL

Let's think about the Fibonacci device (we'll call it `fibDev`) first.  The device `fibDev` will do three things:

1. Wait for a new index _i_ from the driver.
2. Produce a result, fib(_i_).
3. Give the result to the driver.

Let's think about step (2) first.  Think for a second how we'd write this (efficiently) in Haskell:

> ```
> fib :: Int -> Int
> fib n = fst $ fibHlp n
>     where fibHlp n =
>               case n of
>                 0 -> (1, 1)
>                 _ -> let (a,b) = fibHlp (n-1)
>                      in (b,a+b)
> ```

The Atom implementation will use the same algorithm, but it'll look different.  Atom is a [synchronous language](http://en.wikipedia.org/wiki/Synchronous_programming_language), so you specify rules that fire on clock ticks.  Here's what the core of the algorithm looks like in Atom:

> ```
> atom "computeFib" $ do
>   cond $ value runFib
>   cond $ value i >. 0
>   decr i
>   snd <== (value fst) + (value snd)
>   fst <== value snd
> ```

Atom is written in a monadic style.  Here, we have two conditions, both of which must be true for the rule to "fire".  The first condition is that `runFib` is true (telling the device it's in its computation step), and the second condition is that the index is greater than 0 (we stop computing at zero).  If the conditions are true, then the value of `i` is decremented, and we update the values of the `fst` and `snd` variables, corresponding the first and second elements, respectively, of the pair in the Haskell specification. Again, this is legal Haskell; the Atom library defines the special operators (e.g., `>.`).  One great thing about writing embedded code in Atom is that variable updates are synchronous.  For example, in the code above, `fst` is updated to the previous value of `snd``.` That's the core of the Fibonacci device.

The rest of the architecture handles the message passing (in the C code we'll generate, messages are passed via global variables) and synchronization between the driver and device, as summarized below:

\\[caption id="attachment\_73" align="aligncenter" width="340" caption="System Architecture"\\]![System Architecture](blog/images/arch2.jpg "arch2")\\[/caption\\]

We do not assume that `fibDvr` and `fibDev` execute at the same rate, so we handle message passing with a series of handshakes.  First, `fibDvr` sends a new value `x` and notifies `fibDev` that the value is ready (by issuing `newInd`).  `fibDev` acknowledges that `x` has been received with `valRcvd`.  At this point, `fibDvr` knows to wait for `fibDev` to compute fib_(x_).  Once it receives the notice `ansReady`, it reads off the answer, `ans`.

All we have to do now is implement the handshakes.  For example, let's look at step (3) of the device, sending the final answer to the driver.  It's behavior should be clear from the architectural description.

> ```
> atom "sendVal" $ do
>   cond $ value i ==. 0
>   cond $ value runFib
>   runFib   <== false
>   ans      <== value fst
>   ansReady <== true
>   valRcvd  <== false
> ```

And here's step (1) for `fibDev`, waiting for a new index from the driver:

> ```
> atom "getIndex" $ do
>   cond $ not_ (value runFib)
>   cond $ value newInd
>   i        <== value x
>   runFib   <== true
>   fst      <== 1
>   snd      <== 1
>   ansReady <== false
>   valRcvd  <== true
> ```

These three rules for `fibDev` define the body of `fibDev`'s "do" block.

```
fibDev :: Atom ()
fibDev = period 3 $ do ...
```

We tell atom that the period is 3, meaning execute each of our three rules every three clock ticks (based on the underlying clock).

Now that we're comfortable with the language, let's look at the entire definition of `fibDvr` in one go. Recall the job of `fibDvr` is to send a value then wait for an answer.  Our driver will increment values by 5, starting at 0.  It'll stop sending new values if the index is bigger than 50.

> ```
> fibDvr :: Atom ()
> fibDvr = period 20 $ do
>   x        <- word64 "x" 0 -- new index to send
>   oldInd   <- word64 "oldInd" 0 -- previous index sent
>   -- external signals --
>   valRcvd  <- bool' "valRcvd" -- has the device received the new index?
>   ans      <- word64' "ans" -- the newly-computed fib(x)
>   ansReady <- bool' "ansReady" -- is an answer waiting?
>   ----------------------
>   valD     <- word64 "valD" 1 -- local copy of fib(x)
>   newInd   <- bool "newInd" True -- a new index is ready
>   waiting  <- bool "waiting" True -- waiting for a new computation
> 
>   atom "wait" $ do
>     cond $ value valRcvd
>     cond $ not_ $ value waiting
>     newInd  <== false
>     waiting <== true
> 
>   atom "getAns" $ do
>     cond $ value ansReady
>     cond $ value waiting
>     cond $ value x <. 50
>     valD    <== value ans
>     x       <== value x + 5
>     waiting <== false
>     newInd  <== true
>     oldInd  <== value x
> ```

Note that we've specified the period of the driver to be 20, meaning that its two rules get executed every 20 ticks.  So the driver is much slower than the device, but if our handshakes are correct, the two devices communicate correctly for any rates of execution of the two components.  (Proving it for all-time is a classic [model checking](http://en.wikipedia.org/wiki/Model_checking) problem.)

## Compiling to C

We include a little Haskell function that we can call to "compile" `fibDev` and `fibDvr` into embedded C files.  (The `compile` function is part of Atom, and it takes a name for the generated C file and Atom specifications to compile.)

> ```
> compileFib :: IO ()
> compileFib = do
>   compile "fibDev" $ fibDev
>   compile "fibDvr" $ fibDvr
> ```

We can call this from an interpreter for Haskell; it takes about a second to compile. Doing so _almost_ produces the source files `fibDvr.c` and `fibDev.c`. We do a few things manually:

- Write two header files, `fibDvr.h` and `fibDev.h` and import them. This is the code we want to talk to each other through global variables.  We'll also include `stdio.h` so we can `printf` our results.
- Because Atom automatically (_atom_atically? :)) generates variable and function names in the generated code, we declare some of the identifiers in `fibDev.c` to be `static` so they aren't globally visible.
- We `#define` the variable names from the Atom-generated identifiers back to the expected identifiers for the variables that are shared.
- And we add a little main function to execute the code: let's execute the driver and device for 500 clock ticks:
    
    > ```
    > int main() {
    >    while(__clock < 500) {
    >       fibDvr();
    >       fibDev();
    >    }
    >    return 0;
    > }
    > ```
    

Of course, Atom could be extended to handle these things itself---John Van Enk has already [started doing](http://blog.sw17ch.com/wordpress/?p=84) some of it.  In all, our 80-some lines of Atom compile to over 200 lines of embedded C.  So let's test it!

`> gcc -Wall -o fibDvr fibDev.c fibDvr.c > ./fibDvr`

generates the following output:

```
i: 0, fib(i): 1
i: 0, fib(i): 1
i: 0, fib(i): 1
i: 5, fib(i): 8
i: 5, fib(i): 8
i: 10, fib(i): 89
i: 10, fib(i): 89
i: 10, fib(i): 89
i: 15, fib(i): 987
i: 15, fib(i): 987
i: 15, fib(i): 987
i: 15, fib(i): 987
i: 20, fib(i): 10946
i: 20, fib(i): 10946
i: 20, fib(i): 10946
i: 20, fib(i): 10946
i: 25, fib(i): 121393
i: 25, fib(i): 121393
i: 25, fib(i): 121393
i: 25, fib(i): 121393
i: 25, fib(i): 121393
i: 30, fib(i): 1346269
i: 30, fib(i): 1346269
i: 30, fib(i): 1346269
i: 30, fib(i): 1346269
```

Wait, why are we getting the same answers multiple times? Recall that Atom is a synchronous language, so functions are executed based on time (measured in underlying clock ticks), not events. But most times, the guards don't hold, so state isn't updated. That's what we see here.

Oh, we should check our specification. We can do that using our original Haskell specification:

```
> map fib [0,5..30]
[1,8,89,987,10946,121393,1346269]
```

Looks good!

Let me know if this helps you understand Atom, or if you have thoughts on how Atom compares to other languages.
