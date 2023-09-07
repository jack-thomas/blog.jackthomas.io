---
title: 'Scheduling Problem'
author: Jack Thomas
date: '2018-11-20'
slug: scheduling-problem
categories:
  - category:
    - math
---

## Question

Suppose you have been asked to create a "perfectly fair" schedule for four (distinct) workers who work four distinct shifts. The four shifts are broken into two groups (of two), where each group works together. In order to maintain fairness, each worker must work with each other worker the same number of times, and each worker must work each shift the same number of times. What is the shortest possible (in terms of duration) "perfectly fair" schedule?

## Answer

Let the set of workers be defined as $$W = \{W1, W2, W3, W4\}$$.

Consider these two facts:

- Since$$
|W| = 4
$$, there are $$C(4, 2) = 6$$ two-member subsets of $$W$$ (i.e. six ways that the four workers could be paired up to work the paired shifts). In order for each pair to work together the same number of times, the solution must be a multiple of six.
- Since each worker must work each shift the same number of times, and there are four shifts, the solution must be a multiple of four.

Combining these two facts, the shortest possible schedule is $$LCM(4, 6) = 12$$ units of time. (One such schedule, which also allows each worker to not work the same shift back-to-back, is shown below.)

Note: This means that there are $$P(12, 12) = 479,001,600$$ possible "perfectly fair" shortest schedules (though many of them have people working the same shift back-to-back for two or more weeks).

## One Possible Schedule

One possible "perfectly fair" schedule (as defined above) is shown below.

| Shift 1.a | Shift 1.b | Shift 2.a | Shift 2.b |
| :-------- | :-------- | :-------- | :---------|
| W1        | W2        | W3        | W4        |
| W4        | W1        | W2        | W3        |
| W3        | W4        | W1        | W2        |
| W2        | W3        | W4        | W1        |
| W1        | W4        | W2        | W3        |
| W3        | W1        | W4        | W2        |
| W2        | W3        | W1        | W4        |
| W4        | W2        | W3        | W1        |
| W1        | W3        | W4        | W2        |
| W2        | W1        | W3        | W4        |
| W4        | W2        | W1        | W3        |
| W3        | W4        | W2        | W1        |
