## Counting & Combinatorics

### Permutations: order matters
Number of ways to arrange all of them: $n!$

Arrange $k$ objects chosen from $n$. If you select and order k objects
$$P(n, k) = \frac{n!}{(n-k)!}$$

### Combinations: order does NOT matter
Choose k objects from n:
$$\binom{n}{k} = \frac{n!}{k!(n-k)!} = \frac{P(n, k)}{k!}$$


Example: choose 3 people from 10:

$$\binom{10}{3} = 120$$

We divide by $3!$ because the permutation count treats each ordering as different even though they are all the same group.


> **Example**: a deck has 52 cards, what is the probability that a 5-card hand contains exactly 2 aces?

**Answer**: *Total hands: $\binom{52}{5}$. For exactly 2 aces (choose 2 of the 4 aces): $\binom{4}{2}$. Then choose the remaining 3 cards from the 48 non-aces:
$\binom{48}{3}$*

Therefore:

$$P = \frac{\binom{4}{2}\binom{48}{3}}{\binom{52}{5}}$$


its the ways satisfying constraints out of the total ways

Exactly $k$: $P(k)$
At least $k$: $P(at\ least\ one) = 1 - P(none)$


## Counting with Repetition

If there are $n$ objects with groups of identical objects of sizes:
$$n_1, n_2, ... , n_k$$

then:
$$\frac{n!}{n_1! n_2! ... n_k!}$$

> Example: MISSISSIPPI

Counts: $M=1, I=4, S=4, P=2$

so:
$$\frac{11!}{4!4!2!}$$
