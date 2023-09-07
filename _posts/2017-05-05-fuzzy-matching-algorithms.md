---
title: 'Fuzzy Matching Algorithms'
author: Jack Thomas
date: '2017-05-05'
slug: fuzzy-matching-algorithms
categories:
  - category:
    - math
---

Traditionally, comparing two strings yields either true or false, indicating whether the strings are *exactly* the same. Fuzzy matching (also known as [approximate string matching](https://en.wikipedia.org/wiki/Approximate_string_matching) or [probabilistic record linkage](https://en.wikipedia.org/wiki/Record_linkage#Probabilistic_record_linkage)) overcomes this limitation by calculating the probability that two records refer to the same entity. 

There are two broad categories of fuzzy matching algorithms: edit distances and phonetic algorithms. Generally, edit distances count the minimum number of operations that it takes to transform one string into the other, and phonetic algorithms determine whether two strings "sound" alike.

If you're interested in trying out any of the algorithms I'll be discussing below, I built a [Shiny app](https://jackthomas.shinyapps.io/presentation/) that covers a lot of the same information that's presented here.

## Edit Distances

### Hamming Distance

The simplest edit distance is the Hamming ([1950](http://www.lee.eng.uerj.br/~gil/redesII/hamming.pdf)) distance, which counts the number of nonmatching characters in a given pair of strings. Symbolically, it's defined for two strings ($$A$$ and $$B$$) as:

$$
D_H(A,B) = \begin{cases}
\frac{| (A \cap B)^c |}{2}, &|A| = |B| \\
-1, &|A| \neq |B|
\end{cases}
$$

A simple extension of this algorithm is a score (which provides the probability that two strings are the same), defined for two strings ($$A$$ and $$B$$) as:

$$
S_H(A,B) = \begin{cases}
\frac{|A| - D_H}{|A|}, &|A| = |B| \\
0, &|A| \neq |B|
\end{cases}
$$

Consider, for example, "WOW" and "MOM". The first and third characters ("W" and "M" in both cases) don't match, yielding a Hamming distance of 2. Since we're counting nonmatching characters, the Hamming distance is not defined for strings of different lengths (thus returning a nonsensical distance of -1). One obvious way to overcome this length limitation is to pad (add underscores or some other character until the strings are the same length). If we consider "ROB COFFMAN" and "ROBERT COFFMAN", we get the following results:


| Padding | Distance | Score |
| :---: | :---: | :---: |
| No Padding | -1 | 0% |
| Left Padding | 6 | 57.14% |
| Right Padding | 13 | 14.29% |

As we can see from the prior example, left padding appears to overemphasize the last name while right padding appears to overemphasize the last name. This become even clearer when we compare "ROBERT COFFMAN" and "ROBERT SMITH":

| Padding | Distance | Score |
| :---: | :---: | :---: |
| No Padding | -1 | 0% |
| Left Padding | 14 | 0% |
| Right Padding | 7 | 50% |

Unfortunately, this algorithm is overly simplistic. Specifically, it is invalid for strings of different lengths, padding can overemphasize the beginning or end of the strings, and one mistaken insertion can drastically alter the score.

### Levenshtein Distance

One of the best-known edit distances is the Levenshtein ([1965](https://nymity.ch/sybilhunting/pdf/Levenshtein1966a.pdf)) distance, which counts the number of insertions, deletions, and substitutions required to get from one string to another. Again, consider our favorite example, "BOB COFFMAN" and "ROBERT COFFMAN". To get from the prior to the latter, we perform the following edits:

1. <b>B</b>ob Coffman $$\rightarrow$$ <b>R</b>ob Coffman
2. Rob Coffman $$\rightarrow$$ Rob<b>e</b> Coffman
3. Robe Coffman $$\rightarrow$$ Robe<b>r</b> Coffman
4. Rober Coffman $$\rightarrow$$ Rober<b>t</b> Coffman

Thus, the Levenshtein distance is 4. Though this distance provides a more acurrate measure of similarity than the Hamming distance, it is computationally inefficient, it does not convert easily into a percentage, and it returns some rather odd results with longer strings. For example, "France" and "Quebec" (with a distance of 6) are more similar than "France" and "Republic of France" (with a distance of 12) (see [this article](http://www.catalysoft.com/articles/StrikeAMatch.html)).

<!---
### Damerau-Levenshtein Distance
### Jaro Distance
### Longest Commmon Substring
--->

## Phonetic Algorithms

### Soundex Algorithm

The Soundex Algorithm is defined by the [Census Bureau](https://www.archives.gov/research/census/soundex.html) as follows:

1. Retain first letter and drop occurrences of A, E, I, O, U, H, W, and Y.
2. Replace consonants with numbers per the following table:

| Number | Represents |
| ---: | :--- |
| 1 | B, F, P, V |
| 2 | C, G, J, K, Q, S, X, Z |
| 3 | D, T |
| 4 | L |
| 5 | M, N |
| 6 | R |

3. Repeated numbers are only counted once (unless they're separated by a vowel). This also applies to the first letter.
4. Add zeros or remove numbers from right until the code is four characters (one letter and three numbers).

Once again, let's consider our favorite example, "Robert Coffman". After this name has been split into two substrings (first and last name of course), it becomes "R163 C155". Interestingly, this does not match "Bob Coffman", which becomes "B100 C155". As such, one of the problems with the Soundex algorithm (and other phonetic matching algorithms such as NYSIIS or Metaphone) is that they only provide a simple true/false verdict (thus known as equivalence algorithms). However, they're still useful because data can be pre-hashed, which allows for faster joins than some of the other algorithms we're considering. 

<!---
### NYSIIS Algorithm
### Metaphone
--->

## Other Algorithms

### S&#248;rensen-Dice Coefficient

Another possible fuzzy matching algorithm is the S&#248;rensen-Dice coefficient. This coefficient was independently introduced by two botanists in the 1940's (see [S&#248;rensen, 1948](http://www.worldcat.org/search?q=no%3A04713331) and [Dice, 1945](http://onlinelibrary.wiley.com/doi/10.2307/1932409/full)), but is easily applied to string (broken into [bigrams](https://en.wikipedia.org/wiki/Bigram) and denoted $A$ and $B$). That is, the S&#248;rensen-Dice coefficient is defined as:

$$
C_{SD} = \frac{2 | A \cap B |}{|A| + |B|}
$$

Though this algorithm is slightly less efficient (since we aren't able to pre-hash data) than the phonetic algorithms, it has seemed to be more correct.

#### Example

To demonstrate this algorithm's effectiveness, I rebuilt a better version of the [University of Wisconsin-River Falls Directory](https://www.uwrf.edu/Directory/), which is available via [this Shiny app](https://jackthomas.shinyapps.io/search/). To demonstrate the differences, I suggest you try our favorite example. That is, I suggest you search for "Bob Coffman". While the University Directory does not yield any results, my retooled search engine yields the expected result (Robert Coffman of the Math department).
