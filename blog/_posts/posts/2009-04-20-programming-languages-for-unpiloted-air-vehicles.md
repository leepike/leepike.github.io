---
layout: post
title: "Programming Languages for Unpiloted Air Vehicles"
date: 2009-04-20
categories: 
  - "software"
  - "verification"
tags: 
  - "dsls"
  - "uavs"
---

I recently presented [a position paper](https://leepike.github.io/pub_pages/mixedCrit.html) at a [workshop](http://www.cse.wustl.edu/~cdgill/CPSWEEK09_MCAR/) addressing software challenges for unpiloted air vehicles (UAVs).  The paper is coauthored with [Don Stewart](http://donsbot.wordpress.com/) and John Van Enk.  From a software perspective, UAVs (and aircraft, in general) are fascinating.  Modern aircraft are essentially computers that fly, with a pilot for backup.  UAVs are computers that fly, without the backup.

Some day in the not-so-distant future, we may have UPS and FedEx cargo planes that are completely autonomous (it'll be a while before people are comfortable with a pilot-less airplane).  These planes will be in the commercial airspace and landing at commercial airports.  Ultimately, UAVs must be _transparent_: an observer should not be able to discern whether an airplane is human or computer controlled by its behavior.

You won't be surprised to know a lot of software is required to make this happen.  To put things in perspective, Boeing's [777](http://en.wikipedia.org/wiki/Boeing_777) is said to contain over 2 million lines of newly-developed code; the [Joint Strike Fighter](http://en.wikipedia.org/wiki/F-35_Lightning_II) aircraft is said to have over 5 million lines.  Next-generation UAVs, with pilot AI, UAV-to-UAV and UAV-to-ground communications, and arbitrary other functionality, will have millions more lines of code. And the software must be correct.

In our paper, we argue that the only way to get a hold on the complexity of UAV software is through the use of domain-specific languages (DSLs). A good DSL relieves the application programmer from carrying out boilerplate activities and providers her with specific tools for her domain. We go on and advocate the need for _lightweight_ DSLs (LwDSLs), also known as _embedded_ DSLs. A LwDSL is one that is hosted in a mature, high-level language (like [Haskell](http://haskell.org/)); it can be thought of as domain-specific libraries and domain-specific syntax.  The big benefit of a LwDSL is that a new compiler and tools don't need to be reinvented. Indeed, as we report in the paper, companies realizing the power of LwDSLs are quietly gaining a competitive advantage.

Safety-critical systems, like those on UAVs, combine multiple software subsystems.  If each subsystem is programmed in its own LwDSL hosted in the same language, then it is easy to compose testing and validation across subsystem boundaries. (Only testing within each design-domain just won't fly, pun intended.)

The "LwDSL approach" won't magically make the problems of verifying life-critical software, but "raising the abstraction level" must be our mantra moving forward.
