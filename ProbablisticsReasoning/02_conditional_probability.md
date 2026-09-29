## Conditional Probability

### Multiplication rule

$$P(A \cap B) = P(A | B) P(B) = P(B|A) P(A)$$

or three events:

$$P(A \cap B \cap C) = P(A)P(B|A)P(C|A, B)$$

General form:

$$P(A_1, ..., A_n) = P(A_1)P(A_2 | A_1)P(A_3 | A_1, A_2)...$$

> Example: cards without replacement

probability that the first two cards are aces:

$$P(A_1 \cap A_2) = P(A_1) P(A_2 | A_1) = \frac{4}{52}\frac{3}{51} = \frac{1}{221}$$

## Independence vs. conditional probability 
A and B are independent if knowing B gives you no information about A:

$$P(A|B) = P(A)$$

Equivalent:

$$P(A \cap B) = P(A) P(B)$$


## Law of total probability
Suppose the population can be split into cases: 
$$B_1, B_2, ..., B_n$$

Then

$$P(A) = \sum_i P(A|B_i)P(B_i)$$

> Calculate the probability of A under each possible scenario, then weight by how likely that scenario is. 


## Bayes' theorem

Bayes is just conditional probability + reversing the direction.

$$P(A | B) = \frac{P(B|A)P(A)}{P(B)}$$

And usually:

$$P(B) = P(B|A)P(A) + P(B|A^c)P(A^c)$$

> A disease occurs in 1% of people

a test has:

$$P(+ | D) = 0.99$$

and 

$$P(+ | D^c) = 0.05$$

You test positive, whats $P(D|+)?$ Use a population of 10,000. Diseased: 100, positive among diseased: 99. Healthy: 9900

False positives:
$$9900 (0.05) = 495$$

Total positive tests: $99 + 495 = 594$

Of those, only 99 actually have the disease:

$$P(D | +) = \frac{99}{594} \approx 16.7\% $$

                    Person
                  /        \
              Disease     Healthy
                .01         .99
               /   \       /   \
              +     -     +     -
            .99   .01   .05   .95


$$P(D, +) = 0.01(0.99)$$

while $$P(+) = 0.01(0.99) + 0.99(0.05)$$

Then 

$$P(D|+) = \frac{desired\ positive\ branch}{all\ positive\ branches}$$

## "At least one" conditioning

Two children are independently equally likely to be boys or girls. At least one child is a boy

Whats the probability both are boys? 

Original sample space:
$$\{BB, BG, GB, GG\}$$

Condition on "at least one boy":

$$\{BB, BG, GB\}$$

Therefore:

$$ P(BB|at\ least\ one B) = \frac{1}{3}$$

However, if you randomly select one of the two children and observe that the selected child is a boy. Now:

$$P(BB | observed\ selected\ child\ is\ B) = \frac{1}{2}$$

\[
\boxed{\text{Condition not only on what you know, but how you learned it.}}
\]

## Monty Hall

Three doors:
- one car
- two goats

You pick Door 1. Probability car is behind Door 1 is $\frac{1}{3}$. Probability it's somewhere else is $\frac{2}{3}$

Monty knows where the car is and always opens a goat door. After he opens one of the other doors, your original door is still: $\frac{1}{3}$. The remaining unopened door inherits the $\frac{2}{3}$.

Therefore switching wins with probability:
\[
\boxed{\frac23}.
\]The crucial point isn't simply "there are two doors left."
Monty's action contains information because his choice is constrained.

## Conditional independence


Events A and B can be dependent normally but independent once you know C:

\[
\boxed{
P(A,B\mid C)
=
P(A\mid C)P(B\mid C)
}
\]

Example intuition:
Suppose:
- \(A\): person carries an umbrella
- \(B\): person wears rain boots
- \(C\): it's raining

Without knowing the weather, umbrella and boots are correlated. But conditional on the weather, much of that association may disappear.