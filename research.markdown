---
layout: page
permalink: /research/
title: Research
---

My research is based on idea of "*Computational Trinity*" which is a profound insight dating back to the '70s that `Computer Science`, `Intuitionistic Logic` and `Category Theory`  are essentially "three sides" of the same coin.

Consider a terminating functional program $$f$$ that takes an input of type $$A$$ and produces an output of type $$B$$

$$f : A \to B$$ 

This program can be also viewed as a **proof** that $$A$$ implies $$B$$ and as a morphism between the objects $$A$$ and $$B$$. Thus total functional programming, intuitionistic logic and category theory 
are three different mathematical subjects that describe the same idea. 

While these are interesting subjects on their own, they also have **practical applications** in `structuring functional programs`, `formal verification` and in the `design` of the next generation of programming languages.

As of today, `Haskell` is probably one of the most successful examples of how Category Theory influenced the design of a programming language and how it is continuing to inspire new programming techniques, e.g `Recursion Schemes`, to structure programs even further thus ensuring these have well-understood safety properties.

# Denotational Semantics
The intuition that *programs* should be regarded as *functions* and *types* should be regarded as "*mathematical objects*" is due to Dana Scott. 

This is formalised via a *denotational interpretation* which is a *compositional* interpretation function of the syntax of a programming language $$\mu \Sigma$$ into a mathematical object called a *semantic domain* $$\mathcal{D}$$ 

$$[\![\cdot ]\!] : \mu \Sigma \to \mathcal{D}$$

This function is furthermore characterised by two additional properties. The first is called `Soundness` and it states that the interpretation has to be agnostic to step reductions. The second is called `Computational Adequacy`  which states that if two programs are equated then they are observationally indistinguishable. Furthermore, a semantics that equates exactly the programs that are observationally indistinguishable is called `Fully Abstract`. 

The theory was developed by Christopher Strachey and Dana Scott between 1969 and 1971. The best introduction to these topics is probably [Thomas Streicher's book](https://www.amazon.co.uk/Domain-Theoretic-Foundations-Functional-Programming-Streicher/dp/9812701427).


# Recursion Schemes 
A recursion scheme is a structuring device that ensures that recursive definitions are well-defined. 

For an inductive data type, e.g. the lists over a type `a`, the `foldr` function takes a base case of type `b` 
and the inductive step of the recursion and returns a function from `[a] -> b`. 

{% highlight haskell %}
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr ind base []     = base
foldr ind base (x:xs) = ind x (foldr ind base xs) 
{% endhighlight %}

In Haskell, assuming the user only feeds well-defined functions to the `foldr` then `foldr` itself is well-defined.

Category theory generalises this concept to any inductive data type.
In particular, a `fold` function is in the *unique extension* of an algebra for a functor $$F$$ .

Haskell is particularly good at dealing with these abstract definitions. In fact, we can use type classes and recursive types to implement a `fold` for any functor $$F$$ (that has a least fixed-point)

{% highlight haskell %}
{-# LANGUAGE DeriveFunctor #-}

import Data.Functor

data Fix f = In {inOp :: f (Fix f)}

fold :: (Functor f) => (f b -> b) -> Fix f -> b
fold alg = alg . fmap (fold alg) . inOp
{% endhighlight %}

However, not every well-defined function can be defined in terms 
of `fold`s (or `unfolds`) directly, but it has been shown that every recursion 
scheme that has been observed is an instance of an [adjoint (un)fold](https://research-information.bris.ac.uk/ws/portalfiles/portal/65842535/Nicolas_Wu_Unifying_Structured_Recursion_Schemes.pdf). 

# Guarded recursion 
Guarded recursion is a form or recursion where the recursive call is guarded by a 
modality $$\blacktriangleright$$ called the "*later*" operator. 

This is useful for checking productivity of definitions and solve recursive domain 
equations that are in general hard (or impossible) to prove well-defined. 

The later modality is essentially an abstract device to talk about step-indexed definitions. The idea of step-indexing is to break the circularity of definitions by using a natural number that allows us to get a handle of recursion. 

The easiest example of the use of guarded recursion is the guarded streams defined as the *unique* solution to the following equation

$$ \text{Str}^{g} A = A \times \blacktriangleright \text{Str}^{g} A$$

The intuitive meaning of this type is that while the head of the stream is available now, 
the tail of the stream is available only after one step of computation. 
The end result is that productivity for elements of $$\text{Str}^{g}$$ is guaranteed by the type system. 
