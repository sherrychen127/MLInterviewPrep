

### Q1 - two coins

You have two coins:
- Coin A is fair.
- Coin B has \(P(H)=0.8\).
You choose one uniformly at random and flip it once. It lands heads.
What is the probability you chose Coin B?


P(B|H) = P(H|B)P(B)/P(H) = 0.4/0.65

P(B) = 1/2
B(H|B) = 0.8
P(H) = 1/2 * 0.8 + 1/2 * 0.5 = 0.65



### Q2. Bayes — two heads

Same setup, except you flip the chosen coin twice and observe:
\[
HH
\]

What's the probability you selected Coin B?

P(B|HH) = P(HH|B)P(B)/P(HH) = 0.64 * 0.5 / 0.65^2

P(HH|B) = 0.8 * 0.8
P(HH) = P(HH|A) + P(HH|B) = 0.8 * 0.8 + 0.5 * 0.5 = 0.89

## Q3. Independence

Roll a fair six-sided die. 

Let A = {roll is even}
B = {roll > 3}

Are A and B independent? 



