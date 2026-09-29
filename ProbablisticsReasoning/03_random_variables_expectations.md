## Random Variables

A random variable is a function that maps an outcome to a number

Suppose you flip two coins. 

Sample space:

$$\Omega  = {HH, HT, TH, TT}$$

Define:

$$X = number of heads$$

Then:

$$X(HH) = 2$$
$$X(HT) = X(TH) = 1$$

So X has distribution:

$$P(x=0) = \frac{1}{4}, P(x=1)=\frac{1}{2}, P(x=2)=\frac{1}{4}$$

**Discrete vs continuous**
Discrete: takes contable values

Examples:
$$X = number of heads$$
$$X  \sim Binomial(n, p)$$


Continuous: takes values over an interval.

Examples:

$$X \sim N(\mu, \sigma^2)$$

For discrete variables, you use a PMF:
$$p_X(x) = P(X=x)$$

For continuous variables, you use a PDF f_X(x), where probabilities are areas:

$$P(a < X < b) = \int^b_a f_X(x)dx$$

important:
$$P(X=x) = 0$$

for a continuous random variable. 

## Expectation

Expectation is the long-run average value of a random variable. 

For discrete X:

\[
\boxed{E[X]=\sum_x xP(X=x)}
\]

For continuous \(X\):

\[
E[X]=\int x f_X(x)\,dx
\]

**Expectation of a function**

For a fair die:
$$E[x] = 1\frac{1}{6} + 2\frac{1}{6} + ... + 6\frac{1}{6} = 3.5$$

expectation of a function:

$$E[g(X)] = \sum_x g(x)P(X=x)$$

For example:

$$E[X^2] = \sum_x x^2P(X=x)$$

**Linearity of expectation**

\[
\boxed{
E[X_1+\cdots+X_n]
=
E[X_1]+\cdots+E[X_n]
}
\]

## Variance

$$Var(X) = E(X^2) - [E(X)]^2$$

Example:

Let $$X \sim Bernoulli(p)$$

So:

$$X=
\begin{cases}
1 & p\\
0 & 1-p
\end{cases}$$

Then:

$$E[X] = p$$

Because X^2 = X,



Therefore:
$$
Var(X)=p-p^2
$$

\[
\boxed{Var(X)=p(1-p)}
\]Scaling

Know this cold:

\[
Var(aX+b)=a^2Var(X)
\]

Adding a constant doesn't change variance:

\[
Var(X+b)=Var(X)
\]but multiplying by 2 multiplies variance by 4.

## Covariance

Covariance measures whether two random variables tend to move together:

\[
\boxed{
Cov(X,Y)=E[XY]-E[X]E[Y]
}
\]

Positive covariance → they tend to increase together.

Negative covariance → one tends to increase when the 
other decreases.

Zero → no linear relationship.

**Variance of a sum**

This is extremely important:

\[
\boxed{
Var(X+Y)
=
Var(X)+Var(Y)+2Cov(X,Y)
}
\]

If \(X,Y\) are independent:
\[
Cov(X,Y)=0
\]

so:
\[
Var(X+Y)=Var(X)+Var(Y)
\]But remember:
\[
\boxed{\text{independent}\Rightarrow Cov=0}
\]while generally:
\[
\boxed{Cov=0\not\Rightarrow\text{independent}}
\]

That's another common interview trap.

## Conditional expectation

Now combine conditional probability with expectation.

$$E[X|Y=y]$$

means:
> What is the expected value of X, after i learn that Y=y?

## Low of total expectation

One of the most useful formulas in probability:

\[
\boxed{E[X]=E[E[X|Y]]}
\]

Also called the tower property.
For an event \(A\), this becomes:

\[
E[X]
=
E[X|A]P(A)
+
E[X|A^c]P(A^c)
\]

Example:

You choose:
- fair coin with probability $\frac{1}{2}$
- biased coin with probability $\frac{1}{2}$

The biased coin has $P(H) = 0.8$

Let $X=1$ for heads

Condition on which coin $C$ you selected:
$$E[X|C=fair] = 0.5$$
$$E[X|C=biased] = 0.8$$

Thus:
$$E[X] = \frac{1}{2} (0.5) + \frac{1}{2} (0.8) = 0.65$$
