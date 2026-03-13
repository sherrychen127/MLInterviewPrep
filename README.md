# ML Interview Prep

A collection of notes and from-scratch implementations covering machine learning, deep learning, and data structures & algorithms — geared toward ML engineering interviews.

## Repository Structure

### MLCoding/
From-scratch implementations in Python (Jupyter notebooks):
- `linear_regression.ipynb` — Linear Regression
- `logistic_regression.ipynb` — Logistic Regression
- `decision_tree.ipynb` — Decision Tree
- `knn.ipynb` — K-Nearest Neighbors
- `svm.ipynb` — Support Vector Machine
- `k_means_clustering.ipynb` — K-Means Clustering
- `PCA.ipynb` — Principal Component Analysis
- `feedforward.ipynb` — Feedforward Neural Network
- `convolution.ipynb` — Convolutional Neural Network
- `transformer.ipynb` — Transformer
- `practice.ipynb` — Mixed practice problems

### MLFundamentals/
Concept notes and Q&A-style study guides:
- `ML_fundamentals.md` — Core ML concepts (CNNs, ViTs, etc.)
- `Transformer.md` — Transformer architecture deep dive
- `recommendation_system.md` — Recommendation systems
- `Anomaly_detection.md` — Anomaly detection methods
- `Object_detection.md` — Object detection (YOLO, R-CNN, etc.)
- `CLIP.md` — CLIP model overview
- `MLSystemDesign.md` — ML system design patterns

### DSA/
Data structures & algorithms notebooks:
- `Dynamic Programming.ipynb`
- `Backtrack.ipynb`
- `Graphs.ipynb`
- `Knapsack.ipynb`
- `Trees.ipynb`
- `Lists.ipynb`

### Probabilistic Reasoning (WIP)
https://probmlcourse.github.io/csc412/ 


#### Linear / Probablistic supervised learning
- Logistic regression 
- Linear regression
- Bayesian linear regression
- MAP vs MLE 
- Regularization (L1 / L2)
- Bias-variance tradeoff 
- Feature scaling / collinearity 

You need to know:
- derive gradient
- explain optimization
- reason about overfitting
- explain uncertainty 

#### Generative probabilistic models 
- Naive Bayes
- Mixture of Gaussians 
- Expectation-Maximization

You need to know: 
- why EM works 
- soft assignment intuition 
- likelihood surface intuition 


#### Time-series / sequential modeling 
- Hidden Markov Models 
- Kalman filter intuition 
- AR / autoregressive intution 
- regime shift 

#### Tree models 
- Random forest 
- Boosting

#### Non-parametric models
- Kernel density estimation 
- SVM intuition 
- Gaussian Processes 

#### Model Deep Learning 
- RNN / LSTM 
- Transformer
- ConvNets 

#### Others
- Neural ODEs
- GAN 
- Normalizing flows 
- Variational autoencoders
- Stochastic variational inference


Scratch Pad
- Naive Bayes
- Mixture of Gaussians 
- Logistic Regression
- Bayesian Linear Regression 
- Hiddent Markov Models 
- Factor analysis 
- Neural net classifier 
- Time series and recurrent models
    - LSTMs
    - RNNs 
- Transformers
- Convnets 
- Neural ODEs
- Variational Autoencoders
- GAN 
- Normalizing Flows
- Non-parametrics (kernel density estimation, Gaussian processes, SVM)
- Boosting
- Random forests 
- Expectation-Maximization
- stochastic variational inference 

🔥 MUST MASTER

Logistic regression

Bayesian linear regression

Regularization theory

EM + Gaussian mixtures

Naive Bayes

Hidden Markov Model intuition

Bias-variance + model selection

Random forest vs boosting

Neural network training dynamics

Gaussian Process intuition

If you know these deeply → you already look strong.

⭐ A Better Mental Structure

Instead of memorizing models, think in axes:

Axis 1 — Parametric vs Non-parametric

Linear / logistic → parametric

GP / KDE → non-parametric

Axis 2 — Discriminative vs Generative

Logistic regression → discriminative

Naive Bayes / MoG → generative

Axis 3 — Static vs Sequential

regression → static

HMM / RNN → sequential

Axis 4 — Deterministic vs Bayesian

OLS → deterministic

Bayesian regression → uncertainty modeling

This mental map is VERY interview powerful.

## Getting Started

```bash
pip install jupyter numpy
jupyter notebook
```
