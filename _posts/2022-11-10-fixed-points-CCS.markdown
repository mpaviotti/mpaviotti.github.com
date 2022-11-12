---
layout: post
title:  "Inconsistencies in Cartesian Closed Categories with fixed-points" 
date:   2022-11-10 13:14:21 +0000
categories: categories recursion
---
Yesterday I had yet another interesting conversation with [Zhixuan
Yang](https://yangzhixuan.github.io) where I pointed out that there is a very
nice [paper](https://www.sciencedirect.com/science/article/pii/030439759090165E)
paper stating that 

> Any Cartesian Closed Category (CCC) with an initial object and a fixed-point operator is trivial. 

This involves showing that every object $$A$$ is isomorphic to the terminal object $$1$$. 

We use the fixed-point operator which exists at all types. 

We know that for all endomaps $$f : A \to A$$ in the category there exists a map
$$\text{fix}_{f} : 1 \to A$$ such that $$f \circ \text{fix}_{f} =
\text{fix}_{f}$$. Thus, we can use the unique endomap on the initial object $$i:
0 \to 0$$ to get a map $$\text{fix}_{i} : 1 \to 0$$. By initiality, we also have
a unique map $$! : 0 \to 1 $$ which forms an isomorphism $$0 \cong 1$$.

Now we compute as follows. For every object $$A$$ in the category  $$ 1 \cong 0
\cong 0 \times A \cong 1 \times A \cong A $$ 

In the paper, the authors show that this is also the case when instead of the
initial object we postulate a natural numbers object $$\mathbb{N}$$.

A natural question to ask now is: wait a minute, 

> is every model of PCF trivial? 

The answer to this question (i think) is **negative** for the following reason: 

> there is no initial object in the category of Scott domains

The category of Scott domains consists of pointed directed complete partial orders
(dCPPO) as objects and continuous functions as arrows (just following [Thomas Streicher's
book](https://www.amazon.co.uk/Domain-Theoretic-Foundations-Functional-Programming-Streicher/dp/9812701427) to avoid any misunderstanding).

Now the only way this category has an initial element is if the arrows in the
category are *strict*, namely they preserve $$\bot$$ elements. But continuous
functions need not to be strict. 

If this category had an initial element $$0$$ it would have at least a bottom
element $$\bot_0$$. Notice that the subset $$\{\bot_0\}$$ is indeed directed and
its suprema $$\bigsqcup \{\bot_0\}$$ is $$\bot_0$$ itself. Now if we take any
other dCPPO $$X$$, any continuous function $$f : 0 \to X$$ will map $$\bot_0$$
to some $$x \in X$$ and the suprema would be $$\bigsqcup f(0) = \bigsqcup \{x\} =
x$$. 

> Is this just a coincidence that Scott's model is not trivial? Not really. 

If it was trivial it would break computational adequacy which is the statement
that for every pair or well-typed terms in the language $$\Gamma \vdash t : A$$
and $$\Gamma \vdash t' : A$$

if $$[\![ t ]\!] = [\![ t' ]\!] $$ then $$t \approx t'$$

where $$\approx$$ is contextual equivalence of programs. 

This means that if the model is trivial then every pair of PCF-denotable terms
would be equal and therefore operationally equivalent. 

### What does this all mean for the Haskell programmer?

Nothing, because Haskell does not have a formal model.

But let's say we make a big leap and took the fragment of Haskell consisting of
"inductive data types" and recursion. Now I can craft a program that resembles
what I just said above

{% highlight haskell %}
{-# LANGUAGE GADTs #-}

data Empty where

data Unit = One ()

y :: (a -> a) -> a
y f = f (y f)

empty :: Empty -> Empty
empty x = x

(===) :: a -> a -> a
x === y

endoEmpty :: Unit -> Empty
endoEmpty = y id === id (y id) -- by Fixed-point property y f = f (y f)

{% endhighlight %}

Is this a problem ? No, this is not a problem because `y id` is the infinite
computation. In other words, sends the unit element to $$\bot$$. But since
Haskell functions need not to be strict, I can send the $$\bot$$ element in
`Empty` to `One ()`. So this map is not an isomorphism.

### Conclusions
This is probably a very convoluted way of saying 
> There is no initial object (or natural numbers object) in PCF (or other "PCF-like" languages like Haskell)

this is because `Empty` actually contains the bottom element $$\bot$$. 
For the same reasons, if we know consider System F with a polymorphic fixed-point operator and define the $$0$$ object by setting 

$$ 0 = \forall x . x$$ 

This object has actually an inhabitant: the non-terminating computation. Thus, it is not the intial object.   

But if you instead think this is just me rambling about.. well you're probably right. 
