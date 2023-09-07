---
title: 'The Math of Pushup Pyramids'
author: Jack Thomas
date: '2020-07-30'
slug: pushup-pyramids
categories:
  - category:
    - math
---

I was first introduced to pushup pyramids at a hockey camp that I attended while I was in junior high. I've encountered them occasionally, but fairly infrequently, since then. Even so, every time they come up, I manually add up how many pushups are included in the pyramid. If we're going up to 10 pushups, here's how I usually add it up in my head:

$$
\begin{aligned}
p(10) = & 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10 + 9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1 \\
= & (1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9) + 10 + (9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1) \\
= & 2 \cdot 45 + 10 \\
= & 100
\end{aligned}
$$

Okay, so a pushup pyramid up to 10 pushups is 100 pushups total. I'm starting to think that this is just a way for coaches to hide how many pushups they're making you do... It's usually at this point during the pushup pyarmid that I stop thinking about math and remember to focus on doing pushups.

## Summation Formula

Now that I'm revisiting the math behind these pushup pyramids (while not simultaneously trying to do one), I'd like to see if I can come up with a generic formula to figure out how many pushups are in the pyramid.

Representing "the number of pushups in a pyramid up to $$n$$" as $$p(n)$$, let's revisit that 10-pushup pyramid:

$$
p(10) = 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10 + 9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1
$$

If we instead use an $$n$$-pushup pyramid, $$p(n)$$ can be represented as:

$$
p(n) = 1 + 2 + ... + (n - 1) + n + (n - 1) + ... + 2 + 1
$$

Let's see if we can simplify that (using summation notation and the relevant sum formula)

$$
\begin{aligned}
p(n) & = 1 + 2 + ... + (n - 1) + n + (n - 1) + ... + 2 + 1 \\
     & = \sum_{k = 1}^{n} k + \sum_{k = 1}^{n - 1} k \\
     & = \frac{n \cdot (n + 1)}{2} + \frac{(n - 1) \cdot n}{2} \\
     & = \frac{n^2 + n + n^2 - n}{2} \\
p(n) & = n^2
\end{aligned}
$$

## Conclusion

Turns out that there's a very simple formula for determining how many pushups are in a pushup pyramid!

$$
p(n) = n^2
$$

Does this conclusion shed light on some heretofore undiscovered principle of math? No. Does it use fancy complicated math? No. Is it interesting? Meh. Even so, am I happy that I finally sat down and figured out a generic formula for determining how many pushups are in a pushup pyramid? Yes! It's been bothering me for over a decade!
