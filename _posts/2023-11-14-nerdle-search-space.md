---
title: 'Nerdle Search Space'
author: Jack Thomas
date: '2023-11-14'
slug: nerdle-search-space
categories:
  - category:
    - math
---

I just started (late, I know) regularly playing [Nerdle](https://nerdlegame.com/), which is a variation on Wordle where the guesses are 8-symbol mathematical equations instead of 5-letter dictionary words. Many people have taken on the question of the "best" starting guess in Wordle, and I thought it might be interesting to tackle similar questions with respect to Nerdle.

I began by determining the search space. I whittled it down from the naïve set of $$2.6 \times 10^9$$ possible symbolic combinations to $$7.2 \times 10^7$$ possible combinations that follow the rules to $$1.7 \times 10^4$$ guesses that are actually valid.

## Search Space

Let $$G$$ be the search space.

### Unrestricted Search Space

The unrestricted version of $$G$$ is simply the set of all 8-character selections - regardless of validity - from the accepted "letters." In this case, let $$L$$ be those letters. That is,

$$
L = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, +, -, *, /, =\}.
$$

Further, let $$n$$ represent the length of our guess. In the classic interpretation of Nerdle, $n = 8$. In this case (an 8-letter selection without considering validity),

$$
|G| = |L|^n = 15^8 \approx 2.6 \times 10^9.
$$

