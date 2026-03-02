

## CNN
##### Q: Why is convolution better than fully connected for images
Convolution exploits spatial structure via:
- local connectivity -> captures local patterns 
##### Q: CNN vs Vision Transformer 
CNN:
- strong inductive bias 
- data efficient
- good for small dataset
- faster at high resolution

ViT:
- global attention
- better scaling with data
- more flexible representation

Attention is $O(N^2D)$


#### Output size for a conv layer:

$$output = \frac{W - F + 2P}{S} + 1$$
- $W$ input size
- $F$ filter size
- $P$ padding
- $S$ stride

#### Receptive field
Receptive field of a neuron is the region of the input that can influence that neuron's activation
- early layer -> small receptive field (edges, corners)
- deeper layer -> large receptive field (objects, context)

For each layer $l$ define:
- kernel size: $k_l$
- stride: $s_l$
- dilation: $d_l$
- receptive field: $r_l$
- jump (effective stride): $j_l$

For each layer, effective kernel:
$$k_l^{eff} = d_l(k_l - 1) + 1$$
Update rules:
$$r_l = r_{l-1} + (k_l^{eff} - 1)j_{l-1}$$
$$j_l = j_{l-1}\cdot s_l$$

Examples:
| conv=3x3 | RF | 
|----------|----|
| input    | 1  |
|after 1 conv| 3|
|after 2 conv|5|
| ...| ...|
|after n conv| 2n+1|

increase by $(k-1)$


#### Number of parameters in a conv layer
$$Params = k^2 C_{in}C_{out}$$

#### Number of multiplication
output feature map = $H_{out}\times W_{out} \times C_{out}$
$$Mult = H_{out}W_{out}C_{out}k^2 C_{in}$$

---

## Transformer
Self-attention computes pairwise similarity between all tokens:
$$QK^T => N\times N$$
So complexity is $O(N^2D)$. Common ways to reduce:
- windowed attention (Swin)
- sparse attention
- linear attention
- low-rank approximation
- token pooling / patch merging

---
## Object Detection

#### Q: What is NMS
Non-Maximum Suppression removes duplicate overlapping detections by:
1. sorting by confidence
2. keeping highest score
3. suppressing high-IoU neighbors

Needed because anchor-based detectors produce many overlapping boxes

---
## Metrics and Evaluation
- IoU
- Precision
- Recall
- mAP -> area under prevision-recall curve across classes



---


## Domain Shift
- data augmentation / domain randomization
- fine-tuning on target domain
- feature alignment
- self-training
- test-time adaptation

### Feature Alignment
Explicitly force: 
> souce features ~= target features
1. **Adversarial alignment** (DANN)
    1. Add a domain classifier. The intuition is that feature extractor tries to confuse domain classifier
    2. `feature → domain head → predict source vs target`
    3. Loss `L = task_loss - λ * domain_loss`
2. **MMD Loss**
    1. Minimize distribution distance directly
    2. `L_align = MMD( f(source), f(target) )`
3. **CORAL Loss**
    1. Match covariance statistics
    2. `L = || Cov_s - Cov_t ||^2`


### Self-training
```
train on source
    ↓
predict on target
    ↓
keep high-confidence predictions
    ↓
retrain model
```


