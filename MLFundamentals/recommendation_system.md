## Recommender system


A recommender system tries to learn a function:
$$score(user, item)$$

## Collaborative Filtering
> If two users behaved similarly before, they will like similar things. 

- User-based: find users similar to you
    - use cosine similarity
- Item-based: find items similar to what you liked. 


User-item interaction matrix

| User / Movie | Interstellar | Inception | Titanic | Avatar |
| ------------ | ------------ | --------- | ------- | ------ |
| User1        | 5            | 4         | ?       | 1      |
| User2        | 5            | ?         | 2       | 1      |
| User3        | ?            | 5         | 4       | ?      |
- rows -> users
- columns -> items
- values -> ratings / clicks / views  

Usually the matrix is very sparse. Example in Netflix: `100M users x 10k movies`

#### User-based Collaborative Filtering
> Find users similar to the target user

Prediction: weighted average of neighbors:
$$\hat{r}_{u,i} = \frac{\sum_{v\in neighbors} sim(u, v) \cdot r_{v,i}}{\sum|sim(u, v)|}$$

#### Item-based Collaborative Filtering
Instead of finding similar users, we find similar items. This is used more in practice. 

Item Similarity is calculated as the cosine similarity between items . 

$$\hat{r}_{u,i} = \frac{\sum_{j\in items likes} sim(i, j) \cdot r_{u,i}}{\sum|sim(i, j)|}$$

#### Limitations
1. Cold-start Problem: 
    - Problem:
        - New user -> no history
        - New item -> no ratings
    - Solution:
        - Content-based features
        - hybrid models
2. Scalability
    - User-user similarity is expensive. 100M users will have `100M x 100M` similarity matrix
3. Sparsity
    - Most entries are missing. E.g. `99.9% empty matrix`. Hard to compute similarity


## Matrix Factorization
Instead of comparing users directly, we learn latent embeddings.
> Users are close to items they like in embedding space

User-item interaction matrix:
$$R_{user, item}$$

We factorize into:
$$R \approx U \times V^T$$

- U = user embeddings
- V = item embeddings

Score:
$$score(u, i) = U_u \cdot V_i$$

Loss Function:
$$\sum (r_{ui} - U_u^TV_i)^2$$
or with regularization:
$$\sum(r_{ui} - U_u^TV_i)^2 + \lambda(\|U\|^2 + \|V\|^2)$$

#### Limitations:
It only uses interaction data, and can not easily incorporate rich features such as metadata for users and items. 


## Two-Tower Model
Its neural collaborative filtering, where user and item embeddings are learned by neural networks 
> Learn embeddings for users and items so that we can quickly find similar ones. 

Then recommendation becomes a nearest neighbor search problem. 

```
User Tower        Item Tower
   ↓                 ↓
User Embedding   Item Embedding
        ↓
   Dot Product
        ↓
     Ranking
```

User features:
- watch history
- demographics
- clicks

Item features:
- video embedding
- category
- text
- image

The training objective is to make user embeddings close to positive items and far from negative items. 

Common loss: `contrastive loss` or `softmax cross-entropy loss`


#### Q: Why two towers?
We can precompute item embeddings and store in a database
```
item → item tower → item embedding
```

At runtime, we compute user embedding and retrieve nearest items
```
user → user tower → user embedding
```

#### Q: Why can't two towers be used for ranking?
User and item embeddings are computed independently. So the model can not model complex interactions. 

For example, if user likes `action movies starring Tom Cruise`, the interaction `action AND Tom Cruise` is hard to model. 

Ranking models fix this by using cross features. 
```
score = f(user_features, item_features)
```

So ranking models are more expressive but expensive for retrieval. 

## Graph Neural Networks
Some systems model the recommendation system as a graph. GNN propagate signals across this graph
```
user → clicked → item
item → similar → item
item → category → node
```

Basic idea:
```
item embedding = aggregate embeddings of neighboring items
```


