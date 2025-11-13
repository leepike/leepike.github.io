---
layout: post
title: "Stop Regression Testing"
date: 2013-12-16
---

Some of my colleagues and I have been building a large software application, the open-source [SMACCMPilot](smaccmpilot.org) autopilot system, using Haskell-based embedded domain-specific languages (EDSLs) to generate embedded C code.  Our thesis is that EDSLs both increase our productivity _and_ increase the assurance of our application; so far, we believe [our thesis is holding up](http://smaccmpilot.org/software/properties.html): we've created a complex 50k lines-of-code application in under two engineer years that is (mostly) free of undefined and memory-unsafe behavior.

Of course, there are always bugs.  Even with a fully formally-verified system, there are likely bugs in the specification/requirements, bugs in the hardware, or bugs in the users.  But our hope is that most of our bugs now are logical bugs rather than, say, arithmetic bugs (e.g., underflow/overflow, division by zero) or resource bugs (e.g., null-pointer dereference or buffer overflows).

However, since the EDSL we are using, [Ivory](http://smaccmpilot.org/languages/index.html), is relatively young---we've been writing SMACCMPilot in the language while developing the language---there have been cases where we have not checked for unsafe programs as stringently as possible.  For example, while we insert arithmetic underflow/overflow checks in general, we (ahem, I) neglected to do so for initializers for local variables, so an initializer might overflow with an unexpected result.

If we were writing our application in a typical compiled language, even a high-level one, and found such a bug in the compiler, we would perhaps file a bug report with the developers.  If it were an in-house language, things would be simpler, but modifying a large stand-alone compiler can be difficult (and introduce new bugs).  Most likely, the compiler would not change, and we'd either make some ad-hoc work-around or introduce regression tests to make sure that the specific bug found isn't hit again.

But with an EDSL, the situation is different.  With a small AST, it's easy to write new passes or inspect passes for errors.  Rebuilding the compiler takes seconds.  Fixing the bug with initializers took me a few minutes.

So introducing regression tests about the behavior of initializers isn't necessary; we've changed the compiler to _eliminate_ that class of bugs in the future.

More generally, we have a different mindset programming in an EDSL: if a kind of bug occurs a few times, we change the language/compiler to eliminate it (or at least to automatically insert verification conditions to check for it).  Instead of a growing test-suite, we have a growing set of checks in the compiler, to help eliminate both our bugs and the bugs in all future Ivory programs.

**Edit (16 Dec. 2013)**: a few folks have noted that the title is a bit strong: it absolutely is, that's probably why you read it. :)  Yes, you should (regression) test your applications.  You might consider testing your compiler, too.  Indeed, the point is that modifying the compiler can sometimes eliminate testing for a class of bugs and sometimes simply supports testing by inserting checks automatically into your code.
