## Counting & Combinatorics

\[
\boxed{
\begin{array}{ll}
n! & \text{arrange}\\[4pt]
\frac{n!}{(n-k)!} & \text{choose + order}\\[6pt]
\binom nk & \text{choose}\\[6pt]
n^k & \text{ordered choices with replacement}\\[6pt]
\frac{n!}{n_1!\cdots n_k!} & \text{repeated categories}\\[6pt]
\binom{n+k-1}{k-1} & \text{stars and bars}
\end{array}
}
\]


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

## Sampling with and w/o replacement

| | Order matters | Order doesn't matter |
|---|---:|---:|
| Without replacement | \(\frac{n!}{(n-k)!}\) | \(\binom nk\) |
| With replacement | \(n^k\) | \(\binom{n+k-1}{k}\) |

## Binomial coefficients
> Instead of thinking about choosing objects, think about choosing positions

How many binary strings of length 10 contain exactly four 1s? $\binom{10}{4}$

Number of sequences containing 6 heads and 4 tails: $\binom{10}{6}$

## Multinomial counting

Suppose 10 objects need to be divided into labeled groups of sizes: 3, 2, 5

Number of ways:
$$\frac{10!}{3!2!5!}$$

or 
$$\binom{10}{3}\binom{7}{2}\binom{5}{5}$$

## Stars and bars
> How many nonnegative integer solutions satisfy: x1 + x2 + .. + xk = n

Answer:
$$\binom{n+k-1}{k-1}$$

>Example: $x + y + z = 10$ with $x, y, z \geq 0 $

Imagine 10 stars:
$**********$ and place 2 separators: $*** | **** | ***$

meaning $x = 3, y = 4, z = 3$. You arange:
- 10 stars
- 2 bars

so $$\binom{12}{2} = 66$$

If every variable must be positive:
$$\binom{n-1}{k-1}$$

## Complement counting

> Wanted = total - unwanted 

>Ex: How many 5-person committees from 10 people contain at least one of Alice or Bob

$$\binom{10}{5} - \binom{8}{5}$$

## Inclusion-exclusion
$$|A \cup B| = |A| + |B| - ｜A \cap B|$$

For three sets:
$$|A \cup B \cup C| = |A| + |B| + |C| - |A \cap B| - |A \cap C| - |B \cap C| - |A \cap B \cap C|$$


## Counting arrangements with constraints
Suppose 6 people sit in a row and Alice and Bob must sit together. Treat Alice + Bob as one block

Then instead of 6 objects, you arrange 5 objects: $5!$, inside the block: $AB$ or $BA$ So:
$$2 \cdot 5!$$

**If Alice and Bob cannot sit together:**
Use complement:

$$6! - 2(5!)$$


## Circular permutations
Arrange n people around a circular table.

Normally: $n!$

But rotations are equivalent represent the same seating. 

Fix one person and arrange everyone else:

$$(n-1)!$$


## The binomial theorem

$$(x+y)^n = \sum^n_{k=0} \binom{n}{k} x^k y^{n-k}$$

$$(x+y)^3 = x^3 + 3x^2y + 3xy^2 + y^3$$

The coefficients: 1, 3, 3, 1
are:
$$\binom{3}{0}, \binom{3}{1}, \binom{3}{2}, \binom{3}{3}$$

The coefficient counts how many ways you can choose which factors contribute x


## Common Identities
Symmetry:
$$\binom{n}{k} = \binom{n}{n-k}$$

Pascal's identity:
$$\binom{n}{k} = \binom{n-1}{k} + \binom{n-1}{k-1}$$


sum:
$$\sum^x_{k=0}\binom{n}{k} = 2^n$$

Why? 
A set with n elements has $2^n$ subsets. Alternatively, choose a subset by size:

$$\binom{n}{0} + \binom{n}{1} + ... + \binom{n}{n}$$


> **Example**: Suppose two dice are rolled. Possible sums are: 2, 3, ..., 12

**Answer**: There are 11 sums. But: $P(sum=2) \neq \frac{1}{11}$
because the sums aren't equally likely. Instead count the equally likely ordered dice outcomes: $6 \times 6 = 36$

Sum 7 occurs through:
$$(1, 6), (2, 5), (3, 4), (4, 3), (5, 2), (6, 1)$$

So: 

$$P(sum=7) = \frac{6}{36} = \frac{1}{6}$$

## Practices

>Q1. How many ways can you choose 4 people from 10?

$$\binom{10}{4}$$

>Q2. How many ways can you assign president, VP and treasure among 10 people

$$10 \cdot 9 \cdot 8$$

>Q3. Whats the probability of exactly 3 heads in 7 fair coin flips?

$$\frac{\binom{7}{3}}{2^7}$$

>Q4. What's the probability that 5 cards contain exactly 1 ace?

$$\frac{\binom{48}{4}\binom{4}{1}}{\binom{52}{5}}$$

>Q5. What's the probability that 5 cards contain at least 1 ace?

$$1 - \frac{\binom{48}{5}}{\binom{52}{5}}$$

> Q6. How many nonnegative solutions are there to: $x + y + z = 20$

$$\binom{22}{2}$$

> Q7. How many arrangements of 8 people have Alice and Bob adjacent?

$$7!2!$$

> Q8. How many binary strings of length 12 contain exactly five 1s?

$$\binom{12}{5}$$