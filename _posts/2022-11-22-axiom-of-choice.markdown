---
layout: post
title:  "The Axiom of Choice: An easy explanation." 
date:   2022-11-25 13:14:21 +0000
categories: foundations types
---

The Axiom of Choice (AC) is a controversial axiom in set theory that states that the product of a family of non-empty sets is itself non-empty. 
 
First off, I do not consider myself an expert on set theory, but after having this kind  of conversation with mathematicians and computer scientists I found *there are* some
misconceptions around this axiom and the reasons why it is needed. 

For example, as you will see, it is indeed true that the axiom of choice is connected with the existential quantifier, it is not true, however, that we cannot pick an element out of the existential because the logic is classical.  

In my mind there are two problems: the first is that  

> the existential quantifier does not ensure there exists **one** element with a particular property in the domain of discourse

and the second is that 

> we would need to create a infinite proof that uses Existential Instantiation for each element of the indexing set   

However, in order to fully understand what is going on we need to be more precise. 
So first let's begin with what is the axiom of choice.

### The axiom of choice (AC)
The original formulation of the AC is the following. 

> Given a set $$X$$ and a family of non-empty sets $$\{A_x\}_{x \in X}$$ over
> $$X$$, the infinite product of these sets, namely  $$\Pi_{x \in X}. A_{x}$$ is
> non-empty

For the record, the infinite product is defined as follows

$$\Pi_{x \in X}. A_{x} = \{ f : X \to \bigcup_{x \in X} A_{x} \mid f(x) = A_{x} \}$$ 

However, this statement is a little bit more packed than we would like it to be. An equivalent statement is skolemization. 

### Skolemization (Sk)
Skolemization is what allows one to turn an existentially quantified formula into a function. Formally, skolemization is the following statement  

> Given a relation $$R \subseteq X \times Y$$, $$\forall x \in X. \exists y \in Y. R(x,y)$$ then $$\exists f \in X \to Y. \forall x \in X. R (x, f(x))$$ 

The AC is equivalent to Skolemization. A full discussion of this fact can be
found in
[here](https://mathoverflow.net/questions/191010/when-does-skolemization-require-the-axiom-of-choice) 

For proving that Sk $$\Rightarrow$$ AC, for a family of sets $$\{A_{x}\}_{x \in
X}$$, we define a relation $$R(x,y) = y \in A_{x}$$.  For the other direction we
assume a relation $$R \subseteq X \times Y$$ and then we construct the family of
sets $$\{A_{x}\}_{x \in X}$$ such that each $$A_{x} = \{ y \mid y \in Y \text{
and } R(x,y)\}$$. 

### The existential 
Set theory is a first-order logic together with a set of axioms (9 of them
exactly including the AC) postulating the existence of certain sets. Besides the
propositional fragment of first-order logic there is also the predicate fragment
formed by *universal quantification* ($$\forall$$) and *existential
quantification* ($$\exists$$). 

The Existential Instantiation rule states that if we know there exists an $$x$$
that satisfies the property $$P$$ and we can construct a proof from a fresh
$$t$$ that satisfies that property to a proposition $$R$$ then we can obtain
$$R$$

$$\frac{\exists x. P \qquad t, P[t/x]\cdots R }{R}$$

with $$t$$ free for $$x$$ in $$P$$.

So here we have to treat $$t$$ carefully in that it is a fresh $$t$$ that
satisfies $$P$$, but "we do not know what it is!". 

The reason why I put this sentence in quotes is because this is the explanation
that many people would use. However, to me the real reason is that *we do not
know how many other elements in the universe exist with such a property*. There
is certainly one, but there may be more. 

### The problem with producing a choice function  

To prove Sk we have to assume $$\forall x \in X. \exists y \in Y. R(x, y)$$ and
prove $$\exists f : X \to Y. \forall x \in X. R (x , f (x))$$ 
Here though $$f : X \to Y$$ really means a relation $$f \subseteq X
\times Y$$ such that it is a function, *i.e.* that for all $$x \in X$$ there exists only one $$y \in Y$$ such that $$(x,y) \in f$$. 

Now first we try to construct this relation $$f$$. The naive way I would do this
is by using the axiom of comprehension and construct a set of all pairs such
that the first component is in $$X$$ and the second is in $$Y$$ s.t. this $$y$$
exists such that $$R(x,y)$$

$$f = \{(x, y) \mid x \in X \wedge y \in Y \wedge R(x, y)\}$$

I do not know whether there are other ways to do this. However, before we go on
proving that for all $$x \in X$$ we have $$R(x, f(x))$$ we have to prove that
$$f$$ is a function. In particular, that for each $$x \in X$$ there we have a
**unique** $$y \in Y$$ we map $$x$$ to. But the existential quantifier does not
allows us to say that this $$y$$ is unique, only that there exists one. If there
were multiple $$y$$s satisfying $$R(x,y)$$ then $$f$$ would clearly be not a
function. 

Now the question is, couldn't we just have picked one $$y$$ for each $$x$$?

I could do this if was able to use Existential instantiation for each $$x \in
X$$, thereby constructing a formula of the form 

$$R(x_0,y_0) \wedge R(x_1,y_1) \wedge \dots \wedge R(x_n, x_n) \wedge \dots$$

which by Set Comprehension would give the function we need. But formulas and
proofs in set theory are finite and the one above is an infinite formula which would need an (potentially) infinite number of applications of the Existential Instantiation rule.

### Conclusions 
Hopefully this untangles some confusion around the axiom of choice.

On the other hand, it works in Type Theory simply because we have access to the proof that for every $$x$$ there exists a $$y$$ such that $$R(x,y)$$. But the reason why there exists only one is because inhabitants of the dependent product $$\Pi$$ are functions already.  

See the code below. 

{% highlight agda %}
choice : ∀ (A B : Set) → ∀ (R : A →  B → Set) → (∀ (x : A) → Σ B (λ y → R x y)) → Σ (A → B) (λ f → ∀ x → R x (f x))
choice A B R r = (λ x →  proj₁ (r x)) , (λ x → proj₂ (r x)) 
{% endhighlight %}

If you have any comment about this please feel free to drop me an email or something I would very happy to know more (especially if I said something wrong).  
