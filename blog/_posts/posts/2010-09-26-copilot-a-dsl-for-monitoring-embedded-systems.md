---
layout: post
title: "Copilot: a DSL for Monitoring Embedded Systems"
date: 2010-09-26
categories: 
  - "embedded-software"
  - "fault-tolerance"
  - "hardware"
  - "haskell"
  - "software"
---

In case you missed all the excitement on the [Galois blog](http://www.galois.com/blog/2010/09/22/copilot-a-dsl-for-monitoring-embedded-systems/), what follows is a re-post.

# Introducing Copilot

Can you write a list in Haskell? Then you can write embedded C code using Copilot. Here's a Copilot program that computes the Fibonacci sequence (over Word 64s) and tests for even a numbers:

```

fib :: Streams
fib = do
  "fib" .= [0,1] ++ var "fib" + (drop 1 $ varW64 "fib")
  "t" .= even (var "fib")
    where even :: Spec Word64 -> Spec Bool
          even w = w `mod` const 2 == const 0
```

Copilot contains an interpreter, a compiler, and uses a model-checker to check the correctness of your program. The compiler generates constant time and constant space C code via [Tom Hawkin's Atom Language](http://github.com/tomahawkins/atom) (thanks Tom!). Copilot is specifically developed to write embedded software monitors for more complex embedded systems, but it can be used to develop a variety of functional-style embedded code.

Executing

```
> compile fib "fib" baseOpts
```

generates [fib.c](http://leepike.github.com/Copilot/code/fib.c) and [fib.h](http://leepike.github.com/Copilot/code/fib.h) (with a main() for simulation---other options change that). We can then run

```
> interpret fib 100 baseOpts
```

to check that the Copilot program does what we expect. Finally, if we have [CBMC](http://www.cprover.org/cbmc/) installed, we can run

```
> verify "fib.c"
```

to prove a bunch of memory safety properties of the generated program.

**Galois has open-sourced Copilot (BSD3 licence). More information is available on the [Copilot homepage](http://leepike.github.com/Copilot/). Of course, [it's available from Hackage](http://hackage.haskell.org/package/copilot), too.**

# Flight of the Navigator

![Aberdeen Farms entrance](/blog/images/DSC03839.JPG "Aberdeen Farms entrance")

![View of the James River.](/blog/images/DSC03794.JPG "View of the James River.")

![Pitot tube on the test aircraft.](/blog/images/DSC03836.JPG "Pitot tube on the test aircraft.")

![Our testbed stack: 4 STM32 microcontrollers (ARM Cortex M3s), an SD card for logging data, air pressure sensor, and voltage regulator.](/blog/images/DSC03805.JPG "Our testbed stack: 4 STM32 microcontrollers (ARM Cortex M3s), an SD card for logging data, air pressure sensor, and voltage regulator.")

![](/blog/images/uav1.jpg "Stack in the hull")

![Sebastian installing the stack.](/blog/images/DSC03810.JPG "Sebastian installing the stack.")

![](/blog/images/photo-8.JPG "Getting the plane ready")

Copilot took its maiden flight in August 2010 in Smithfield, Virginia. NASA rents a private airfield for test flights like this, but you have to get past the intimidating sign posted upon entering the airfield. However, once you arrive, there's a beautiful view of the James River.

We were flying on a RC aircraft that NASA Langley uses to conduct a variety of [Integrated Vehicle Health Management](http://www.nasa.gov/centers/ames/research/humaninspace/humansinspace-ivhm.html) (IVHM) experiments. (It coincidentally had Galois colors!) Our experiments for Copilot were to determine its effectiveness at detecting faults in embedded guidance, navigation, and control software. The test-bed we flew was a partially fault-tolerant [pitot tube](http://en.wikipedia.org/wiki/Pitot_tube) (air pressure) sensor. Our pitot tube sat at the edge of the wing. Pitot tubes are used on commercial aircraft and they're a big deal: a number of aircraft accidents and mishaps have been due, in part, to [pitot tube failures](http://en.wikipedia.org/wiki/Pitot-static_system#Pitot-static_related_disasters).

Our experiment consisted of a beautiful hardware stack, crafted by Sebastian Niller of the Technische Universität Ilmenau. Sebastian also led the programming for the stack. The stack consisted of four STM32 ARM Cortex M3 microprocessors. In addition, there was an SD card for writing flight data, and power management. The stack just fit into the hull of the aircraft. Sebastian installed our stack in front of another stack used by NASA on the same flights.

The microprocessors were arranged to provide [Byzantine fault-tolerance](http://en.wikipedia.org/wiki/Byzantine_fault) on the sensor values. One microprocessor acted as the general, receiving inputs from the pitot tube and distributing those values to the other microprocessors. The other microprocessors would exchange their values and perform a fault-tolerant vote on them. Granted, the fault-tolerance was for demonstration purposes only: all the microprocessors ran off the same clock, and the sensor wasn't replicated (we're currently working on a fully fault-tolerant system). During the flight tests, we injected (in software) faults by having intermittently incorrect sensor values distributed to various nodes.

The pitot sensor system (including the fault-tolerance code) is a hard real-time system, meaning events have to happen at predefined deadlines. We wrote it in a combination of [Tom Hawkin's Atom](http://hackage.haskell.org/package/atom), a Haskell DSL that generates C, and C directly.

Integrated with the pitot sensor system are Copilot-generated monitors. The monitors detected

- unexpected sensor values (e.g., the delta change is too extreme),
- the correctness of the voting algorithm (we used [Boyer-Moore majority voting](http://userweb.cs.utexas.edu/~moore/best-ideas/mjrty/index.html), which returns the majority only if one exists; our monitor checked whether a majority indeed exists), and
- whether the majority votes agreed.

The monitors integrated with the sensor system without disrupting its real-time behavior.

![](/blog/images/photo-15.JPG "Getting data from the SD card.")

We gathered data on six flights. In between flights, we'd get the data from the SD card.

![](/blog/images/DSC03831.JPG "The Copilot Team")

![](/blog/images/DSC03838.JPG "The entire flight team")

We took some time to pose with the aircraft. The Copilot team from left to right is Alwyn Goodloe, [National Institute of Aerospace](http://www.nianet.org/); Lee Pike, [Galois, Inc.](http://www.galois.com); Robin Morisset, École Normale Supérieure; and Sebastian Niller, Technische Universität Ilmenau. Robin and Sebastian are Visiting Scholars at the NIA for the project. Thanks for all the hard work!

There were a bunch of folks involved in the flight test that day, and we got a group photo with everyone. We are very thankful that the researchers at NASA were gracious enough to give us their time and resources to fly our experiments. Thank you!

Finally, here are two short videos. The first is of our aircraft's takeoff during one of the flights. Interestingly, it has an electric engine to reduce the engine vibration's effects on experiments.

[http://player.vimeo.com/video/15198286](http://player.vimeo.com/video/15198286)

The second is of AirStar, which we weren't involved in, but that also flew the same day. AirStar is a scaled-down jet (yes, jet) aircraft that was really loud and really fast. I'm posting its takeoff, since it's just so cool. That thing was a rocket!

[http://player.vimeo.com/video/15204969](http://player.vimeo.com/video/15204969)

# More Details

Copilot and the flight test is part of a NASA-sponsored project ([NASA press-release](http://www.reuters.com/article/idUS221847+23-Apr-2009+PRN20090423)) led by [Lee Pike](https://leepike.github.io//) at Galois. It's a 3 year project, and we're currently in the second year.

# Even More Details

Besides the language and flight test, we've written a few papers:

- Lee Pike, Alwyn Goodloe, Robin Morisset, and Sebastian Niller. [Copilot: A Hard Real-Time Runtime Monitor](https://leepike.github.io//pub_pages/rv2010.html). To appear in the proceedings of the _1st Intl. Conference on Runtime Verification (RV'2010)_, 2010. Springer.

This paper describes the Copilot language.

- Lee Pike. [Schrödinger's CRCs (Fast Abstract)](https://leepike.github.io//pub_pages/dsn.html). _40th Annual IEEE/IFIP International Conference on Dependable Systems and Networks (DSN 2010)_, 2010.

Byzantine faults are fascinating. Here's a 2-page paper that shows one reason why.

- Alwyn Goodloe and Lee Pike. [Monitoring distributed real-time systems: a survey and future directions](https://leepike.github.io//pub_pages/monitors.html). NASA Contractor Report NASA/CR-2010-216724, 2010.

At the beginning of our work, we tried to survey prior results in the field and discuss the constraints of the problem. This report is a bit lengthy (almost 50 pages), but it's a gentle introduction to our problem space.

- Lee Pike, Geoffrey M. Brown, and Alwyn Goodloe. [Roll your own test bed for embedded real-time protocols: a Haskell experience](https://leepike.github.io//pub_pages/qc-biphase.html). In [_Haskell Symposium_](http://www.haskell.org/haskell-symposium/2009/), 2009.

Yes, QuickCheck can be used to test low-level protocols.

- Alwyn Goodloe and Lee Pike. [Toward monitoring fault-tolerant embedded systems (extended abstract)](https://leepike.github.io//pub_pages/shm.html). In [_International Workshop on Software Health Management_](http://www.isis.vanderbilt.edu/workshops/smc-it-2009-shm) (SHM'09), 2009.

A short paper motivating the need for runtime monitoring of critical embedded systems.

# You're _Still_ Interested?

We're always looking for collaborators, users, and we may need 1-2 visiting scholars interested in embedded systems & Haskell next summer. If any of these interest you, drop Lee Pike a note (hint: if you read any of the papers or download Copilot, you can find my email).
