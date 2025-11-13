---
layout: post
title: "Backseat Driving: Copilot Updates"
date: 2010-11-21
categories: 
  - "embedded-software"
  - "formal-methods"
  - "hardware"
  - "haskell"
  - "software"
---

A lot has been going on since the [announcement of Copilot]({{ 'blog/2010/09/26/copilot-a-dsl-for-monitoring-embedded-systems/' | relative_url }}), a Haskell DSL for generating hard real-time C monitors. We've presented Copilot a few times, including at [Runtime Verification 2010](http://www.rv2010.org/), at a [Galois Technical Seminar](http://www.galois.com/blog/2010/11/02/tech-talk-copilot-a-hard-real-time-runtime-monitor/) (video of the talk is [here](http://www.galois.com/blog/2010/11/10/tech-talk-video-copilot-a-hard-real-time-runtime-monitor%E2%80%9D/)), and at a recent NASA Technical Interchange.

Copilot has had five releases since we originally open-sourced the project.  Recent work has focused on making the language more straightforward and improving Copilot libraries.  But first, let me remind you how to use Copilot: you can compile specs to hard real-time C code, you can interpret them, you can model-check them, and you can generate specs to test the compiler and interpreter---you can see a bit about usage [here](http://leepike.github.com/Copilot/).

For example, here's a Copilot specification that generates the Fibonacci sequence (over `Word64`s) and tests for even numbers:

```
fib :: Streams
fib = do
  let f = varW64 "f"
  let t = varB "t"
  f .= [0,1] ++ f + (drop 1 f)
  t .= even f
  where even :: Spec Word64 -> Spec Bool
            even w' = w' `mod` 2 == 0
```

Notice that lists look _almost_ exactly like Haskell lists.

What about something a little more complicated?  Consider the property:

_If the temperature rises more than 2.3 degrees within 2 seconds, then the engine has been shut off._

We might use a Copilot specification like the following to express it, assuming that `temp` and `shutoff` are C variables being sampled at phases 1 and 2 respectively, and the period of execution is 1 second:

```
engine :: Streams
engine = do
  -- external vars
  let temp     = extF "temp" 1
  let shutoff  = extB "shutoff" 2
  -- Copilot vars
  let temps    = varF "temps"
  let overTemp = varB "overTemp"
  let trigger  = varB "trigger"
  -- Copilot specification
  temps    .= [0, 0, 0] ++ temp
  overTemp .= drop 2 temps > 2.3 + temps
  trigger  .= overTemp ==> shutoff
```

Here's something that I think shows why you want to write your DSLs in Haskell: Haskell gives you a macro language for your DSL... for free.  For example, consider the following (more complicated) property:

_"If the engine temperature exeeds 250 degrees, then the engine is shut off within one second, and in the 0.1 second following the shutoff, the cooler is engaged and remains engaged."_

We can more succinctly specify this property using [past-time linear temporal logic (ptLTL)](http://fsl.cs.uiuc.edu/index.php/Past_Time_Linear_Temporal_Logic).  There's a Copilot library for writing those kind of specs, which can be interspersed with normal Copilot streams---the ptLTL specs are highlighted in blue below.  Again, assume a period of execution of 1 second:

```
engine :: Streams
engine = do
  -- external vars
  let engineTemp = extW8 "engineTemp" 1
  let engineOff  = extB "engineOff" 1
  let coolerOn   = extB "coolerOn" 1
  -- Copilot vars
  let cnt        = varW8 "cnt"
  let temp       = varB "temp"
  let cooler     = varB "cooler"
  let off        = varB "off"
  let monitor    = varB "monitor"
  -- Copilot specification
  temp    `ptltl` (alwaysBeen (engineTemp > 250))
  cnt     .=      [0] ++ mux (temp && cnt < 10) (cnt + 1) cnt
  off     .=      cnt >= 10 ==> engineOff
  cooler  `ptltl` (coolerOn `since` engineOff)
  monitor .=      off && cooler
```

Today, I finished updating another feature of Copilot: the ability to send stream values over ports to other components in a distributed system. We had an implementation of this, but it was a bit hacky. Hopefully, it's a bit less hacky now. For example, consider the following specification:

```
distrib :: Streams
distrib = do
  -- Copilot vars
  let a = varW8 "a"
  let b = varB "b"
  -- Copilot spec
  a .= [0,1] ++ a + 1
  b .= mod a 2 == 0 
  -- send commands
  send "portA" (port 2) a 1
  send "portB" (port 1) b 2
```

The blue commands are send commands.  For example, the first command says, "call the C function `portA(str, num)`, where argument `str` is the value of stream `a` and `num` is port number 1." The port number says who to send it to.

These are just a few of the recent updates. We're still working on Copilot, so let me know if you have questions or comments.

Interested? Get Copilot on [Hackage](http://hackage.haskell.org/package/copilot) or [GitHub](https://github.com/leepike/Copilot).
