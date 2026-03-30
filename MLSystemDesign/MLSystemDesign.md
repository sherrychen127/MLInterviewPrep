
### Data Pipeline

### Model Training

### Model Serving
Serving predictions in production requires a robust architectrue that can handle high concurrency and low latency. 

client -> load balancer -> inference API -> Feature store/feature cache layer -> inference cache -> Model server -> Logging store 

--- 
## ML System Design Template
Problem clarification
  1. who are the users?
  2. what is the input and output?
  3. real-time or batch?
  4. how many requests per second?
  5. latency target?
  6. accuracy/business metric?
  7. freshness requirement?
  8. is explainability important?
  9. what happens if model is unavailable?

Functional requirements
  1. ingest articles/events/documents
  2. generate predictions or ranked results
  3. log user feedback
  4. update features/models
  5. support admin/model rollout/monitoring


Non-functional requirements:
  1. latency: p95 under 200ms
  2. throughput: 10k QPS
  3. availability: 99.9%
  4. durability of logs
  5. fault tolerance
  6. scalability
  7. freshness
  8. security/compliance

High-level architecture

Data flow

ML design
  1. training data
  2. features
  3. candidate generation vs ranking
  4. offline vs online inference
  5. model deployment
  6. feedback loop 
  7. monitoring 


Storage strategy
   1. SQL vs NoSQL
   2. cache
   3.  object store
   4.  feature store
   5.  search index / vector DB if needed
   6.  sharding/replication/partitioning


Reliability and failure handling
  1.  retries
  2.  timeout
  3.  circuit breakers
  4.  fallback logic
  5.  dead-letter queues
  6.  graceful degradation


Testing strategy
   1.  unit
   2.  integration
   3.  load
   4.  shadow/canary
   5.  offline evaluation
   6.  A/B test
   7.  data quality tests 


## Case Study: Visual Search System
### 1. Problem Definition

A system helps users discover images that are visually similar to a selected image. 

Visual search = given an image, find:
- the same item 
- similar items 
- items matching a region/object inside the image

> I will define the product goal, success metrics, data pipeline, model architecture, retrieval/ranking design, serving system, and how I'd handle scale and quality
### 2. Requirements
#### Product questions:
- for e-commerce, general web image search, or internal catalog retrieval
- exact match or similar style search 
- is the query:
    - full image only?
    - image + text? 
    - cropped region? 
- are results retrieved from:
    - a fixed product catalog?
    - all indexed images?
- do we need real-time freshness

#### Constraints:
- latency target? p50 < 100ms, p99 < 300ms
- catalog size?
- traffic? Queires per second? 
- precision v.s. recall? 

### 3. Define the system objective
There are usually two stages. The overall pipeline is:
```
query image -> preprocess -> query encoder -> embedding -> ANN retrieval -> top-K candidates -> ranking / re-ranking -> results
```

#### Retrieval 
Map query image to an embedding and retrieve nearest neighbors from a large index. 

#### Ranking/re-ranking
Use a more expensive model to reorder candidates. 

### 4. Core architecture
#### Offline pipeline
1. ingest catalog images
2. preprocess / crop / normalize 
3. run item encoder to generate embeddings 
4. store embeddings in vector index
5. store metadata in feature / document store 

#### Online query pipeline
1. user uploads image
2. detect main object / region if needed
3. encode image to embedding
4. retrieve nearest neighbors from ANN index 
5. re-rank with richer features 
6. filter by availability / policy / locale 
7. return results 

### 5. ML approach 
Use a CNN or ViT-based encoder to map images into embedding space. 
- ResNet
- EfficientNet
- Vision Transformer
- CLIP-like image encoder 

> visually similar items should have embeddings close together, dissimialr items far apart

### 6. Training objective
The most common setup is metric learning 

Common losses:
- contrastive loss
- triplet loss
- InfoNCE / softmax contrastive loss
- ArcFace / proxy losses sometimes

Training example:
- query image
- positive example
    - same SKU with different viewpoints 
    - same product
    - user-clicked purchased similar product 
- negative examples
    - random negatives
    - hard negatives from same category
    - visually similar but wrong item 

> “I’d likely use metric learning with hard negative mining, because retrieval quality depends heavily on embedding separation among visually similar items.”

### 7. Data strategy
Data sources
- proeduct catalog images
- multi-view item photos
- user query -> click logs
- add-to-cart / purchase log 
- text metadata: title, brand, category, attributes 

Labels:
- exact match 
- same SKU
- same style
- same categoy 

### 8. Feature / representatio ndesign 

1. Image-only system: embed only pixel 
    1. bootstrap
    2. pure visual similarity
2. Multi-modal system: use image + text metadata 
    1. image encoder
    2. text encoder
    3. fused or aligned embedding space

