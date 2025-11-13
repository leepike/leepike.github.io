---
layout: post
title: "Copilot: A Hard Real-Time Runtime Monitor"
date: 2010-08-22
categories: 
  - "embedded-software"
  - "haskell"
  - "software"
---

I'm the principal investigator on a [NASA-sponsored research project](http://www.reuters.com/article/idUS221847+23-Apr-2009+PRN20090423) investigating new approaches for monitoring the correctness of safety-critical guidance, navigation, and control software at run-time.  We just got a paper accepted at the [Runtime Verification Conference](http://www.rv2010.org/) on some of our recent work developing a language for writing monitors.  The language, Copilot, is a domain-specific language (DSL) embedded in Haskell that uses the powerful [Atom DSL](http://hackage.haskell.org/package/atom) as a back-end.  Perhaps the best tag-line for Copilot is, "Know how to write Haskell lists?  Good; then you're ready to write embedded software."

Stay tuned for a software release and updates on a flight-test of our software on a NASA test UAV...  In the meantime, [check out the paper](https://leepike.github.io//pub_pages/rv2010.html)!
