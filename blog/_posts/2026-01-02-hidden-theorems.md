---
layout: post
title: "Hidden Theorems"
date: 2026-01-02
---

*The trove of formal proof training data math-AI companies are missing out on.*

Companies like Axiom, Harmonic AI, and Google DeepMind are combining LLMs and formal proof to advance mathematical reasoning. Their tools are rapidly progressing from scoring well on the Math Olympiad to Putnam Exam to making contributions to [open mathematical problems](https://terrytao.wordpress.com/2025/12/08/the-story-of-erdos-problem-126/).

The primary focus of these efforts today is on pure math proofs in Lean. These go hand-in-hand: Lean is a leading (if not the leading) proof assistant today, and it particularly has a robust mathematics library ([mathlib](https://leanprover-community.github.io/mathlib-overview.html)).

But there are decades of development in other proof assistants such as ACL2, Isabelle/HOL, and Rocq (Coq). In particular, these assistants have been used to verify complex systems like compilers and microkernels, beyond pure mathematics.

This blog post argues that math-AI systems that focus on Lean miss out on **most** formal proof training data since it sits in other proof assistants. I don’t know anything about the models these companies use, other than what has been made public. Public documentation I have seen does not mention leveraging training data from other proof assistants.[^1]

# Where the Wild Theorems Are

For a back-of-the-envelope analysis for how many theorems and proofs are available in other proof assistants, I’m focusing on publicly-available proofs (as opposed to closed-source industrial proofs). I’ll be making a bunch of assumptions, but just trying to get an order-of-magnitude evaluation. I searched GitHub for files with theorem prover file extensions that contain a theorem identifier. The following table summarizes the proof assistants searched for, the search strings, and the resulting number of files found.

| Prover | Search term | Number of files |
| :---- | :---- | :---- |
| ACL2 | path:\*.lisp AND defthm | 21.9k |
| Agda | path:\*.agda | 75.8k |
| Rocq (Coq) | path:\*.v AND (Theorem OR Lemma OR Fact OR Remark OR Corollary) | 339k |
| Isabelle/HOL/HOL Light | (path:\*.thy OR path:\*.hl OR path:\*.ml) AND (set\_goal OR prove OR theorem OR lemma OR proposition OR thm) | 183k |
| PVS | path:\*.pvs AND (THEOREM OR LEMMA OR CLAIM OR PROPOSITION) | 4.1k |


So we get a total of \~**624k files**.

We can do the same for Lean as well. Searching GitHub for Lean files with proofs using path:\*.lean AND (theorem OR lemma OR example), we get **251k files**.

On the face of it, this means **over 70%** of publicly available files containing proof scripts are in proof assistants other than Lean, which is likely approximately correct.

There are several caveats to this figure. The number of theorems per file can vary widely between projects and proof assistants. There are a lot of proofs not on GitHub. Focusing on GitHub likely overcounts Lean, which is a modern prover with a strong GitHub-based community for mathlib. It likely undercounts other proof assistants with legacies that predate GitHub’s launch (2008); Isabelle/HOL was born in the 80s\!

Also, this estimate does not take into account the number of theorems that are incomplete, duplicative, etc. Still it is safe to estimate that at least 50% of publicly available theorems are in assistants other than Lean.

These are astonishing figures from a couple of perspectives. First, there’s a lot of formalized mathematics out there\! It’s amazing to see hundreds of thousands of files in what was considered a niche sub-domain of Computer Science. Second, if these estimates are approximately true, it represents the incredible growth of Lean. Lean3, the first stable version, was launched in 2017\. Proof assistants like Isabelle/HOL and ACL2 date back to the 80s\! Lean is the newest of the proof assistants listed.

Beyond a quantitative comparison, the qualitative comparison is perhaps more important. While Lean has incredible uptake given its youth, it has particularly been taken up in the pure mathematics community. Recent efforts like [CSLib](https://www.cslib.io/) that formalize Computer Science concepts in Lean broaden its applicability. Still, there are deep domain-specific specifications and proofs found in other proof assistants. In contrast, there are deep domain-specific specifications and proofs found in other proof assistants. For just a few examples, consider the following:

* [SeL4 microkernel proofs](https://github.com/seL4/l4v) in Isabelle/HOL.
* [Aircraft collision avoidance proofs](https://github.com/nasa/pvslib/blob/master/ACCoRD/README.md) in PVS.
* [Java Virtual Machine (JVM) proofs](https://github.com/acl2/acl2/tree/master/books/kestrel/axe/jvm) in ACL2.
* [Compiler (CompCert) proofs](https://compcert.org/) in Rocq (Coq).

This is just a brief list of years (or engineer-centuries) of thought that have gone into building formal models in other assistants. Training on non-Lean proofs could improve math-AI’s ability to reason about stateful systems, refinements, and program invariants. These are the kinds of domains that will help math-AI become relevant to software and hardware verification, in addition to moving forward pure math research.

# Getting to Lean

One approach to get this training data from other proof assistants is to somehow convert proofs in other assistants into Lean proofs. Then math-AI systems can continue to focus on one assistant, Lean, for proof generation and as an oracle for correctness.

Translating between proof assistants is notoriously difficult. Proof assistants have different logics, type systems, and proof calculi. They can contain complex proof rules, the behavior of which is opaque.

But this kind of translation is exactly what LLMs are good at\! Indeed, there have been several exciting LLM-based proof-assistant translation efforts in 2025:

[ProofGym](https://openreview.net/pdf/a7072c4403a1e6e1c320e0ac1fd87dc032664d23.pdf?utm_source=chatgpt.com) (2025) unifies LLM interactions across proof assistants such as Isabelle, Coq, and Lean. It also provides a unified and optimized API for interacting with proof assistants that improves batch verification. ProofGym provides a promising basis for leveraging proofs across proof assistants. The authors state in the last paragraph of the paper:

> Building on this platform, our ongoing work aims to develop a multilingual proof synthesis pipeline. We have already generated a corpus of 150k parallel informal-formal statements across the three languages (50k each) and are training a 7B model for statement formalization and cross-system translation. The goal is to produce aligned 〈statement, proof〉triplets across all three systems, enabling large-scale supervised fine-tuning of a unified, multilingual theorem prover.

[Babel-formal](https://openreview.net/pdf?id=WBI1PL0hZ2) (2025) investigates using LLMs with proof terms to help translate between Rocq and Lean.

The [MiniF2F repo](https://github.com/openai/miniF2F/tree/main) is an abandoned OpenAI project to formalize problems from Math Olympiads across HOL Light, Isabelle, Lean, and Metamath. However, in 2025, researchers were able to [automatically translate 98% of MiniF2F theorems into Rocq](https://arxiv.org/pdf/2503.04763) using LLM-based techniques. (Note this work translates theorems only, not their associated proofs.)

Building on these efforts, math-AI, and more generally, neurosymbolic AI, can become more powerful, accurate, and more relevant to real-world verification problems. The data to improve math-AI exists, waiting to be unlocked.

[^1]:  Although some systems leverage multiple proof assistants. For example, in 2024, Google Deepmind [used a Lean-based solver as well as a specialized geometry solver](https://deepmind.google/blog/ai-solves-imo-problems-at-silver-medal-level/) to tackle the Math Olympiad.
