

## Traditional Object Detection
- Faster R-CNN
- YOLO
- SSD
- RetinaNet

### Core Idea: Anchor-based detection 
Anchor-based detectors turn object detection into:
> Dense classification + box regression over a grid (x, y, w, h, class)

Instead of saying "find objects" they say: 
> At every location (grid cell) in a feature map, predict whether an object exists and how to adjust a predefined box anchor + offset

Anchor provides strong prior on shape 




#### Step 1: CNN Backbone
Use ResNet to extract feature map 

#### Step 2: Anchor Boxes
At each spatial location, predefine multiple anchor boxes:
- different scales
- different aspect ratios

So total anchors: `HxWx9`

#### Step 3: For each anchor predict
- Objectness score
- Bounding box offset
- Class probabilities 

**Training**:
- Match GT boxes to anchors (IoU-based heuristics matching)
    - Heuristics matching to assign GT boxes to anchors
- Assign positive/negative anchors
- Handle extreme class imbalance
- Tune NMS thresholds

**Inference**:  
- Model predict MANY overlapping boxes -> need
- Non-maximum suprresion (NMS) - only applied at inference!
    - Remove overlapping boxes
    - Keep highest confidence

*NMS is non-differentiable and greedy and not learnable!*

#### Limitations
- Anchor hyperparameters
- Heavy post-processing (NMS)
- Duplicate predictions
- Complex matching heuristics
- Dense predictions -> many negatives

---
## Anchor-free CNN detector

- FCOS
- CenterNet

Instead of predefined boxes, they predict:
- Distance to left/right/top/bottom edges
- Object centers

---

## DETR - Detection Transformer
Treat object detection as a set prediction problem using transformers 

No anchors, NMS, 

#### Step 1. CNN backbone
Image -> feature map 

```
800x800 image
↓
ResNet
↓
25x25x2048 feature map
```

#### Step 2. Flatten + Positional Encoding
Feature map -> sequence of tokens
$$25×25=625 tokens$$

Each token is a 2048-d vector.
Add positional encoding

#### Step 3. Transformer Encoder
Self-attention across all spatial locations. Now each token:
- Sees entire image
- Has global context

#### Step 4. Object queries
Instead of anchors, DETR uses **N learnable object queries**  
$$Q = \{q_1, q_2, q_3..., q_N\}, \quad q_i \in \mathbb{R}^d$$

Each query is: 
- A learnable embedding vector
- Same dimension as transformer hidden size
- Randomly initialized
- Updated by backprop

Eventually:
- Some queries learn to detect large objects
- Some learn small ones
- Some specialize in certain spatial patterns

DETR outputs a fixed number N of predictions:
- N = 100 queries
- Image has 7 objects 

7 queries get matched to real objects, 93 are matched to no object class. This is handled by Hungarian matching

#### Step 5. Transformer Decoder
Each query:
- cross-attends to encoder output
- computes with other queries via self-attention

So decoder does:
- Query self-attention
- Query -> image cross-attention

Each query becomes an object hypothesis

#### Step 6. Prediction Heads
For each query output:
Two MLP heads:
1. Class head -> (C+1) logits
2. Box head -> 4 values (normalized box)

#### Hungarian Matching 
During training: 
- Model outputs fixed N predictions
- Gound truth has M objects

Compute bipartite matching using hungarian algorithm with cost:
- Classification loss
- L1 bbox loss
- GIoU loss

This ensures one-to-one matching, no duplicates, and hence no need for NMS.  This create competition between predictions (KEY DIFFERENCE)
- Only one query gets assigned to the label - Only one wins
- The other are assigned to "no object" - gets penalized

Difference

1. Traditional - dense prediction, predict at every pixel location
2. DETR -> Direct set prediction, predict fixed N objects 

#### DETR-based models
- Deformable DETR
- DINO

#### Limitations
- slow convergence
- small object performance

## Deformable DETR 
Detection needs:
- Small objects
- Large objects

CNN naturally builds hierarchical features.
Original DETR struggled with small objects because it used single-scale features.
That’s why Deformable DETR added multi-scale attention.


**Q: Difference between CNN and Transformer**  
When we say CNN detection is local, we mean: each prediction head is tied to a spatial grid location

You have a feature map `HxWxC`
At each `(i, j)` location:
- You predict K anchors
- Each anchor is centered at that location

So prediction is tied to a spatial grid cell $P(i, j, k)$ means that prediction is responsible for objects around that region. Thats local in a structural sense. You can't directly regress arbitrary box from a grid cell:
- That grid cell has limited context
- may not see the full object cleanly
- doesn't know which object to choose if multiple overlap


When we say DETR is global, we mean:
Each object query can reason over the entire image without being tied to a specific spatial coordinate 

In DETR, you do not predict per spatial location, each query outputs one box, $P(query_i)$
$$box = MLP(query_output)$$




---

## Domain Shift
#### Data-level techniques
1. Heavy augmentation
3. Domain randomization

#### Feature-level techniques
1. Domain adversarial training
3. Style transfer
3. Feature alignment losses
4. Self-supervied pretraining


#### Fine-tuning strategy
1. Freeze backbone
2. Finetune head

#### Test-time adaptation
1. BatchNorm adaptation
2. Entropy minimization


## Data Imbalance
#### Loss-level solutions
1. Focal loss
2. Class-weighted cross entropy
3. GIoU/DIoU losses

#### Sampling
1. Oversample
2. Hard example mining
3. Balance mini-batch

#### Architectural
1. Separate heads per class
2. Class-specific anchors