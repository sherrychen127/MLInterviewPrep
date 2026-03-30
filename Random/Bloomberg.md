## Full Pipeline
```
Raw data ingestion
    ↓
NLP extraction (NER, events, sentiment)
    ↓
Indexing (search + embeddings)
    ↓
Retrieval (BM25 / vector search)
    ↓
Ranking (learning-to-rank model)
    ↓
User-facing features:
    - search
    - alerts
    - dashboards
    - recommendations
```

1. Multi-source data ingestion 
   1. raw article, transcript, filing, social/alt data, market data
   2. ASR for audio -> text 
   3. cleaning, language detect, de-dup prefilter (hashing)
2. NLP enrichment 
   1. NER entity extraction + entity linking 
   2. event extraction
   3. sentiment analysis (doc or aspect-level)
   4. document classification (labels: earnings / macro / sector / geopolitics)
   5. summarization (seq2seq)
   6. embedding generation (from BERT, finBERT, or sentence-transformer)
   7. dedup / near-duplicate detection (minHash + embedding cosine. Keep one canonical doc + cluster IDs)
   8. clustering / topic grouping 
   9. anomaly detection (time + text signals)
3. Storage (multi-index)
   1. object store: raw text
   2. structured DB: entities, events, sentiment, labels, timestamps
   3. search index - keyword (inverted index, like Elasticsearch)
   4. vector index - embedding (FAISS / ANN index)
   5. Graph/lookup: entity registry (tickers, aliases)
4. Retrieval pipeline 
   1. query understanding
      1. NER on query -> TSLA
      2. intent detection -> earnings + time filter
   2. Hybrid retrieval - combine all candidates
      1. keyword search - look for exact matches
      2. structured filtering 
      3. vector retrieval - find nearest neigbbors
5. Ranking
   1. Features:
      1. keyword match score
      2. embedding similarity
      3. recency
      4. entity match
      5. sentiment
   2. Model:
      1. LTR (GBDT or neural)

| Method     | Strength      | Weakness       |
| ---------- | ------------- | -------------- |
| BM25       | precise, fast | no semantics   |
| Embedding  | semantic      | weak filtering |
| Structured | exact filters | no fuzziness   |


>“Embeddings are used for semantic recall, but structured signals like entities and timestamps are required for precision filtering and explainability.”

```
Raw text
  ↓
[NER] → entities
[Event extraction] → structured events
[Sentiment] → signal
[Embedding] → semantic vector
  ↓
Stored in:
  - structured DB
  - search index
  - vector index
  ↓
Retrieval:
  - keyword (BM25)
  - filters (entity/time)
  - embedding similarity
  ↓
Ranking model (LTR)
  ↓
User
```


```
                ┌────────────────────────────┐
                │     Data Sources           │
                │  News | Filings | Audio    │
                └────────────┬───────────────┘
                             ↓
                ┌────────────────────────────┐
                │   Ingestion & Cleaning     │
                │  ASR | Dedup (hashing)     │
                └────────────┬───────────────┘
                             ↓
        ┌──────────────────────────────────────────────┐
        │            NLP Enrichment Layer              │
        │  NER | Entity Linking | Event Extraction     │
        │  Sentiment | Classification | Summarization  │
        │  Embeddings                                 │
        └────────────┬─────────────────────────────────┘
                     ↓
   ┌─────────────────────────────────────────────────────────┐
   │                     Storage Layer                       │
   │  Object Store (raw text)                               │
   │  Structured DB (entities, events, sentiment, time)     │
   │  Inverted Index (BM25 search)                          │
   │  Vector Index (embeddings / ANN)                       │
   │  Entity Graph / Lookup (TSLA ↔ Tesla ↔ aliases)        │
   └────────────┬────────────────────────────────────────────┘
                ↓
        ┌────────────────────────────┐
        │     Query Understanding    │
        │  NER + intent + filters    │
        └────────────┬───────────────┘
                     ↓
        ┌────────────────────────────┐
        │     Retrieval Layer        │
        │  BM25 + Filters + Vectors  │
        └────────────┬───────────────┘
                     ↓
        ┌────────────────────────────┐
        │   Ranking (LTR model)      │
        │  (NDCG / CTR optimized)    │
        └────────────┬───────────────┘
                     ↓
        ┌────────────────────────────┐
        │   Output / UI Layer        │
        │  Results + Summaries       │
        │  Alerts + Analytics        │
        └────────────────────────────┘
```

### NER (Named Entity Recognition)

Classic:
- CRF,HMM (rare)

Modern:
- Transformer + token classification head 
- BERT / RoBERTa
- Finance-tuned variants (FinBERT)

```
Input:  "Tesla reported strong earnings"
Output: [B-ORG, O, O, O, O]
```


Metrics:
- Token-level: Precision/recall/F1
- Entity-level: Exact match F1 


### Event Extraction
Model:
- Two-stage
  - NER
  - Relation / event classifier 
- Modern:
  - Transformer-based sequence labeling
  - QA-style extraction (prompting)
  - Structured prediction heads

```
Input:  "Tesla acquired X for $2B"
Output:
  event = acquisition
  acquirer = Tesla
  value = $2B
```

Metrics:
- Trigger detection F1
- Argument extraction F1 
- End-to-end event F1

### Sentiment analysis:
Model:
- Fine-tuned transformer
- BERT/FinBERT

Metrics:
- Accuracy 
- F1
- ROC-AUC 

> Class imbalance - neural dominates


### Embedding Models
Model:
- Sentence transformers (SBERT-style)
- Dual encoder (query/doc encoders)
- Contrastive learning models 

```
doc_vec = encoder(article)
query_vec = encoder(query)
```

Metrics:
- Recall @ K
- MRR (Mean Reciprocal Rank)
- NDCG (ranking quality)

```
Recall@10 = % of queries where correct doc is in top 10
```

### Ranking Models

offline metrics:
- DNCG @ K
- MAP
- MRR

online metrics:
- CTR (click-through rate)
- dwell time 
- user engagement

>“Ranking is evaluated offline with NDCG and online with user engagement metrics like CTR.” 

### Topic modeling / clustering 

### Summarization 
Used for:
- earnings call summaries
- news briefs

Models:
- seq2seq (T5, GPT-style)