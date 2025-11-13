---
layout: post
title: "N-Version Programming...  For the nth Time"
date: 2009-04-27
categories: 
  - "fault-tolerance"
  - "software"
tags: 
  - "n-version-programming"
  - "reliability"
---

Software contains faults.  The question is how to cost-effectively reduce the number of faults.  One approach that gained traction and then fell out of favor was _[N-version programming](http://en.wikipedia.org/wiki/N-version_programming)_.  The basic idea is simple: have developer teams implement a specification independent from one another.  Then we can execute the programs concurrently and compare their results.  If we have, say, three separate programs, we vote their results, and if one result disagrees with the others, we presume that program contained a software bug.

_N_\-version programming rests on the assumption that software bugs in independently-implemented programs are random, statistically-uncorrelated events.  Otherwise, multiple versions are not effective at detecting errors if the different versions are likely to suffer the same errors.

John Knight and Nancy Leveson famously debunked this assumption on which _N_\-version programming rested in the "Knight-Leveson experiment" they published in 1986.  In 1990, Knight and Leveson published a brief summary of the original experiment, as well as responses to subsequent criticisms made about it, in their paper, [_A Reply to the Criticisms of the Knight & Leveson Experiment_](http://www.google.com/url?sa=t&source=web&ct=res&cd=1&url=http%3A%2F%2Fsunnyday.mit.edu%2Fcritics.pdf&ei=ztHqSeytCoKUswOF1NHiAQ&usg=AFQjCNEuCLgJPwnUw05Z3UkJ4aF4WQL3Gw).

The problem with _N_\-version programming is subtle: it's not that it provides _zero_ improvement in reliability but that it provides significantly less improvement than is needed to make it cost-effective compared to other kinds of fault-tolerance (like [architecture-level fault-tolerance](http://www.google.com/url?sa=t&source=web&ct=res&cd=1&url=http%3A%2F%2Fntrs.nasa.gov%2Farchive%2Fnasa%2Fcasi.ntrs.nasa.gov%2F20080009026_2008008714.pdf&ei=sbv1SYzUD6P0tAPa_bz2Cg&usg=AFQjCNEHVU2b6f74yCuxD8q2pJXwlir9fg)).  The problem is that even small probabilities of correlated faults lead to significant reductions in potential reliability improvements.

Lui Sha has a more recent (2001) _IEEE Software_ [article](http://www.google.com/url?sa=t&source=web&ct=res&cd=2&url=https%3A%2F%2Fagora.cs.illinois.edu%2Fdownload%2Fattachments%2F10581%2FIEEESoftware.pdf&ei=40f1SYKUIJqytAOevLj6Cg&usg=AFQjCNEJORj6PJHwLdoqXVeoAVBn-NX7Ow) discussing _N_\-version programming, taking into account that the software development cycle is finite: is it better to spend all your time and money on one reliable implementation or on three implementations that'll be voted at runtime?  His answer is almost always the former (even if we assume _un_correlated faults!).

But rather than _N_\-versions of the same program, what about different programs compared at runtime?  That's the basic idea of [runtime monitoring](http://rtg.cis.upenn.edu/mac/index.php3).  In runtime monitoring, one program is the implementation and another is the specification; the implementation is checked against the specification at runtime.  This is easier than checking before runtime (in which case you'd have to mathematically _prove_ every possible execution satisfies the specification).  As Sha points out in his article, the specification can be slow and simple.  He gives the example of using the very simple [Bubblesort](http://en.wikipedia.org/wiki/Bubble_sort) as the runtime specification of the more complex [Quicksort](http://en.wikipedia.org/wiki/Sorting_algorithm#Quicksort): if the Quicksort does its job correctly (in _O_(_n_ log _n_), assuming a good pivot element), then checking its output (i.e., a hopefully properly sorted list) with Bubblesort will only take linear time (despite Bubble sort taking _O_(_n_2) in general).

The simple idea of simple monitors fascinates me.  Of course, Bubblesort is not a full specification, though.  Although Sha doesn't suggest it, we'd probably like our monitor to compare the lengths of the input and output lists to ensure that the Quicksort implementation didn't remove elements.  And there's still the possibility that the Quicksort implementation modifies elements, which is also unchecked by a Bubblesort monitor.

But instead of just checking the output, we could sort the same input with both Quickcheck and Bubblesort and compare the results.  This is a "stronger" check insofar as different sorts would have to have exactly the same faults (e.g., not sorting, removing elements, changing elements) for an error not to be caught.  The principal drawback is the latency of the slower Bubblesort check as compared to Quicksort.  But sometimes, it may be ok to signal an error (shortly) after a result is provided.

Just like for _N_\-version programming, we would like the faults in our monitor to be statistically uncorrelated with those in the monitored software.  I am left wondering about the following questions:

- Is there research comparing the kinds of programming errors made in radically different paradigms, such as a Haskell and C?  Are there any faults we can claim are statistically uncorrelated?
- Runtime monitoring itself is predicated on the belief that the implementations of different _programs_ will fail in statistically independent ways, just like _N_\-version programming is.  While more plausible, does this assumption hold?