>“For catalog items, I’d consider multimodal embeddings because pure vision may confuse semantically distinct products. At serving time, query may be image-only, but catalog embeddings can still benefit from text supervision.”

### 9. Retrieval system design 
Use ANN search. Much faster, slight recall loss. 

### 10. Ranking / re-ranking 

Ranker inputs:
- embedding similarity 
- category match 
- branch match 
- price distance 
- text similarity 
- popularity / CTR / conversion
- inventory / availability 
- freshness 
- user context 

Ranker models:
- gradient boosted trees
- MLP
- cross-attention multimodal model 
- learning-to-rank models

> “Retrieval optimizes recall under latency constraints; re-ranking optimizes top-K precision using richer features.”

### 11. Metrics
Offline metrics:
- Recall@K
- Precision@K
- mAP
- NDCG
- top-1 accuracy for exact match 
- embedding clustering quality 
- query latency 

Online metrics:
- CTR 
- add-to-cart rate 
- conversion rate 
- revenue per search 

### 12. Handling object detection 
User uploads a scene image with multiple objects 
- object detector 
- crop region
- encode crop 
- retrieve matchs 

### 13. Cold start and long-tail issues 
- some items have few images or little interaction data 
- rely on image encoder pretraining
- use text metadata supervision 
- augment with synthetic transformation 
- hierarchical retrieval: category first then fine-grained
- use foundation-model embeddings as initialization 

For rare classes:
- balanced sampling
- hard negative mining within category 
- class-aware training 


### 14. Freshness and indexing
- nearline pipeline for new items 
- daily or hourly embedding generation 
- index delta updates 
- blue/green rollout for new indices

### 15. Scalability / system design points 
Storage:
- image store 
- metadata DB 
- feature store 
- vector index 

Compute:
- GPU batch inference offline for catalog embeddings 
- CPU/GPU online encoder depending QPS/latency 
- caching for popular queries 

Reliability:
- cached results for frequent queries 
- fallback to category/text search if vision fails 

Multi-region:
- replicate AN indices
- local serving for lower latency 

### 16. Personalization
> “I’d usually separate visual relevance from personalization. First retrieve visually relevant candidates, then apply lightweight personalized re-ranking.”

### 17. Common challenges and tradeoffs
1. Exact match vs similar match 
2. Latency vs accuracy 
3. background noise 

--- 

## Case Study: Recommendation System 
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






## Case Study: Personalized News Recommendation 

The problem statement is to design a system that recommends relevant financial/news articles to each user. 

**Requirements**
Functional requriements:
- ingest articles from publishers/wires
- maintain user profiles and reading history
- rank candidate articles for a user
- return top K articles quickly
- log impressions/clicks/dwell time 
- retrain/update models periodically
- handle cold start for new users and new articles 

Non-functional requirements:
- p95 latency under 200ms
- high read traffic, 20k+ QPS at peak
- freshness
- 99.9% availability
- graceful degradation
- support A/B experiments 

**Data flow**
Offline / ingestion path
1. articles arrive from feeds/publishers
2. content is cleaned, enriched, tagged by topic/ticker/entity 
3. 


```
Article Ingestion ---> Article Store / Index ----\
                                                  \
User Request --> LB --> Reco Service --> Candidate Retrieval --> Ranker --> Response
                     |         |                |                |
                     |         |                |                --> Model Server
                     |         |                --> Cache
                     |         --> User Profile / Feature Store
                     |
                     --> Impression/Click Logs --> Kafka --> Stream/Batch Pipeline --> Training
```

## Comparison


| System                                      | Query / Input                          | Retrieval Method                                  | Ranking Signals                                       | Labels / Training                      |
| ------------------------------------------- | -------------------------------------- | ------------------------------------------------- | ----------------------------------------------------- | -------------------------------------- |
| **Visual Search**                           | Image query                            | ANN over **image embeddings**                     | Visual similarity                                     | Image similarity pairs                 |
| **Video Recommendation**                    | User profile + watch history           | **Two-tower retrieval** (user → video) + trending | Watch time, CTR, recency                              | Implicit feedback (click, watch, skip) |
| **Event Recommendation**                    | User location, interests, time         | Two-tower or collaborative filtering              | Distance, time relevance, popularity, personalization | Event clicks / RSVPs                   |
| **Harmful Content Detection**               | Single content item (image/video/text) | **No retrieval stage**                            | Classification model outputs risk score               | Moderation labels                      |
| **Similar Listings (e.g., Airbnb, Amazon)** | Item ID                                | ANN over **item embeddings**                      | Metadata similarity (price, location, category)       | Co-view, co-click                      |
| **Personalized News Feed**                  | User reading history                   | Two-tower or candidate sources                    | Recency, diversity, CTR, user interests               | Click + dwell time                     |
