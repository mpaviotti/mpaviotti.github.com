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

Here the word *trivial* means that every object $$A$$ in the category is isomorphic to the terminal object $$1$$. 

To do this proof we make use of the fixed-point operator, which exists at all types. 

We know that for all endomaps $$f : A \to A$$ in the category there exists a map
$$\text{fix}_{f} : 1 \to A$$ such that $$f \circ \text{fix}_{f} =
\text{fix}_{f}$$. Thus, we can use the unique endomap on the initial object,
namely the identity map $$id_{0}: 0 \to 0$$, to get a map $$\text{fix}_{id_{0}} :
1 \to 0$$. But now, because $$0$$ is initial (and $$1$$ is terminal), we also
have a unique map into the terminal object, namely $$! : 0 \to 1$$. It is easy
to see that $$\text{fix}_{id_{0}}$$ and $$1$$ are inverses to each other, hence
they form an isomorphism $$0 \cong 1$$.
In particular, $$\text{fix}_{id_{0}} \circ ! : 0 \to 0$$ is $$id_{0}$$ by initiality and
$$! \circ \text{fix}_{id_{0}} : 1 \to 1$$ is $$id_{1}$$ by finality. 

Now we compute as follows. For every object $$A$$ in the category  $$ 1 \cong 0
\cong 0 \times A \cong 1 \times A \cong A $$ and the proof is concluded. 

This result was shown to hold also when in the case when instead of the
initial object we postulate a natural numbers object $$\mathbb{N}$$.

A natural question to ask now is: 

> is every model of PCF trivial? 

To answer this question we take as a model of PCF the category of [Scott domains](https://en.wikipedia.org/wiki/Scott_domain).
This category consists of pointed directed complete partial orders
(dCPPO) as objects and continuous functions as arrows (just following [Thomas Streicher's
book](https://www.amazon.co.uk/Domain-Theoretic-Foundations-Functional-Programming-Streicher/dp/9812701427) to avoid any misunderstanding).

Now, we would like to prove that this category is cartesian closed (which we know), has a fixed-point map (which it has) 
and that it has an initial object. However,  

> there is no initial object in the category of Scott domains

This is because if this category had an initial element $$0$$ it would have at least a bottom
element $$\bot_0$$. Notice that the subset $$\{\bot_0\}$$ is indeed directed and
its suprema $$\bigsqcup \{\bot_0\}$$ is $$\bot_0$$ itself. Now if we take any
other dCPPO $$X$$, a continuous function $$f : 0 \to X$$ that maps $$\bot_{0}$$ to any element $$x \in X$$ 
will satisfy the equation

$$f \bigsqcup \{\bot_0\} = \bigsqcup f \{\bot_0\} $$

because, for any $$x \in X$$ we choose for $$f(\bot_0)$$ (even the bottom element), $$\bigsqcup f \{\bot_0\} = \bigsqcup \{x\} = x$$. 

The only way this category had an initial element is if the arrows in the
category were *strict*, namely they preserved $$\bot$$ elements, but, as we have
seen, continuous functions do not necessarily preserve it.

> Is this just a coincidence that Scott's model is not trivial? 

Not really. Because if it was trivial it would break computational adequacy which is the statement
that for every pair or well-typed terms in the language $$\Gamma \vdash t : A$$
and $$\Gamma \vdash t' : A$$

if $$[\![ t ]\!] = [\![ t' ]\!] $$ then $$t \approx t'$$

where $$\approx$$ is contextual equivalence of programs. 

But if the models was trivial then all the pairs of PCF-denotable terms (pairs
of maps into something isomorphic to $$1$$) would be equal (by finality) and
therefore operationally equivalent.  

### What does this all mean for the Haskell programmer?

Well nothing, because Haskell does not have a formal model.

But let's say we make a big leap and take the fragment of Haskell consisting of
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

Is this a problem? No, this is not a problem because `y id` is the infinite
computation. In other words, sends the unit element to $$\bot$$. But since
Haskell functions need not to be strict, I can send the $$\bot$$ element in
`Empty` to `One ()`. So this map is not an isomorphism.

### Conclusions
This is probably a very convoluted way of saying 
> There is no initial object (or natural numbers object) in PCF (or other "PCF-like" languages like Haskell)

this is because `Empty` actually contains the bottom element $$\bot$$. 
For the same reasons, if we now consider System F with a polymorphic fixed-point operator and define the $$0$$ object by setting 

$$ 0 = \forall x . x$$ 

This object has actually an inhabitant: the non-terminating computation. Thus, it is not the initial object.
