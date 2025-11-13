---
layout: post
title: "Byzantine Cyclic Redundancy Checks (CRC)"
date: 2009-04-18
categories: 
  - "fault-tolerance"
tags: 
  - "byzantine-fault"
  - "crc"
---

I'm working on a [NASA-sponsored project](https://leepike.github.io//index.html#Current_Research_Projects_) to monitor safety-critical embedded systems at runtime, and that's started me thinking about [cyclic redundancy checks](http://en.wikipedia.org/wiki/Cyclic_redundancy_check) (CRCs) again.  Error-checking codes are fundamental in fault-tolerant systems.  The basic idea is simple: a transmitter wants to send a data word, so it computes a CRC over the word.  It sends both the data word and the CRC to the receiver, which computes the same CRC over the received word.  If its computed CRC and the received one differ, then there was a transmission error (there are simplifications to this approach, but that's the basic idea).

CRCs have been around since the 60s, and despite the simple mathematical theory on which they're built (polynomial division in the Galois Field 2, containing two elements, "0" and "1"), I was surprised to see that even today, their fault-tolerance properties are in some cases unknown or misunderstood.  [Phil Koopman](http://www.ece.cmu.edu/~koopman/) at CMU has written a [few nice papers](http://www.ece.cmu.edu/~koopman/personal.html#publication) over the past couple of years explaining some common misconceptions and analyzing commonly-used CRCs.

Particularly, there seems to be an over-confidence in their ability to detect errors.  One fascinating result is the so-called "Schrödinger's CRC," so-dubbed in a paper entitled _[Byzantine Fault Tolerance, from Theory to Reality](http://citeseer.ist.psu.edu/696238.html)_, by Kevin Driscoll et al. A Schrödinger's CRC occurs when a transmitter broadcasts a data word and associated CRC to two receivers.   and at least one of the data words is corrupted in transit and so is the corresponding CRC so that the faulty word and faulty CRC match!  How does this happen?  Let's look at a concrete example:

```
             11-Bit Message           USB-5
Receiver A   1 0 1 1 0 1 1 0 0 1 1    0 1 0 0 1
Transmitter  1 ½ 1 1 0 1 1 ½ 0 1 1    ½ 1 0 0 1
Receiver B   1 1 1 1 0 1 1 1 0 1 1    1 1 0 0 1
```

We illustrate a transmitter broadcasting an 11-bit message to two receivers, A and B. We use [USB-5 CRC](http://en.wikipedia.org/wiki/Cyclic_redundancy_check#Commonly_used_and_standardised_CRCs), generally used to check USB token packets  (by the way, for 11-bit messages, USB-5 has a [Hamming Distance](http://en.wikipedia.org/wiki/Hamming_distance) of three, meaning the CRC will catch any corruption of fewer than three bits in the combined 11-bit message and CRC).  Now, suppose the transmitter has suffered some fault such as a “stuck-at-1/2” fault so that periodically, the transmitter fails to drive the signal on the bus sufficiently high or low. A receiver may interpret an intermediate signal as either a 0 or 1. In the ﬁgure, we show the transmitter sending three stuck-at-1/2 signals, one in the 11-bit message, and two in CRC.  The upshot is an example in which a CRC does _not_ prevent a Byzantine fault---the two receivers obtain different messages, each of which passes its CRC.

One question is how likely this scenario is.  Paulitsch et al. [write](http://www.ece.cmu.edu/~koopman/pubs/paultisch05_dsn_crc_ultradependable.pdf) that The probability of a Schrödinger’s CRC is hard to evaluate.  A worst-case estimate of its occurrence due to a single device is the device failure rate."  It'd be interesting to know if there's any data on this probability.
