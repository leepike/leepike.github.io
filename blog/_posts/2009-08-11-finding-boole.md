---
layout: post
title: "Finding Boole"
date: 2009-08-11
categories: 
  - "hardware"
  - "verification"
tags: 
  - "model-checking"
  - "sal"
---

Here's a simple challenge-problem for model-checking Boolean functions: Suppose you want to compute some Boolean function \\(spec :: B^k \rightarrow B\\), where \\(B^k\\) represents 0 or more Boolean arguments.

Let \\(f\_0\\), \\(f\_1\\), \\(\ldots\\), \\(f\_j\\) range over 2-ary Boolean functions, (of type \\((Bool, Bool) \rightarrow Bool\\)), and suppose that \\(f\\) is a fixed composition of \\(f\_0\\), \\(f\_1\\), \\(\ldots\\), \\(f\_j\\). (By the way, I'm going to talk about functions, but you can think of these as combinatorial circuits, if you prefer.)

Our question is, "Do there exist instantiations of \\(f\_0\\), \\(f\_1\\), \\(\ldots\\), \\(f\_j\\) such that for all inputs, \\(f = spec\\)?

What is interesting to me is that our question is quantified and of the form, "exists a forall b ...", and it is "higher-order" insofar as we want to find whether there exist satisfying functions. That said, the property is easy to encode as a model-checking problem. Here, I'll encode it into [SRI's Symbolic Analysis Laboratory](http://sal.csl.sri.com/) (SAL) using its BDD engine.  

To code the problem in SAL, we'll first define for convenience a shorthand for the built-in type, BOOLEAN:

```
B: TYPE = BOOLEAN;
```

And we'll define an enumerated data type representing the 16 possible 2-ary Boolean functions:

```
B2ARY: TYPE = { False, Nor, NorNot, NotA, AndNot, NotB, Xor, Nand
              , And, Eqv, B, NandNot, A, OrNot, Or, True};
```

Now we need an application function that takes an element f from B2ARY and two Boolean arguments, and depending on f, applies the appropriate 2-ary Boolean function:

```
app(f: B2ARY, a: B, b: B): B =
  IF    f = False   THEN FALSE
  ELSIF f = Nor     THEN NOT (a OR b)
  ELSIF f = NorNot  THEN NOT a AND b
  ELSIF f = NotA    THEN NOT a
  ELSIF f = AndNot  THEN a AND NOT b
  ELSIF f = NotB    THEN NOT b
  ELSIF f = Xor     THEN a XOR b
  ELSIF f = Nand    THEN NOT (a AND b)
  ELSIF f = And     THEN a AND b
  ELSIF f = Eqv     THEN NOT (a XOR b)
  ELSIF f = B       THEN b
  ELSIF f = NandNot THEN NOT a OR b
  ELSIF f = A       THEN a
  ELSIF f = OrNot   THEN a OR NOT b
  ELSIF f = Or      THEN a OR b
  ELSE                   TRUE
  ENDIF;
```

Let's give a concrete definition to f and say that it is the composition of five 2-ary Boolean functions, f0 through f4. In the language of SAL:

```
f(b0: B, b1: B, b2: B, b3: B, b4: B, b5: B):
  [[B2ARY, B2ARY, B2ARY, B2ARY, B2ARY] -> B] =
    LAMBDA (f0: B2ARY, f1: B2ARY, f2: B2ARY, f3: B2ARY, f4: B2ARY):
      app(f0, app(f1, app(f2, b0,
                              app(f3, app(f4, b1, b2),
                                      b3)),
                      b4),
              b5);
```

Now let's define the spec function that f should implement:

```
spec(b0: B, b1: B, b2: B, b3: B, b4: B, b5: B): B =
  (b0 AND b1) OR (b2 AND b3) OR (b4 AND b5);
```

Now, we'll define a module m; modules are SAL's building blocks for defining state machines. However, in our case, we won't define an actual state machine since we're only modeling function composition (or combinatorial circuits). The module has variables corresponding the function inputs, function identifiers, and a Boolean stating whether f is equivalent to its specification (we'll label the classes of variables INPUT, LOCAL, and OUTPUT, to distinguish them, but for our purposes, the distinction doesn't matter).

```
m: MODULE =
BEGIN
  INPUT b0, b1, b2, b3, b4, b5 : B
  LOCAL f0, f1, f2, f3, f4 : B2ARY
  OUTPUT equiv : B

  DEFINITION
    equiv = FORALL (b0: B, b1: B, b2: B, b3: B, b4: B, b5: B):
              spec(b0, b1, b2, b3, b4, b5)
            = f(b0, b1, b2, b3, b4, b5)(f0, f1, f2, f3, f4);
END;
```

Notice we've universally quantified the free variables in spec and the definition of f.

Finally, all we have to do is state the following theorem:

```
instance : THEOREM m |- NOT equiv;
```

Asking whether equiv is false in module m. Issuing

```
$ sal-smc FindingBoole.sal instance
```

asks SAL's BDD-based model-checker to solve theorem instance. In a couple of seconds, SAL says the theorem is proved. So spec can't be implemented by f, for any instantiation of f0 through f4! OK, what about

```
spec(b0: B, b1: B, b2: B, b3: B, b4: B, b5: B): B =
  TRUE;
```

Issuing

```
$ sal-smc FindingBoole.sal instance
```

we get a counterexample this time:

```
f0 = True
f1 = NandNot
f2 = NorNot
f3 = And
f4 = Xor
```

which is an assignment to the function symbols. Obviously, to compute the constant TRUE, only the outermost function, f0, matters, and as we see, it is defined to be TRUE.

By the way, the purpose of defining the enumerated type B2ARY should be clear now---if we hadn't, SAL would just return a mess in which the value of each function f0 through f4 is enumerated:

```
f0(false, false) = true
f0(true, false) = true
f0(false, true) = true
f0(true, true) = true
f1(false, false) = true
f1(true, false) = true
f1(false, true) = false
f1(true, true) = true
f2(false, false) = false
f2(true, false) = true
f2(false, true) = false
f2(true, true) = false
f3(false, false) = false
f3(true, false) = false
f3(false, true) = false
f3(true, true) = true
f4(false, false) = false
f4(true, false) = true
f4(false, true) = true
f4(true, true) = false
```

OK, let's conclude with one more spec:

```
spec(b0: B, b1: B, b2: B, b3: B, b4: B, b5: B): B =
  (NOT (b0 AND ((b1 OR b2) XOR b3)) AND b4) XOR b5;
```

This is implementable by f, and SAL returns

```
f0 = Eqv
f1 = OrNot
f2 = And
f3 = Eqv
f4 = Nor
```

Although these assignments compute the same function, they differ from those in our specification. Just to double-check, we can ask SAL if they're equivalent:

```
spec1(b0: B, b1: B, b2: B, b3: B, b4: B, b5: B): B =
  ((b0 AND ((NOT (b1 OR b2))  b3)) OR NOT b4)  b5;
```

specifies the assignments returned, and

```
eq: THEOREM m |- spec(b0, b1, b2, b3, b4, b5) =  spec1(b0, b1, b2, b3, b4, b5);
```

asks if the two specifications are equivalent. They are.
