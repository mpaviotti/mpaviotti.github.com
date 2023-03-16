---
layout: post
title:  "CCCs are not complete models of STLC" 
date:   2023-03-16 13:14:21 +0000
categories: categories semantics 
---

Now that I have caught your attention with a completely outrageous title I will
explain what I mean by this.  

Assume $$\Lambda_X$$ is the set of closed well-typed STLC (Simply Typed $$\lambda$$-calculus) terms.
Clearly, STLC can be interpreted into any Cartesian Closed category (CCC) by defining an interpretation function 
$$[\![\cdot]\!] : \Lambda_X \to \mathcal{C}$$ such that for any term $$t \in \Lambda_X$$ , $$[\![t]\!] \in \mathcal{C}(1, [\![\sigma]\!])$$ where $$\sigma$$ is the type of $$t$$. Moreover, it can be proved that the interpretation function is sound and complete.  The completeness statement reads as follows. For all terms $$t_1$$ and $$t_2$$,

$$t_1 \equiv_{\beta\eta} t_2 \text{ iff } [\![t_1]\!] = [\![t_2]\!] $$

where the $$(\Rightarrow)$$ direction is soundness whereas $$(\Leftarrow)$$ is completeness of the interpretation.

This statement is certainly true. If two terms are $$\beta\eta$$ equivalent they are equal in the model, i.e. the semantics is agnostic to $$\beta\eta$$-step reductions. Conversely, all equations that hold for any two STLC-denotable terms also hold in the syntax. 

However, $$CCC$$ categories are not *complete models* in the following sense. A model is complete if the following statement holds 

$$t_1 =_{\beta\eta} t_2 \text{ iff for all } [\![ \cdot ]\!] : \Lambda_X \to \mathcal{C}, [\![t_1]\!] = [\![t_2]\!] $$

This statement is not true for CCCs. The counter example is given by the preorder category $$\mathcal{P}$$ with CCC structure. The preorder the category has at most one morphism ($$\sqsubset$$) between objects. If this category has the greatest element $$\top$$, binary meets ($$\wedge$$) and Heyting implications ($$\to$$) the it is CCC. 

The counterexample is constructed by considering the projection maps out of the product. In fact,  in $$\mathcal{P}$$ the maps $$x \wedge x \xrightarrow{\pi_1} x$$ and $$x \wedge x \xrightarrow{\pi_2} x$$ are the same map, i.e. $$\pi_1 = \pi_2$$. However, clearly the projections $$\pi_1$$ and $$\pi_2$$ in the syntax are not $$\beta\eta$$-equivalent. 

I will defer the reader to the [original paper](https://link.springer.com/chapter/10.1007/BFb0014068) for more details.

