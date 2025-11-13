---
layout: post
title: "Stable Names in Haskell"
date: 2011-11-26
categories: 
  - "copilot"
  - "haskell"
  - "software"
---

[Stable names](http://www.haskell.org/ghc/docs/latest/html/libraries/base-4.4.1.0/System-Mem-StableName.html) in GHC "are a way of performing fast (O(1)), not-quite-exact comparison between objects."  Andy Gill showed how to use them to extract the explicit graph from writing recursive functions in his [Data.Reify](http://hackage.haskell.org/package/data-reify) package (and associated paper).  It's a great idea and very practical for embedded domain-specific languages---we've used the idea in [Copilot](http://hackage.haskell.org/package/copilot-2.0.1) to recover sharing.

However, consider [this example](https://gist.github.com/1385118), with three tests executed in GHCI.

For a function with type constraints, stable names fails to "realize" that we are pointing to the same object. As a couple of my colleagues pointed out, the cause is the dictionary being passed around causing new closures to be created. Simon Marlow noted that if you compile with `-O`, the explicit dictionaries get optimized away.

Here are the solutions I have to "fixing" the problem, in the context of a DSL:

- Tell your users that recursive expressions must be monomorphic---only "pure functions" over the expressions of your DSL can be polymorphic.
- Implement a check in your reifier to see how many stable names have been created---if some upper-bound is violated, then the user has created an infinite expression, the expression is extremely large (in which case the user should try to use some sharing mechanism, such as let-expressions inside the language), or we've hit a stable-names issue.
- Ensure your DSL programs are always compiled.
- Of course, you can always take another approach, like Template Haskell or not using recursion at the Haskell level; also check out Andy Gill's [paper](http://www.ittc.ku.edu/~andygill/paper.php?label=DSLExtract09) for other solutions to the observable sharing problem.

I don't see how to use (deep)seq to fix the problem, at least as it's presented in the example above, but I'd be keen to know if there are other solutions.
