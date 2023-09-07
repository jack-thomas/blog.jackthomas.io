---
title: 'Alternate Birthday Problem'
author: Jack Thomas
date: '2016-01-17'
slug: alternate-birthday-problem
categories:
  - category:
    - math
---

Since my birthday was a couple weeks ago, I thought I'd start this blog with a proof and a Java implementation of an alternate version of the [birthday problem](https://en.wikipedia.org/wiki/Birthday_problem).

## The Problem

An alternate version of the birthday problem is stated as follows:

Given a planet with $$x$$ days, find the probability that a $$y$$-group will celebrate a birthday every day.

## Proof

Consider the following sets:

- Let $$\Omega$$ be the set of birthdays of a $$y$$-group on a planet with $$x$$ days.
- Let $$A$$ be the subset of $$\Omega$$ where a birthday is celebrated every day. (This is the set for which we want to compute the probability.)
- Let $$S_n$$ be the subset of $$\Omega$$ such that there are no birthdays on the $$n^{\mathrm{th}}$$ day.
- Let $$T_m$$ be defined as the set of $$m$$-wise intersection of $$S$$-sets.

With the above sets, we can form $$A^c$$ as follows:

$$
\begin{align*}
A^c = S_1 \cup S_2 \cup \cdots \cup S_x ~.
\end{align*}
$$

By the Principle of Inclusion-Exclusion,

$$
\begin{align}
| A^c | &= | S_1 \cup S_2 \cup \cdots \cup S_x | \\
&= |T_1| - |T_2| + |T_3| - \cdots \pm |T_x| \\
&= \sum_{i=1}^x (-1)^{i+1} T_i ~.
\end{align}
$$

Since each $$T_m$$ excludes $$m$$ birthdays,

$$
\begin{align}
|T_m| &= {x \choose x-m} (x-m)^y \\
&= {x \choose m} (x-m)^y \label{cardoft}~.
\end{align}
$$

Combining equations the last equation of each of the last two groups,

$$
\begin{align}
|A^c| = \sum_{i=1}^x (-1)^{i+1} {x \choose i} (x-i)^y~.
\end{align}
$$

This gives the following probability of $$A^c$$,

$$
\begin{align}
P(A^c) &= \frac{\sum_{i=1}^x (-1)^{i+1} {x \choose i} (x-i)^y}{x^y}~.
\end{align}
$$

Thus,

$$
\begin{align}
P(A) = \frac{x^y - \sum_{i=1}^x (-1)^{i+1} {x \choose i} (x-i)^y}{x^y}~.
\end{align}
$$

## Java Implementation

Now that we know the formula, let's write some Java to compute it. Let's begin by computing that binomial coefficient $${x \choose i}$$ (a classic example of recursion):

```java
public static long choose(int n, int k){
	if (k==n || k==0){
	//We know these
	return 1;
	} else {
	//Compute it recursively
	return choose(n-1,k-1) + choose(n-1,k);
	}
}
```

Now that we know how to compute the binomial coefficient, we can compute the actual probability:

```java
public static double birthday(int x, int y){
	//Declare variables
	double inex, omega, binom, dayex, complement, probability;

	//Computations
	omega = Math.pow(x,y);
	complement = 0;
	for (int i = 1; i <= x; i++){
		inex = Math.pow(-1, i+1);
		binom = choose(x, i);
		dayex = Math.pow(x-i, y);
		complement = complement + (inex * binom * dayex);
	}
	probability = (omega - complement)/omega;

	//Output the probability
	return(probability);
}
```

That's really all there is to it. The full version is available as a [gist](https://gist.github.com/jack-thomas/37815039ff763d0e6ccc76bc62c826aa).
<!---TO EMBED THE WHOLE GIST
<script src="https://gist.github.com/jack-thomas/37815039ff763d0e6ccc76bc62c826aa.js"></script>
--->
