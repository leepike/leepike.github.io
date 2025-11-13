---
layout: post
title: "Meta-Programming and eDSLs"
date: 2011-01-30
categories: 
  - "copilot"
  - "haskell"
  - "software"
---

I've been working on a domain-specific language that is embedded in Haskell (an "eDSL") that essentially takes a set of Haskell stream (infinite list) equations and turns them into a real-time C program implementing the state-machine defined by the streams. It's called [Copilot](http://leepike.github.com/Copilot/), and in fact, it's built on top of another Haskell eDSL called [Atom](http://hackage.haskell.org/package/atom/),1 which actually does the heavy lifting in generating the C code.

For example, here's the Fibonacci sequence in Copilot:

```
fib = do let f = varW64 "f" f .= [0,1] ++ f + (drop 1 f) 
```

I've been writing Copilot libraries recently, and I've had the following realization about eDSLs and meta-programming (let me know if someone has had this idea already!). Many languages treat meta-programming as a second-class feature---I'm thinking of macros used by the C preprocessor, for example (it's probably generous even to call the C preprocessor 'meta-programming'). One reason why Lisp-like languages were exciting is that the _language_ is a first-class datatype, so meta-programming is on par with programming. In my experience, you don't think twice about meta-programming in Lisp. (Haskell is more like C in this regard---you do think twice before using Template Haskell.)

So languages generally treat meta-programming as either second-class or first-class. What's interesting about eDSLs, I think, is that they treat meta-programming as first-class, but programming as _second_\-class! This isn't surprising, since an eDSL is a first-class datatype, but the language is _not_ first-class---its host language is.

Practically, what this means is that you spend very little time actually writing eDSL programs but rather _generating_ eDSL programs. It is natural to layer eDSLs on top of other eDSLs.

This is just how Copilot came about: I was writing various Atom programs and realized that for my purposes, I just needed a restricted set of behaviors provided by Atom that are naturally represented by stream equations (and make some other tasks, like writing an interpreter, easier).

But as soon as Copilot was written, we2 started writing libraries implementing past-time linear temporal logic (LTL) operators, bounded LTL operators, fault-tolerant voting algorithms, regular expressions, and so on.

You wouldn't think about combining the intermediate languages of a compiler into the same program. The idea of a language is more fluid, more organic in the context of eDSLs, where now libraries can be quickly written and different levels can be easily combined.

1Tom Hawkins wrote Atom. 2Credit for Copilot also goes to Sebastian Niller, Robin Morisset, Alwyn Goodloe.
