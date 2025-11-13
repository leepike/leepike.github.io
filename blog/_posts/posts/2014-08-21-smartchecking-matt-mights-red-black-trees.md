---
layout: post
title: "SmartChecking Matt Might's Red-Black Trees"
date: 2014-08-21
categories: 
  - "haskell"
  - "software"
  - "verification"
---

Matt Might gave a nice [intro to QuickCheck](http://matt.might.net/articles/quick-quickcheck/) via testing red-black trees recently. Of course, QuickCheck has been around for over a decade now, but it's still useful (if underused--why aren't _you_ QuickChecking your programs!?).

In a couple of weeks, I'm [presenting a paper](https://github.com/leepike/SmartCheck/blob/master/paper/paper.pdf?raw=true) on an alternative to QuickCheck called [SmartCheck](https://github.com/leepike/SmartCheck) at the [Haskell Symposium](http://www.haskell.org/haskell-symposium/).

SmartCheck focuses on efficiently shrinking and generalizing large counterexamples. I thought it'd be fun to try some of Matt's examples with SmartCheck.

The kinds of properties Matt Checked really aren't in the sweet spot of SmartCheck, since the counterexamples are so small (Matt didn't even have to define instances for shrink!). SmartCheck focuses on shrinking and generalizing large counterexamples.

Still, let's see what it looks like. (The code can be found [here](https://github.com/leepike/SmartCheck/tree/master/examples/RedBlackTrees).)

SmartCheck is only interesting for failed properties, so let's look at an early example in Matt's blog post where something goes wrong. A lot of the blog post focuses on generating sufficiently constrained arbitrary red-black trees. In the section entitled, "A property for balanced black depth", a property is given to check that the path from the root of a tree to every leaf passes through the same number of black nodes. An early generator for trees fails to satisfy the property.

To get the code to work with SmartCheck, we derive Typeable and Generic instances for the datatypes, and use GHC Generics to automatically derive instances for SmartCheck's typeclass. The only other main issue is that SmartCheck doesn't support a \`[forall](http://hackage.haskell.org/package/QuickCheck-2.7.6/docs/Test-QuickCheck-Property.html#v:forAll)\` function like in QuickCheck. So instead of a call to QuickCheck such as

`> quickCheck (forAll nrrTree prop_BlackBalanced)`

We change the arbitrary instance to be the nrrTree generator.

Because it is so easy to find a small counterexample, SmartCheck's reduction algorithm does a little bit of automatic shrinking, but not too much. For example, a typical minimal counterexample returned by SmartCheck looks like

`T R E 2 (T B E 5 E)`

which is about as small as possible. Now onto generalization!

There are three generalization phases in SmartCheck, but we'll look at just one, in which a formula is returned that is universally quantified if every test case fails. For the test case above, SmartCheck returns the following formula:

`forall values x0 x1: T R E 2 (T B x1 5 x0)`

Intuitively, this means that for any well-typed trees chosen that could replace the variables x0 and x1, the resulting formula is still a counterexample.

The benefit to developers is seeing instantly that those subterms in the counterexample probably don't matter. The real issue is that E on the left is unbalanced with (T B E 5 E) on the right.

One of the early design decisions in SmartCheck was focus on structurally shrinking data types and essentially ignore "base types" like Int, char, etc. The motivation was to improve efficiency on shrinking large counterexamples.

But for a case like this, generalizing base types would be interesting. We'd hypothetically get something like

`forall values (x0, x1 :: RBSet Int) (x2, x3 :: Int): T R E x2 (T B x1 x3 x0)`

further generalizing the counterexample. It may be worth adding this behavior to SmartCheck.

SmartCheck's generalization begins to bridge the gap from specific counterexamples to _formulas_ characterizing counterexamples. The idea is related to [QuickSpec](http://www.cse.chalmers.se/~nicsma/papers/quickspec.pdf), another cool tool developed by Claessen and Hughes (and SmallBone). Moreover, it's a bridge between testing and verification, or as Matt puts it, from the 80% to the 20%.
