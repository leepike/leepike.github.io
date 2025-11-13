---
layout: post
title: "SMACCMPilot"
date: 2013-10-08
categories: 
  - "embedded-software"
  - "formal-methods"
  - "haskell"
---

![px4ioar_400](/blog/images/px4ioar_400.jpg)

Over on the [Galois](http://corp.galois.com/) blog is [a post](http://corp.galois.com/blog/2013/10/2/smaccmpilot-open-source-autopilot-software-for-uavs.html) about my current project, building a secure high-assurance autopilot called _SMACCMPilot_.  SMACCMPilot is open-source; [http://smaccmpilot.org/](http://smaccmpilot.org/) is the project's landing page that describes the technologies going into the project with links to the software repositories.  Check it out!

Galois' main approach to building SMACCMPilot is to use embedded domain-specific languages (EDSLs), embedded in Haskell that compile down to restricted versions of C.  (If you're not familiar with the "EDSL approach", and particularly how it might improve the assurance of your systems, check out [this paper](https://leepike.github.io//pub_pages/icfp2012.html) we wrote about our experiences on a separate NASA-sponsored project.)  The project is quickly approaching one of the larger EDSL projects I know about, and it's still relatively early in its development.  The EDSLs we're developing for SMACCMPilot, [Ivory](https://github.com/GaloisInc/ivory) (for embedded C code generation) and [Tower](https://github.com/GaloisInc/tower) (for OS task setup and communication), are suitable for other embedded systems projects as well.

Like many young and active open-source projects, the code is hot off the presses and under flux, and the documentation always lags the implementation, so let me know if you try using any of the artifacts and have problems.  We're happy to have new users and contributors!
