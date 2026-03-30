## Maximum Likelihood Estimation (MLE)

We assume data is generated from a model with unkonwn parameters. We want to find parameter values that make the observed data most likely. 
- Data comes from some unknown distribution 
- We build a model with parameter $\theta$
- We use data to estimate $\theta$

Given data $x$ -> estimate $\theta$

Suppose:
- Data: $x_1, x_2, ..., x_n$
- Model: $p(x|\theta)$

### Likelihood
> Treat data as fixed, treat parameters as variable, ask:
> Which $\theta$ makes this data most probable?

$$L(\theta) = p(x|\theta) = \prod^n_{i=1} p(x_i | \theta)$$

## Maximum A Posteriori (MAP)
> Choose the parameter $\theta$ that is mostprobable after seeing the data 

$$\theta_{MAP} = \text{argmax}_\theta p(\theta | x)$$

Instead of maximizing likelihood like MLE:
$$\theta_{MLE} = \text{argmax}_\theta p(x| \theta)$$

