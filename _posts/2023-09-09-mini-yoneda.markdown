---
layout: post
title:  "The mini Yoneda lemma for Type Theorists" 
date:   2023-09-09 13:14:21 +0000
categories: semantics categories
---

Disclaimer: I took this from a lecture by Roy Crole at the MGS 2018 which I thought it was great!

Say that you want to do denotational semantics for a simply typed $$\lambda$$-calculus with a unary constructor $$\textsf{R}$$ which has the following typing rule

$$
  \frac{\Gamma \vdash t : A}{\Gamma \vdash \textsf{R}(t) : B}
$$

The task is to give a semantic interpretation $$[\![ \cdot ]\!]$$ for the language by
induction on the typing judgment $$\Gamma \vdash t : A$$ such that terms are
interpreted as morphisms $$[\![\Gamma ]\!] \xrightarrow{[\![ t ]\!]} [\![ A ]\!]$$, assuming
for course $$[\![ \cdot ]\!]$$ is also defined separately for contexts and types.

We interpret the rule above we do induction
on the typing judgment. Thus we assume there exists a morphism
$$[\![ \Gamma ]\!] \xrightarrow{[\![ t ]\!]} [\![ A ]\!]$$ and we construct a morphism
$$[\![ \Gamma ]\!] \xrightarrow{[\![ \textsf{R} ]\!] } [\![ B ]\!]$$. 

For simplicity we remove the semantics brackets, for example,
assuming $$A$$ be interpretation of $$[\![ A ]\!]$$, $$t : \Gamma \to A$$ the
interpretation of $$t$$ an so on.

In this situation it can be quite tricky sometimes to figure out what this
morphism should be since there is some plumming needed to pass around the
context. A particular instantiation of the Yoneda lemma states that given a
morphism $$t : \Gamma \xrightarrow{t} A$$ and a morphism $$R : A \to B$$ there is a
canonical way to construct a morphism $$\Gamma \xrightarrow{R(t)} B$$.

To show this we instantiate the contravariant Yoneda lemma
by setting $$F = \mathbb{C}(-, B)$$. Then for all objects $$A : \mathbb{C}^{\text{op}}$$ we have

$$
  \mathbb{C}(A, B) \cong \mathbb{C}(-, A) \xrightarrow{\cdot} \mathbb{C}(-, B)
$$

Let $$R : A \to B$$ be the interpretation of $$\textsf{R}$$ then, one side of the
isomorphism is $$\phi (\textsf{R},t) = F(t)(\textsf{R}) = \mathbb{C}(t, B)(\textsf{R})$$.  In
other words, the interpretation of $$\textsf{R}(t)$$ is simply $$\textsf{R} \circ t$$.
