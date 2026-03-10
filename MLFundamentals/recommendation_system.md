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


## ML System Design - Recommendation System 
1. goal and product
2. high-level architecture
3. candidate retrieval
4. ranking
5. re-ranking
6. training data / labels
7. cold start
8. evaluation
9. scaling / serving
10. tradeoffs and follow-ups

#### 1. Define product goal 
> I’d first clarify the recommendation surface and the objective, because home feed, related items, and notifications may use different models.

Examples surfaces:
- YouTube home feed
- YouTube Up Next
- Pinterest home feed
- Pinterest related pins
- Pinterest search recommendations

> For Pinterest or YouTube, I would optimize not just immediate clicks but a longer-term engagement metric such as save rate, watch time, or return probability, because pure CTR can lead to clickbait.

Possible Objectives:
- click-through rate
- save rate
- watch time 
- long-term retention
- session length
- downstream conversion

#### 2. High-level system architecture  
```
All items
  ↓
Candidate retrieval
  ↓
~1K candidates
  ↓
Ranking
  ↓
~50 items
  ↓
Re-ranking
  ↓
Final feed
```
> Because we may have hundreds of millions or billions of items, we cannot score everything with a heavy model online. So we use a multi-stage system: fast high-recall retrieval, then more precise ranking, then a light re-ranking layer for diversity and business constraints.

#### 3. Candidate retrieval
The goal is fast and high recall. Typical sources are parallel candidate generators

- Two-tower embedding retrieval 
    - user tower produces user embedding
    - item tower produces pin/video embedding
    - retrieve nearest items using ANN 

- Collaborative / co-occurence retrieval
    - users who engaged with X also engaged with Y 
    - item-to-item graph or co-save/co-watch stats

- Content-based retrieval
    - text similarity
    - image similarity
    - topic/category similarity

- Popularity / trending retrieval

- Graph-based retrieval
    - user-pin graph
    - pin-board graph
    - board-topic graph
    - random walk or GNN-derived embeddings

> I would union candidates from multiple sources because each source covers different failure modes. Embedding retrieval captures semantic similarity, collaborative signals capture user behavior, content-based methods help with cold start, and popularity sources provide safe fallback coverage.

#### 4. Retrieval model details
Two-tower:
- item embeddings can be precomputed offline
- online we only compute the user embedding
- ANN makes retrieval fast

At training time:
- positives: watched / clicked / saved / purchased items
- negatives: sampled unengaged items, hard negatives, in-batch negatives

```
user features → user tower → user embedding
item embeddings stored in ANN index
ANN returns top-K nearest items
```

#### 5.Ranking stage
User features 
- long-term interests
- recent session behavior
- embeddings from history 
- demographics
- follow graph
- prior engagement rates

Item features
- item embedding
- topic/category
- text/image/video features
- freshness
- creator quality 
- popularity stats

User-item cross features
- similarity between current session and item 
- user has engaged with this creator before
- overlap with recent interests
- user tends to save this category at night 

Context features
- tiem of day
- device
- location

> Retrieval uses separable encoders for speed, while ranking uses a richer joint model over user, item, and context so it can learn cross-feature interactions that a dot-product retrieval model cannot represent well.


#### 6. Re-ranking stage
So re-ranking adjusts the ranked list for constraints like:

- diversity
- freshness
- creator fairness
- spam / quality filtering
- exploration
- business rules

> The ranker optimizes predicted engagement per item, while the re-ranker optimizes list-level quality.

New item cold start
- content-based embeddings
- creator features
- topic/category features
- boost small amounts of exploration traffic
- estimate priors from similar items 

New user cold start 
- onboarding interest selection 
- locale/device/time/popularity priors
- trending content
- follow graph
- exploration to quickly gather first signals

#### 8. Graph-based retrieval
Pinterest is naturally graph-shaped:
- users save pins
- pins co-occur on boards
- users follow boards and creators
- pins connect to topics

So we can build a graph where nodes are users, pins, boards, creators, topics. 
- random walks find related neighborhoods
- GNNs aggregate neighbor information
- learned graph embeddings capture structural similarity

> If two pins are often saved to similar boards by similar users, they should end up near each other in embedding space even if they do not look visually similar.

#### 9. Training labels and objectives
Retrieval labels:
- positive = watched/clicked/saved item
- negative = sampled non-engaged items 
- objective = contrastive / sampled softmax / pairwise ranking loss 

Ranking labels:
- click
- long click / dwell time
- save
- share
- hide/skip
- watch time 

Then combine into a weighted objective 
$$score = w_1 \cdot P(click) + w_2 \cdot P(save) + w_3 \cdot E(watchtime)$$

Or train a multi-task model and combine at serving. 

#### 10. Evaluation 
Offline retrieval metrics
- Recall@K
- Hit Rate@K

Offline ranking metrics
- NDCG
- MAP
- AUC
- Precision@K

Online metrics
- CTR
- save rate
- watch time
- dwell time
- hide rate
- retention length

> Offline metrics help iterate quickly, but final quality must be validated with online A/B tests because recommendation systems have strong feedback loop effects.

#### 11. Feedback loops abd exploration

Problem:
- recommender only shows popular things
- gets more engagement
- becomes even more dominant
- long-tail items never get exposure

Solutions
- exploration buckets
- bandit methods
- freshness boosts
- randomized exposure
- debias training with logged propensities if needed

