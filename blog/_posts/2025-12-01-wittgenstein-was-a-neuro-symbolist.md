# Wittgenstein was a Neuro-Symbolist

*How Wittgenstein foreshadowed the need and challenges of combining LLMs and automated reasoning.*

Love LLM’s ability to generate novel code, language, etc. but don’t trust they won’t hallucinate? Neither would the philosopher Ludwig Wittgenstein. Even though his writings predate LLMs by a century, his concerns about the usage and ambiguity of natural language are more prescient today than ever. I was struck to reinvestigate Wittgenstein’s writings upon watching a recent talk by Byron Cook in which he discusses neuro-symbolic AI and the [ambiguity of natural language](https://youtu.be/DLtrKfWLut8?si=Uw0zDAOuzIZMjwul&t=3850), in which he discusses the ambiguity of language and the difficulty of soundness when reasoning over natural language.

# Neuro-Symbolic AI

*Neuro-symbolic AI* is the idea of combining probabilistic AI methods, typified these days by large language models, with mathematically rigorous symbolic reasoning. Examples of neuro-symbolic AI in the wild include coding tools from [Imandra](https://www.imandra.ai/) and [Amazon BedRock Guardrails](https://aws.amazon.com/blogs/aws/minimize-ai-hallucinations-and-deliver-up-to-99-verification-accuracy-with-automated-reasoning-checks-now-available/) for checking business logic analysis. Even modern chatbots are often “smart” (i.e., post-trained) enough these days to execute code when needed for exact results. For example, if I ask GPT-5, “What is the mean of the first 10,000 primes?”, it generates Python code to calculate the result, executes the code, and returns the calculated result. Using precise code execution, even when not explicitly requested, is a first step in neuro-symbolic reasoning.

Indeed, neuro-symbolic AI is the reunion of siblings separated at birth. in 1956, *Logic Theorist* was a program that proved 38 of the theorems from Bertrand Russell’s axiomatization of mathematics, laid out in *Principia Mathematica*. Logic Theorist is widely considered to be one of, if not the first AI program.[^1] Besides how incredible it is to think there was an advanced mechanical theorem-prover from the 50s (the 50s\!), this first “AI program” would be considered an Automated Reasoning system today.

# Wittgenstein on Logic and Language

Wittgenstein’s philosophy of language famously evolves through his lifetime. Early in his philosophical thinking, recorded in his *Tractatus Logico-Philosophicus* (TLP), he was critical of natural language’s ability to precisely describe ideas and concepts.

Language disguises the thought; so that from the external form of the clothes one cannot infer the form of the thought they clothe, because the external form of the clothes is constructed with quite another object than to let the form of the body be recognized.[^2]

In the language of everyday life it very often happens that the same word signifies in two different ways and therefore belongs to two different symbols or that two words, which signify in different ways.[^3]

In some ways, the “TLP Wittgenstein” might not be surprised that LLMs work, since they merely discover how language-symbols, represented by in some higher-dimension vector space, relate to other ambiguous language-symbols. There’s no logic or rigor to the symbols that make up natural language, so being able to probabilistically relate them to form strings of ambiguous symbols is quite reasonable. But these are only the clothes hiding any actual thought. He writes further: 

In order to avoid these errors, we must employ a symbolism which excludes them, by not applying the same sign in different symbols and by not applying signs in the same way which signify in different ways. A symbolism, that is to say, which obeys the rules of logical grammar of logical syntax. (The logical symbolism of Frege and Russell is such a language, which, however, does still not exclude all errors.)[^4]

Logic is the only language to  precisely represent thought. TLP Wittgenstein is the cynical formal verificationist who sees no value in LLMs (or natural language, in general) if they do not provide rigor.

But Wittgenstein’s views famously evolve later in his philosophical writings, as can be seen in his *Philosophical Investigations*. There he builds the idea of “language-games”, that the meaning of language comes from context-dependent endeavors:

Here the term "language-game" is meant to bring into prominence the fact that the speaking of language is part of an activity, or of a form of life. Review the multiplicity of language-games in the following examples, and in others:  
Giving orders, and obeying them—  
Describing the appearance of an object, or giving its measurements  
Constructing an object from a description (a drawing)—  
Reporting an event—  
Speculating about an event—[^5]

He rebukes his viewpoint from TLP, that language meaning is a logical picture of the world but rather that language meaning comes from the *usage* of language. Formal logic itself is just another language-game, but still a useful one\! Its rules do not provide metaphysical insight about the world, but provide a structure for games (e.g., about mathematics). But ultimately, language use depends on the messy and ambiguous world of natural language:

The more narrowly we examine actual language, the sharper becomes the conflict between it and our requirement. (For the crystalline purity of logic was, of course, not a result of investigation: it was a requirement.) The conflict becomes intolerable; the requirement is now in danger of becoming empty.—We have got on to slippery ice where there is no friction and so in a certain sense the conditions are ideal, but also, just because of that, we are unable to walk. We want to walk: so we need friction. Back to the rough ground\![^6]

Within the *Philosophical Investigations*, Wittgenstein anticipates the neuro-symbolic approach. Pure logical reasoning cannot live on its own, but for certain language-games (mathematics, software verification, business-critical logic, etc.), logic provides the structure.

At the end of the day, mathematical proof is a social convention in which I need to convince you that the symbols and axioms represent something in the world, and that convincing is by natural language.  


[^1]:  Russell, Stuart J.; Norvig, Peter. (2021). *Artificial Intelligence: A Modern Approach*. Pg. 17\.

[^2]:  Sec. 4.002, Tractatus Logico-Philosophicus, translated by C. K. OgdenTractatus Logico-Philosophicus.

[^3]:  Sec. 3.323, Tractatus Logico-Philosophicus, translated by C. K. Ogden.

[^4]:  Sec. 3.325, Tractatus Logico-Philosophicus, translated by C. K. Ogden.

[^5]:  Sec. 23, Philosophical Investigations, translated by G. E. M. Anscombe.

[^6]:  Sec. 107, Philosophical Investigations, translated by G. E. M. Anscombe.