However, Nerdle enforces [additional restrictions (rules)](https://www.nerdlegame.com/faqs.html) on the guesses, which we can use to limit our search space. (Please note that although the specific verbiage of the rules has been modified, the result remains the same.)

### Mathematical Search Space Restriction

First, restrict $$G$$ such that the guess has only one "=" in position 2-7 (i.e., the guess cannot start with an equals sign, because that's not a valid equation). Now,

$$
|G| = 6 \cdot 14^7 \approx 6.3 \times 10^8.
$$

That's already an order of magnitude smaller than what we started with.

Second, further restrict $$G$$ such that the portion of the guess to the right of the "=" is only composed of the digits 0-9 and does not contain any leading zeros (i.e., it's a "number"), and such that there are no lone zeros in the equation. To understand how this affects our search space, define sets $$A$$, $$B$$, and $$C$$ (with elements $$a$$, $$b$$, and $$c$$, respecitvely) as follows.

- $$A = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, +, -, *, /\}$$ and ``a`` $$\in A$$
- $$B = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9\}$$ and ``b`` $$\in B$$
- $$C = \{1, 2, 3, 4, 5, 6, 7, 8, 9\}$$ and ``c`` $$\in C$$

Further, let's break $$G$$ down into subsets, where $$G_m$$ is the subset where the "=" is in position $$m$$. Using this second rule, we can elimiate many possibilities:

- $$G_1 = \emptyset$$ by a previous rule (i.e., the guess cannot start with an equals sign), so $$|G_1| = 0$$.
- $$G_2$$ must be of the form ``c=cbbbbb``. It is not possible for a one-digit number (``c``) to be equivalent to a six-digit number with no leading zeros (``cbbbbb``). Therefore, $$G_2 = \emptyset$$ and $$|G_2| = 0$$.
- $$G_3$$ must be of the form ``cb=cbbbb``. It is not possible for a two-digit number (``cb``) to be equivalent to a five-digit number with no leading zeros (``cbbbb``) . Therefore, $$G_3 = \emptyset$$ and $$|G_3| = 0$$.
- $$G_4$$ must be of the form ``cab=cbbb``. If ``a`` $$\in \{+, -, *, /\}$$, the highest possible number on the left side would be $$81 = 9 \cdot 9$$, which cannot be equivalent to a four-digit number with no leading zeros (``cbbbb``). Similarly, if ``a`` $$\in B$$, the highest possible number on the left side would be $$999$$, which also cannot be equivalent to a four-digit number with no leading zeros (``cbbbb``). Therefore, $$G_4 = \emptyset$$ and $$|G_4| = 0$$.
- $$G_8 = \emptyset$$ by a previous rule (i.e., the guess cannot end with an equals sign), so $$|G_8| = 0$$.

The remaining subsets are:

- $$G_5$$, which much be of the form ``caab=cbb``. Therefore, $$|G_5| = |A|^2 \cdot |B|^3 \cdot |C|^2 = 14^2 \cdot 10^3 \cdot 9^2 = 15,876,000$$;
- $$G_6$$, which must be of the form ``caaab=cb``. Therefore, $$|G_5| = |A|^3 \cdot |B|^2 \cdot |C|^2 = 14^3 \cdot 10^2 \cdot 9^2 = 22,226,400$$; and
- $$G_7$$, which must be of the form ``caaaab=b``. Therefore, $$|G_5| = |A|^4 \cdot |B|^2 \cdot |C|^1 = 14^4 \cdot 10^2 \cdot 9^1 = 34,574,400$$.

By effectively eliminating many possible positions of the "=" above, we have limited the size of our search space to

$$
|G| = |G_5| + |G_6| + |G_7| = 72,676,800 \approx 7.2 \times 10^7.

$$

That's two orders of magnitude smaller than what we started with.

Finally, further restrict $$G$$ such that the guess is a valid equation where there aren't any sequences of operations (e.g., "+-" or "\*+"). Let's continue in Python.

### Computational Search Space Restriction

The following code is in Python.

First, let's set up a few variables we can use later:

```python
file_path  = "/path/to/output.txt"
letters    = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "+", "-", "*", "/"]
digits     = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0"]
nonzero    = ["1", "2", "3", "4", "5", "6", "7", "8", "9"]
operations = ["+", "-", "*", "/"]
choices    = {"a": letters, "b": digits, "c": nonzero, "=": ["="]}
branches   = ["caab=cbb", "caaab=cb", "caaaab=b"]
```

Second, let's write a function to check guesses.

```python
def check_guess(guess):
	try:
		for i in range(len(guess) - 1):
			# allow neither sequences of operations (e.g., "+-" or "*+")
            #           nor leading zeros
			if guess[i] in operations and (guess[i + 1] in operations or guess[i + 1] == "0"):
				return(False)
		return(eval(guess.split("=")[0]) == eval(guess.split("=")[1]))
	except SyntaxError:
		# catch syntax errors, e.g., leading zeros
		return(False)
	except ZeroDivisionError:
		# don't allow dividing by zero
		return(False)
```

Third, let's iterate the mathematically restricted search space to determine the true search space.

```python
with open(file_path, "w+") as open_file:
	for branch in branches:
		for a in choices[branch[0]]:
			for b in choices[branch[1]]:
				for c in choices[branch[2]]:
					for d in choices[branch[3]]:
						for e in choices[branch[4]]:
							for f in choices[branch[5]]:
								for g in choices[branch[6]]:
									for h in choices[branch[7]]:
										guess = a + b + c + d + e + f + g + h
										if check_guess(guess):
											open_file.write("\n" + guess)
```

Is there a more efficient way to write this? Undoubtedly! I found [pedrokkrause/Nerdle-Equations](https://github.com/pedrokkrause/Nerdle-Equations/blob/main/NerdleGenerator.py) on GitHub, for example, which uses ``itertools.product`` to generate combinations, which is definitely more efficient(ly written) and scalable (e.g., beyond $n = 8$) than my for loop death trap. However, I can't find how he went from the "raw" set (i.e., any combination that Python will evaluate as True) to the "restricted" set (i.e., following the additional rules defined above). In the end, [his restricted set](https://github.com/pedrokkrause/Nerdle-Equations/blob/main/NerdleClassicRestricted.txt) is equivalent to mine. Regardless, this took less than ten minutes to run on my Dell XPS 9520 with an i7-12700H CPU and 16GB of RAM, which is good enough.

After running this code to compute the fully restricted search space,

$$
|G| = 17,723 \approx 1.7 \times 10^4,
$$

which is five orders of magnitude smaller than what we started with. This code is simple enough (it doesn't even import any libraries) and quick enough (like I said, less than ten minutes) that I'm not going to provide the full list on this website. (Besides, I don't have a good way to do it.)

In a future post, I hope (but do not promise) to figure out a method (or apply preexisting methods) to determine optimal guesses.
