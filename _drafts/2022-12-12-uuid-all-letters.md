---
title: 'How Likely Is A UUID With All Letters?'
author: Jack Thomas
date: '2022-12-12'
slug: uuid-all-letters
categories:
  - category:
    - math
---

UUID strings have 32 characters, each of which can be a number (``0-9``) or a lowercase letter (``a-z``). That means there are 36 possible choices for each position, and 26 of those choices are letters. Since the probability of getting a letter in each spot is independent (assuming we're generating these UUID strings purely randomly), the probability $p$ is given by:

$$
p = \left( \frac{26}{36} \right)^{32} \approx 0.00003 \%
$$
