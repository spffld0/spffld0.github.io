---
nav_order: 1
layout: page
title: Primitive Roots
parent: Modular Arithmetic
permalink: competitive-programming/number-theory/modular-arithmetic/primitive-roots
---

{% include mathjax.html %}

# Primitive Roots



## Order of magnitude of primitive roots

The first algorithm of the following section relies pretty heavily on how big the first primitive root of a number actually is. Let's look at some results on this topic.

{: .new-title}
> Definition
>
> Let $$g(n)$$ denote the smallest primitive root of $$n$$, supposing it exists.

We will first study the case when $$n$$ is prime. Realistically speaking, you will rarely need to work with primitive roots modulo composite numbers, and, unfortunately, that case is a little more complex.

In practice, $$g(p)$$ seems to be very small. Check [this link](http://users.uoi.gr/abeligia/Teaching2011_2020/NumberTheory/NT2016/PrimitiveRoots.pdf) for raw data. Apparently, $$g(p)$$ rarely exceeds $$\sqrt p$$. In fact, most of the time it doesn't even exceed $$10$$. Still, edge cases can be quite scary, so let's also see what the theory has to say.

Our most optimistic result regarding $$g(p)$$ assumes the Generalized Riemann Hypothesis.

{: .note-title}
> Theorem (Shoup)
>
> Assumming the Generalized Riemann Hypothesis, we find $$g(p) = O(\log^6(p))$$

A more concrete upper bound is provided by theorem 2 [here](https://arxiv.org/pdf/1803.11061). This is what is states:

{: .note-title}
> Theorem (Jana Pretorius)
>
> We have $$g(p) < p^{0.68}$$ for all primes $$p$$.

Again, in practice we can be much more optimistic (even more so if we are only interested in the average behavior of $$g(p)$$, e.g. if we are relying on some kind of amortization).

### Primitive roots modulo composite numbers

Let's now look at primitive roots modulo composite numbers. Remember that we only need to consider $$n$$ of the form $$p^k$$ and $$2p^k$$ with odd $$p$$. Who cares about $$4$$?

{: .note-title}
> Theorem
>
> Let $$p$$ be an odd prime and $$g$$ a primitive root modulo $$p$$. Then, at least one of $$g$$ and $$g + p$$ is a primitive root modulo $$p^k$$, where $$k$$ can be any positive integer.

The bigger the exponent $$k$$ is, the smaller $$p$$ is in comparison to $$p^k$$, hence the worst case scenario should be $$n=p^2$$. Using the upper bounds presented in the previous section, we find

$$ g(n) = O(\log^6(n) + \sqrt n) = O(\sqrt n)$$

which isn't that bad.

The case when $$n$$ is even is tragic however.

{: .note-title}
> Theorem
>
> Let $$p$$ be an odd prime and $$g$$ a primitive root modulo $$p$$. Then, at least one of $$g$$ and $$g + p^k$$ is a primitive root modulo $$2p^k$$, where $$k$$ can be any positive integer.

Thankfully the data presented [here](https://oeis.org/A046145/b046145.txt) (the left column is $$n$$ and the right column is $$g(n)$$, defaulting to $$0$$ when there are no primitive roots) shows that in practice $$g(n)$$ is incredibly small even for composite $$n$$, again following the $$\sqrt n$$ worst-case scenario trend.

## How to find a primitive root

### The naive method
Although the title says "naive method", this should be enough for competitive programming purposes.

We can just itereate over every number $$g \in \mathbb{Z}_n^*$$ and check if it is a primitive root. How can we do that? A pretty inefficient way would be to just check for every proper divisor $$d$$ of $$\phi(n)$$ whether $$g^d \equiv 1 \,(\text{mod } n)$$ holds using binary exponentiation. If it does, then we can eliminate $$g$$ as a candidate (along with any power of $$g$$). 

Let's try to optimize this a little. The key idea was already mentioned: if $$g$$ is not a primitive root, then neither is $$g^k$$ for any $$k \ge 1$$. Looking at this idea "in reverse", we find that $$g^k \not\equiv 1 \,(\text{mod } n)$$ implies $$g^d \not\equiv 1 \,(\text{mod } n)$$ for any $$d\,\vert\,k$$, since $$g^k$$ is be a power of $$g^d$$. Using this fact we can reduce our search space: imagine we want to perform the aforementioned test with exponent $$d \,\vert\, \phi(n)$$. If we can find some in-between $$k$$ satisfying $$d \,\vert\, k \,\vert\, \phi(n)$$, then we can simply skip $$d$$.

Therefore, we should only look at the unskippable values of $$d$$, i.e. the "maximal" divisors of $$\phi(n)$$. They are precisely the values given by $$\frac{\phi(n)}{p}$$ for prime factors $$p$$. This is pretty clear intuitively: to get some divisor of a number, we can look at its prime factorization and simply remove some primes. If we want the divisor we get to be maximal, then we shouldn't remove more than one prime (if we eliminated multiple primes, then we could get a "bigger" divisor by removing just one of them).

To conclude, our algorithm will proceed as follows: iterate over all $$g \in \mathbb{Z}_n^*$$. For every prime factor $$p$$ of $$\phi(n)$$, check whether $$g^\frac{\phi(n)}{p} \equiv 1 \,(\text{mod } n)$$ using binary exponentiation. The time complexity of this algorithm is $$O(g \log^2(\phi(n)))$$, where $$g$$ is the primitive root we find in the end, since the prime factor count of $$\phi(n)$$, the time complexity of binary exponentiation and the time complexity of the euclidean algorithm (to check whether $$\gcd(g, n)=1$$) are all $$O(\log(\phi(n)))$$.

```c++
```

### A probabilistic algorithm