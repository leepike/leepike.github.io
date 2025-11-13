---
layout: post
title: "\"Schrodinger's Probability\" for Error-Checking Codes"
date: 2009-05-15
categories: 
  - "fault-tolerance"
  - "software"
tags: 
  - "byzantine-fault"
  - "crc"
  - "haskell"
---

In a [previous post](/blog/2009/04/18/reconsidering-cyclic-redundancy-checks-crc/), I discussed the notion of _Schrödinger CRCs_, first described by Kevin Driscoll et al. in their paper [_Byzantine Fault Tolerance, from Theory to Reality_](http://www.springerlink.com/content/hx63utlym13x85nv/). The basic idea is that error-detecting codes do not necessarily prevent two receivers from obtaining messages that are semantically different (i.e., different data) but syntactically valid (i.e., the CRC matches the respective data words received). The upshot is that even with CRCs, you can suffer [Byzantine faults](http://en.wikipedia.org/wiki/Byzantine_Fault_Tolerance), with some probability.

... So what _is_ that probability of a Schrödinger’s CRC? That's the topic of this post---which cleans up a few of the ideas I presented earlier. I published a short paper on the topic, which I presented at [_Dependable Sensors and Networks, 2010_](http://2010.dsn.org/), while Kevin Driscoll was in the audience!  If you'd prefer to read the PDF or get the slides, they're [here](https://leepike.github.io/pub_pages/dsn.html).

