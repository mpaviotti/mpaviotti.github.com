---
layout: post
title:  "Bisimulations, Equality and Traces" 
date:   2023-10-09 13:14:21 +0000
categories: semantics concurrency 
---

Today in Theory of Concurrency class we saw how, in certain variants of CCS,
bisimilarity is larger than equality of traces as in it distinguishes more things than
equality. 

One example of this fact is when considering CCS with the choice operator.  In
this language we can define a process $$P$$ and a process $$Q$$ as follows 

$$P = \text{pay}.(\text{coffee}. 0 + \text{tea}. 0)$$ 

$$Q = (\text{pay}.\text{coffee}.0 + \text{pay}.\text{tea}. 0)$$

Now the *trace semantics* of the CCS processes can be defined by a function 

$$[\![ \cdot ]\!] : \text{CCS} \to \mathcal{P}_\text{fin}(\text{Str } L) $$

where $$L$$ is the finite set of actions and, for a generic set $$A$$, the set
$$\text{Str } A = 1 + A \times \text{Str }A $$ is the set of possibly finite
streams over a set $$A$$. 

For the processes above we have that the semantics of $$P$$ is $$[\![P]\!] =
\{\text{pay}.\text{coffee}, \text{pay}.\text{tea}\}$$ and the semantics of $$Q$$
is $$[\![ Q ]\!] = \{\text{pay}.\text{coffee}, \text{pay}.\text{tea}\}$$ and
thus the trace semantics of $$P$$ and $$Q$$ indicate that these processes should
be equal. 

However, consider the relation $$P$$ *simulates* $$Q$$ which is stated as 

$$ P \lesssim Q \Leftrightarrow \forall P'. \text{ if } P \xrightarrow{a} P'
\text{ then  } \exists Q'. Q \xrightarrow{a} Q' \text{ s.t. } P' \lesssim Q'$$

Now the *bisimulation* relation can be defined as $$P \approx Q \Leftrightarrow P
\lesssim Q \text{ and } Q \lesssim P$$. 

The above example is a standard example in concurrency theory that shows that
bisimulation can distinguish processes where equality on the trace semantics
indicate that they should be regarded as equal and that is why bisimulations
turn out to be more useful relations to compare processes.

Using the example above we can prove that $$Q \lesssim P$$. Let's define
half-evaluated processes as 

$$P' = \text{coffee}. 0 + \text{tea}. 0$$

$$P'_{1} = \text{coffee}. 0$$

$$P'_{2} = \text{tea}. 0$$

$$Q_{1} = \text{pay}.\text{coffee}.0$$

$$Q_{2} = \text{pay}.\text{tea}.0$$

$$Q'_{1} = \text{coffee}.0$$

$$Q'_{2} = \text{tea}.0$$

Now for all transitions of $$Q$$ we have to show $$P$$ simulates them. The first
one is $$Q \xrightarrow{\text{pay}} Q'_{1}$$.  Obviously $$P
\xrightarrow{\text{pay}} P'_{1}$$ and so now we have to show that $$Q'_{1}
\lesssim P'_{1}$$ which clearly does.  This works similarly if $$Q$$ decides to
take the other route and produce tea in the end. 

All right, but $$P \lesssim Q$$ does not work. This is because since $$P$$ makes a transition $$P \xrightarrow{\text{pay}} P'$$ we are forced to select which branch in $$Q$$ is simulating this behaviour. No matter which one we choose we get stuck in one way or the other. Say $$Q \xrightarrow{\text{pay}} Q'_{1}$$ we have to show $$P' \lesssim Q'_{1}$$, but this latter fact does not hold because $$P'$$ can make two different transitions and $$Q'_1$$ can only make one.   

## CoRecursion Schemes and Traces
Consider now the unfold function which takes a seed function an produces a trace
by *running* the seed at each steps

{% highlight haskell %}
unfold :: (x -> (L, x)) -> x  -> Str L
unfold seed x = let (l,x') = seed x in l :: unfold seed x' 
{% endhighlight %}

Notice that the seed function $$X \to L \times X$$ can be viewed as a Labeled
Transition System (LTS) where the set of states is $$X$$ and the function is the
function implementing the transitions. 

It is a very well-known fact that the `unfold` is a fully abstract map in the
sense if we consider the notion of bisimilarity above and set $$[\![ \cdot
]\!]$$ to be `unfold seed`  then we have the following theorem 

> **Full abstraction** $$\text{ for all } t_{1}, t_{2}, t_{1} \approx t_{2} \Leftrightarrow [\![ t_{1} ]\!] = [\![ t_{2}]\!]$$. 

This is also backed by the fact that when programming in proof assistants like
(e.g.) Agda -- since coinductive data types are not really final coalgebras --
it is common practice to just  add the following axiom to the type theory 

> **Axiom** $$\text{ for all } (s_{1}, s_{2} : \text{Str L}). s_{1} \approx s_{2} \to s_{1} = s_{2}$$ . 

Even more so, in some proof assistants like Isabelle coinductive data types are
real final coalgebras and so the above axiom is actually a true fact in the
prover's logic. 

Notice that the other direction is obvious and thus the axiom implies
bisimiliary is *logically equivalent*  equality. 

> So why bisimulation in the above example does not correpond to equality? 

The reason is that the shape behaviours for CCS+choice is not $$BX = L \times
X$$ but it is $$\mathcal{P}_\text{fin}(L \times X)$$. 

In fact, the *seed* function describing the LTS of CCS+choice has the following
type 

{% highlight haskell %} 
opsem :: CCS ->  [(L, CCS)]
{% endhighlight %}
where we use lists `[-]` as a (rough) implementation of finite powersets.  

At this point the LTS for CCS+choice can be defined roughly like this 

{% highlight haskell %}
...
opsem (p + q) = [(l, p') | (l, p') <- opsem p ] ++ [(l, q') | (l, q') <- opsem q ]  
{% endhighlight %}

And now the unfold on this LTS will yield a fully abstract semantics  
{% highlight haskell %}
unfold opsem :: CCS -> Trees L 
{% endhighlight %}
where $$\text{Trees}\; L = \mathcal{P}_\text{fin} (L \times \text{Trees}\; L)$$.
