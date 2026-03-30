# ML Modeling Template

## Step 1 - Problem Type
- regression -> predict return / price / spread
- classification -> up/down/trade/no-trade
- ranking -> which signal /asset to prioritize
- time-series forecasting -> next step prediction 
- anomaly detection -> unusual market behavior
- reinforcement learning -> sequential trading policy 


## Step 2 - Understand data properties
- tabular vs sequence vs graph
- iid vs temporal dependency 
- stationary vs regime shift 
- noisy labels
- imbalance


## Model Selection 

### Tabular Prediction 
- predict next minute return 
- predict whether signal profitable
- predict execution slippage 
- predict fill probability 
- risk model/default probability 

**Gradient Boosted Trees (XGBoost / LightGBM)**
- Tabular features
- nonlinear interactions
- missing values
- small-medium data
- low-latency needed

Feature engineering examples:
- rolling mean return 
- rolling volatility 
- order book imbalance
- volume delta
- lagged features
- cross-asset spreads
- time-of-day encoding


```
r_t-1, r_t-5, r_t-20
volatility_30s
bid_ask_spread
depth_ratio
signal_strength
```


**Loss selection**
| Task            | Loss          |
| --------------- | ------------- |
| regression      | MSE / Huber   |
| classification  | LogLoss (BCE) |
| heavy noise     | Huber         |
| tail risk focus | Quantile loss |

**Best practices**
- Purged CV / Time-series split
- no random shuffle
- prevent lookahead leakage
- use walk-forward validation

### Linear Models
- factor model
- signal combination 
- explianability requirements 
- very low latency strategy

**Models:**
- Linear regression 
- Ridge / lasso regression
- Logistic Regresion 

**Feature Selection Practices**
- correlation pruning
- L1 regularization 
- PCA/factor decomposition
- domain knowledge filtering 

**Loss**
- MSE
- LogLoss


**Q: Why linear model?**
- stable
- interpretable coefficients
- robust under regime change
- less overfitting

### Time Series Deep Learning 
- multi-step price prediction
- volatility surface modeling
- order flow prediction 

**Models**
GRU/LSTM
- sequential dependency
- moderate dataset size
- want stability 

Transformer
- long horizon dependency 
- multi-asset relationship
- high dimensional signals 



#### Example: “How would you model short-term return prediction?”

1. Start with tabular formulation
2. build lagged + microstructure features
3. LightGBM baseline
4. walk-forward validation
5. optimize LogLoss / Sharpe proxy
6. evaluate feature stability
7. try GRU if sequential signal suspected
8. monitor turnover + pnl