### Retrieval with ANN 
ANN (Approximate Nearest Neighbor) is not a recommendation model, it's a fast vector search algo. It works whenever you have embeddings. 

Brute force approach of finding nearest item vector is too slow, since it compute similarity for every item. 


ANN structures the vectors so that nearby vectors are grouped together in layers of graphs. The search process follow graph connections instead of checking all vectors. The runtime is $O(Log(N))$ instead of $O(N)$

Common libraries used:
```
FAISS (Meta)
ScaNN (Google)
HNSWlib
Annoy
```

ANN builds a **special index** that makes search fast. 
```
item embeddings
     ↓
ANN index (FAISS / ScaNN)
```

at runtime: 
```
user embedding
     ↓
ANN search
     ↓
top 1000 nearest item embeddings
```
This makes retreival extremely fast

```
User request
     ↓
User features
     ↓
User tower
     ↓
User embedding
     ↓
ANN search over item embeddings
     ↓
~1000 candidates
     ↓
Ranking model
     ↓
Top recommendations
```


| Feature     | Matrix Factorization | Two-Tower      |
| ----------- | -------------------- | -------------- |
| Embeddings  | learned              | learned        |
| Model       | linear               | neural network |
| Input       | user_id, item_id     | rich features  |
| Cold start  | poor                 | better         |
| Flexibility | limited              | very flexible  |
| Used today  | rarely alone         | very common    |


## Explicit vs Implicit Feedback
Explicit: user gives rating
```
5 stars
4 stars
```

Implicit: user behavior
```
click
watch
purchase
like
```

## Full Production Pipeline

```
User Activity
     ↓
Candidate Retrieval
     ↓
Collaborative Filtering
     ↓
Ranking Model
     ↓
Top-K recommendation
```

### 1. Candidate Generation
It's the first stage of a large-scale recommendation system. Its job is to quickly narrow down millions of items to a few hundred or thousand candidates that are likely relevant to a user. Real systems have millions or billions of items, and you can not score every item with a complex ranking model. 

This stage is optimized for speed an recall, not perfect ranking. These candidates are not ordered well yet 

- Two-tower retrieval (most common)
- ANN searh
- Collaborative filtering retrieval
- Popularity-based retrieval (Trending items)
- Content-based retrieval
- Graph-based retrieval (e.g. PinSage)

### 2. Ranking
After candidate retrieval, use heavier ranking models to score each candidate. In ranking stage optimizes for `precision`

Ranking stages uses much richer features than retrieval.

User features:
- history
- demographics
- preferences

Item features
- popularity
- embeddings
- metadata

Context featrues
- tiem of day
- device
-location

The ranking model predicts:
```
p(user interacts with item)
```

Model examples:
- Gradient Boosted Trees 
    - XGBoost
    - LightGBM
- Deep Neural Networks
    - Wide & Deep
    - DLRM
    - Transformer ranking models

Output: top 10 recommendations

### 3. Re-Ranking 
Re-ranking is a final adjustment step after ranking. 
The goal is to improve diversity, fairness, freshness, and business constraints. 

For example, ranking might produce 4 cooking videos, but re-ranking might enforce the videos are in different domains. 

Re-ranking adjustments:
- Diversity: avoid recommending very similar items 
- Freshness: prefer new content
- Business constraints: limit ads, promote sponsored content/new creators

Common approaches:
- Rule-based
- Greedy optimization
- Multi-objective ranking
- Reinforcement learning

#### Q: Why separate ranking and retrieval? 
Because ranking models are expensive
```
100M items
 ↓
retrieve 1000
 ↓
rank 1000
 ↓
return 10
```

#### Q: What is the cold start problem 
Cold-start happens when theres new user and new item with no interactions. 

New item cold start: 
- use content features such as text embedding, image embedding. Item tower can still produce embedding

New user cold start:
- use demographics, location, device
- also use exploration strategies

