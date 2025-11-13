---
layout: post
title: "A Fistful of Dollars"
date: 2012-03-08
tags: 
  - "big-ideas"
  - "darpa"
  - "research"
---

A while back I attended an excellent DARPA Proposers' Day. It got me thinking, "If I had a fistful of (a few million) dollars to provide researchers, what sort of program might I start?" Two ideas struck me:

## Getting software right, fast

Software is usually developed in a world with incomplete requirements and with users (and attackers) that try to break your assumptions.  Furthermore, more often than not, developers themselves (perhaps with a QA team, if they're lucky) largely decide when they're "done"---when a version is bug-free enough, secure enough, and meets the specifications to release.

There are a dizzying number of approaches to improving quality: testing (e.g., unit testing, penetration testing, black-box and white-box testing), compiler tools (e.g., type-checking, warnings), formal verification (e.g., model-checking, static analysis, decision procedures), programming methodologies (e.g., pair-programming, extreme programming), etc.  The question is, what combinations of approaches provide benefits and by what factor?

A major portion of the challenge would be to fund experiments that are large enough to draw statistical conclusions about different approaches.  Teams would get points for finishing faster, but lose points for bugs, security vulnerabilities, unimplemented features, etc. uncovered by a separate "read team".  To make the experience more realistic, I'd have career programmers (not students in an undergrad course) to participate in the experiments.  A major portion of the program would be just designing the experiments to ensure statistical conclusions could be drawn from them, but I imagine the results might be enlightening.

## A Believe Search Engine

Imagine a "search" engine in which you could search not for facts, but beliefs.  For example, I might query the likelihood that someone believes that the Earth revolves around the Sun in Russia in 1600.  Or I might ask what the likelihood of someone believing in global warming in Japan in 2012.  Or whatever else.  The engine would return some probability, perhaps with a confidence interval.  I'd imagine being able to query some believe based on geography, time period, religion, nationality, sex, and so forth.  Of course this is a hard, hard problem, and would likely involve search as well technology akin to [IBM's Watson](http://en.wikipedia.org/wiki/Watson_\\(computer\\)).  I'd be curious to know if it were feasible at all.
