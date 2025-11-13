---
layout: post
title: "The Worst Offenders"
date: 2012-10-13
categories: 
  - "formal-methods"
  - "verification"
---

I listened to a [Freakonomics](http://www.freakonomics.com/2012/01/24/how-to-get-doctors-to-wash-their-hands-visual-edition/) podcast the other day in which a story about hand-washing at Cedars-Sinai Medical Center was told. It turns out that of all the staff, the doctors had the lowest rate of hand-washing (around 65%, if I recall correctly) in the hospital. This was surprising, since the doctors supposedly have the best education and so should know the most about the dangers of bacterial infection.

The chief of the staff tried different means to increase hygiene. Incentives like gift cards were tried. Then he tried another approach: shame. Doctors had their hands cultured so that they could see the bacteria on their hands. Images of the cultures were even used as a hospital-wide screensaver on the computers.

I listened to this wondering if there are any parallels to formal verification engineers and researchers. Ostensibly, we are the experts on the perils of software bugs. Academic papers often begin with some story about bugs in the wild, motivating the new formal verification technique described therein.

But to what extent do we practice what we preach? How many of us might write a Python script with no unit tests? C/C++ programs without running Lint/[CppCheck](http://sourceforge.net/apps/mediawiki/cppcheck/index.php?title=Main_Page)? Compile without fixing all the `-Wall` warnings the compiler emits?

These kinds of activities represent the lowest rung of software assurance; they're the "hand washing of software assurance", if you will. I'm certainly guilty myself of not always practicing good "software hygiene". The justifications you might give for failing to do so is that you're just writing a program for personal use, or you're writing prototype software, "just for research". I could imagine doctors telling themselves something similar: "I always scrub thoroughly before a major surgery, but it's not such a big deal for a simple office visit." But this justification can become a slippery slope.

There are a few examples in which verification-tool assurance shoots straight for the top. For example, John Harrison [verified a model of HOL Light in HOL Light](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.97.2210). Filip Maríc [verified a SAT solver](http://citeseer.uark.edu:8080/citeseerx/viewdoc/summary;jsessionid=0AC351E5EB733DC0C6F79E56A6E267EA?doi=10.1.1.158.5218). But these are more intellectual curiosities than standard operating procedure.

It could be an interesting empirical study to analyze the software quality of open-source formal verification tools to see just how buggy they are (spoiler alert: they are buggy). What I'm interested in are not deep flaws that might be caught via theorem-proving, but simple ones that could be caught with a little better test coverage or lightweight static analysis.

For now, I'm just happy my colleagues' screensaver isn't a bug tracker for software that I've written.
