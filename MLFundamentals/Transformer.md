# Transformer

```
Input sentence → Encoder → context representation
                                 ↓
Target sentence → Decoder → output
```
Encoder:
- Bidirectional self-attention
- Sees entire input sequence at once 

Decoder:
- Masked self-attention (causal)
- Cross-attention to encoder outputs 

Most LLMs remove the encoder entirely with just stacked masked self-attention layers

Most Vision Transformers are encoder-only
```
Tokens → Masked Self-Attention Blocks → Next-token prediction
```

## Attention Mechanism
Given 
- Query ($Q$)
- Key ($K$)
- Value ($V$)

Attention is:
$$Attention(Q, K, V) = softmax(\frac{QK^T}{\sqrt{d_k}})V$$

1. Compute similarity $QK^T$
    1. This computes pairwise similarity between tokens
    2. Each entry = how much token $i$ attends to token $j$

2. Scale $\frac{QK^T}{\sqrt{d_k}}$
    1. dot products grow with dimension
3. Softmax $softmax(QK^T)$
    1. each token distributes its attention across all tokens 
4. Weighted sum of values $softmax(QK^T)V$
    1. Each token becomes a weighted sum of all value vectors - For each token, gather info from all other token weighted by similarity


Q, K, V are learned linear projections. 

Given input hidden states:
$$X \in \mathbb{R}^{Nxd_{model}}$$

We compute:
$$Q = XW_Q$$
$$K = XW_K$$
$$V = XW_V$$

Where:
- $W_Q$, $W_K$, $W_V$ are learnable weight matrices, shared across all inputs - this is like a convolution kernel where same filter applied everywhere
- each is size: $d_{model} x d_k$

> The model learns how to ask questions, describe content, and store information.

Each token embedding gets projected three different ways:
- Query - what am I looking for 
- Key - What do i contain 

**Q: Why not just use X directly?**
If we did: Attention(X, X, X)
Then:
- Similarity space = representation spac e
By learning separate projections:
- Similarity space != value space 
- Model can decide:
    - What features define relevance
    - What features define information content


## Self-Attention 
$Q$, $K$, $V$ come from the same input. So the model is attending to itself. 

Example:

```
The animal didn’t cross the street because it was tired.
```

When encoding "it", attention can focus on "animal". Self-attention lets model resolve long-range dependencies instantly. 

> Each element in a sequence computes a representation by looking at (attending to) all elements in the same sequence

```
[The, cat, sat]
```

Let's call their embeddings:
$$X = [x_1, x_2, x_3]$$
Self-attention means:
- We compute $Q, K, V$ from the same $X$
- Each token attends to other tokens in this same set 

So:
$$Q = XW_Q$$
$$K = XW_K$$
$$V = XW_V$$

Take token `cat`, we compute:
$$\text{attention weights} = \text{softmax}(q_2K^T)$$
- How much `cat` attends to `The`
- How much `cat` attends to itself 
- How much `cat` attends to `sat`

Then its new representation becomes:
$$y_2 = \sum_j(\alpha_{2j}v_j)$$

So `cat` becomes a weighted mixture of all tokens. 


## Multi-Head Attention 
Instead of doing one attention:
$$MultiHead(Q, K, V) = Concat(head_1, ..., head_h)W^0$$
Each head:
- Each layer and each head has its own set of weights
- Learns different relation
    - syntax
    - object identity 
    - positional pattern

## Cross-Attention
Now $Q, K, V$ come from different sources. Used in encoder-decoder models
- Encoder encodes english sentence
- Decoder generates French sentence

During decoding:
$$Q = \text{decoder hidden state}$$
$$K, V = \text{encoder outputs}$$
So decoder token attends to source sentence.

## Masked Attention 
Used in autoregressive models. We prevent token i from seeing future tokens.

So the attention matrix becomes:
$$QK^T + mask$$
where mask:
- upper triangle = $-\inf$

# Vision Transformer (ViT)
Treat image patches as tokens

#### Step 1. Patchify Image
Image `224x224x3`, split `16x16 patches`, so `196 patches`. Each patch:
- Flattened to vector 
- Projected to embedding dim 

Noe we have 196 tokens

#### Step 2. Add CLS token
Add one special token:
- [CLS]

Final sequence length is 197 tokens 

#### Step 3. Add Positional Embedding 
Because transformer has no spatial inductive bias, we add learned positional embeddings. 

#### Step 4. Apply Transformer Encoder 
Noe:
- Standard self-attention blocks 
Each patch attends to every other patch 


#### Step 5. Classification 