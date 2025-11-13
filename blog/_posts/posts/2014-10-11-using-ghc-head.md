---
layout: post
title: "Using GHC HEAD"
date: 2014-10-11
categories: 
  - "haskell"
tags: 
  - "ghc"
---

I wanted to try out a recent feature in [GHC](http://www.haskell.org/ghc/) this week, so I was using HEAD. When I say _using_, I mean it: I wasn't developing with it, but using it to build [Ivory](https://github.com/GaloisInc/ivory), our safe C EDSL, as well as some libraries written in Ivory. I want to point out a few dragons that lay ahead for others using HEAD (7.9.xxx) today.

- The Functor, Applicative, Monad hierarchy (as well as the Alternative and MonadPlus hierarchy) is no longer a warning but an error. You'll be adding a lot of instances to library code.
- You'll need to update Cabal, which comes as a submodule with the GHC sources. Unfortunately, building Cabal was a pain because of the bootstrapping problem. The bootstrap.sh script in cabal-install is broken (e.g., outdated dependency versions). Getting it to work involved using runhaskell directly, modifying a bunch of Cabal's dependencies, and getting a little help from Iavor Diatchki.
- Some of the datatypes in Template Haskell have changed; notably, the datatypes for creating class constraints have been folded into the datatype for constructing types (constraint kinds!). Many libraries that depend on Template Haskell breaks as a result.
- I won't mention the issues with package dependency upper bounds. Oops, I just did.

Fortunately, once Cabal is updated, Cabal sandboxes work just fine. I wrote a [simple shell script](https://gist.github.com/leepike/44b1786a2ffc616efc69) to swap out my sandboxes to switch between GHC versions. (It would be a nice feature if Cabal recognized you are using a different version of GHC than the one the cabal sandbox was originally made and created a new sandbox automatically.)

Because I'm on OSX and use [Homebrew](http://brew.sh/), I tried using it to manage my GHC versions, including those installed with Brew and those built and installed manually. It works great. When building GHC, configure it to install into your Cellar, or wherever you have Brew install packages. So I ran

```
> ./configure --prefix=/usr/local/Cellar/ghc/7.9
```

which makes Brew aware of the new version of GHC, despite being manually installed. After it's installed,

```
> brew switch ghc 7.9
```

takes care of updating all your symbolic links. No more making "my-ghc-7.9" symbolic links or writing custom shell scripts.
