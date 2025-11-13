---
layout: post
title: "SmartCheck: Redux"
date: 2014-06-17
categories: 
  - "haskell"
  - "software"
tags: 
  - "quickcheck"
  - "smartcheck"
---

A few years ago, I started playing with the idea of making counterexamples from failed tests easier to understand. (If you're like me, you spend a lot of time debugging.) Specifically, I started from [QuickCheck](http://www.haskell.org/haskellwiki/Introduction_to_QuickCheck2), a test framework for Haskell, which has since been ported to many other languages. From this, a tool called [SmartCheck](https://github.com/leepike/SmartCheck) was born.

In this post, I'm not going to describe the technical details of SmartCheck. There's [a paper I wrote](https://github.com/leepike/SmartCheck/blob/master/paper/paper.pdf) for that, and it was just accepted for publication at the [2014 Haskell Symposium](http://www.haskell.org/haskell-symposium/2014/index.html) (warning: the paper linked to will change slightly as I prepare the final version).

Rather, I want to give a bit of perspective on the project.

As one reviewer for the paper noted, the paper is not fundamentally about functional programming but about testing. This is exactly right. From the perspective of software testing in general, I believe there are two novel contributions (correct me ASAP if I'm wrong!):

1. It combines counterexample generation with counterexample reduction, as opposed to [delta-debugging](http://blog.regehr.org/archives/527)\-based approaches, which performs deterministic reduction, given a specific counterexample. One possible benefit is SmartCheck can help avoid stopping at local minima during reduction, since while shrinking, new random values are generated.  **Update: as John Regehr points out in the comments below, his group has already done this in the domain of C programs.  See [the paper](https://www.cs.utah.edu/~regehr/papers/pldi12-preprint.pdf).**
2. Perhaps the coolest contribution is generalizing sets of counterexamples into formula that characterize them.

I'd be interested to see how the work would be received in the software testing world, but I suppose first it'd need to be ported to a more mainstream language, like C/C++/Java.

Compared to QuickCheck specifically, QuickCheck didn't used to have generics for implementing shrinking functions; recent versions include it, and it's quite good. In many cases, SmartCheck outperforms QuickCheck, generating smaller counterexamples faster.  Features like counterexample generalization are unique to SmartCheck, but being able to test functions is unique to QuickCheck. Moreover, I should say that SmartCheck uses QuickCheck in the implementation to do some of the work of finding a counterexample, so thanks, QuickCheck developers!

When you have a tool that purports to improve on QuickCheck in some respects, it's natural to look for programs/libraries that use QuickCheck to test it. I found that surprisingly few Hackage packages have QuickCheck test suites, particularly given how well known QuickCheck is. The one quintessential program that does contain QuickCheck tests is [Xmonad](https://hackage.haskell.org/package/xmonad), but I challenge you to think of a few more off the top of your head! This really is a shame.

The lack of (public) real-world examples is a challenge when developing new testing tools, especially when you want to compare your tool against previous approaches. More generally, in testing, it seems there is a lack of standardized benchmarks. What we really need is analogue of the SPEC CPU2000 performance benchmarks for property-based testing, in Haskell or any other language for that matter.  I think this would be a great contribution to the community.

In 1980, Boyer and Moore developed a linear-time majority vote algorithm and verified an implementation of it. It took until 1991 to publish it after many failed tries. Indeed, in the decade between invention and publication, others had generalized their work, and it being superseded was one of the reasons reviewers gave for rejecting it! ([The full story can be found here](http://www.cs.utexas.edu/~moore/best-ideas/mjrty/).)

To a small extent, I can empathize. I submitted a (slightly rushed) version of the paper a couple years ago to ICFP in what was a year of record submissions. One reviewer was positive, one luke-warm, and one negative. I didn't think about the paper much over the following year or two, but I got a couple of requests to put the project on Hackage, a couple of reports on usage, and a couple of inquiries about how to cite the draft paper. So after making a few improvements to the paper and implementation, I decided to try again to publish it, and it finally will be.

As I noted above, this is not particularly a Haskell paper. However, an exciting aspect of the Haskell community, and more generally, the functional programming community, is that it is often exploring the fringes of what is considered to be core programming language research at the time. I'm happy to be part of the fringe.
