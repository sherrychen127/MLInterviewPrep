
# Anomaly Detection 
Existing work on cold-start, industrial visual anomaly detectino relies on learning a model of the nominal distribution via auto-encoding methods, Gaussian mixture models, GANs, or other unsupervised adaptation methods. 

- One-class SVM -> Learn boundary
- Autoencoder -> learn reconstruction
- PaDiM -> fit Gaussian
- GRAEM -> train discriminative model

## One-Class SVM
Unsupervised anomaly detection method that learns the region of feature space where normal data lives and flags anything outside as anomalous. This is one of the earliest and most important baselines in anomaly detection

Draw a boundary around normal data - anything outside is abnormal

Intuition:
- In normal SVM classification you have positive v.s. negative classes, and learn separating hyperplane
- In one-class SVM you only have normal data. The model tries to separate normal data from the origin (or from empty space). 

`normal points -> clustered cloud`

The objective becomes: **find the smallest region that contains most normal samples**
OC-SVM learns a boundary like:
- hypersphere 
- or more generally a curved surface (via kernel)

$$\min_{w,\rho,\varepsilon} \frac{1}{2} ||w||^2 + \frac{1}{vn}\sum_{i} \varepsilon_i - \rho$$


subject to:
$$w \phi(x_i) \geq \rho - \varepsilon_i, \quad \varepsilon_i \geq0$$

- $w$ defines the boundary
- $\rho$ offset that controls the boundary size
- $\epsilon_i$ slack variables that allow some points to be outside - because normal data isn't perfectly clean
- $v$ controls fraction of allowed outliers and boundary tightness - expected anomaly rate in training data

#### Decision function
After training, anomaly score is:
$$f(x) = w\phi(x) - \rho$$

#### Kernel trick
OC-SVM uses kernels such as RBF kernel. This allows learning nonlinear boundary around normal data. With RBF, boundary can wrap around complex shapes and much more flexible. 

**Q: What's kernel?**  
It lets you learn nonlinear decision boundaries without explicitly computing nonlinear features. 
Pretend we mapped data into high-dimensional space - without evever doing the expensive mapping 


#### Training pipeline
OC-SVM never runs on raw pixels, but on deep features instead. 
`image -> CNN backbone -> feature vector -> One-class SVM`

#### Inference:
For test sample:
1. extract feature
2. compute decision functino
3. trheshold 

#### limitations 
Its global modeling so usually work on whole image embedding, and often misses small scratches, tiny defects, and local anomalies, so no defect localization  

Its hard to scale, and sensitive to hyperparameters 

---
## AutoEncoder
`input -> encoder E(x) -> latent z -> decoder D(z)-> reconstruction x_hat`

Goal $\hat{x} ~= x$, It learns to compress and then reconstruct the input with pixel reconstruction loss: $\mathcal{L} = ||x - \hat{x}||^2$

The model only sees normal data during training, so it learns 
- normal patterns, textures and structures

At test time:
- normal image -> reconstructs well
- anomalous image -> reconstructs poorly

Thus $\text{anomaly score} = ||x - \hat{x}||$

#### Common autoencoder variants
- Convolutional Autoencoder - vanila AE for images
    - $z = E(x)$ deterministic mapping to a single point `x -> one point in latent space`
- Variational Autoencoder 
    - Adds probabilistic latent space - `x -> Gaussian blob in latent space`
    - Encoder outputs $\mu(x), \sigma(x)$
    - $z \sim \mathcal{N}(\mu(x), \sigma(x)^2)$
    - VAE loss has reconstruction loss and KL divergence
    - blurrier image -> weaker for tiny defect detection
- Denoising Autoencoder
    - Train with corrupted input -> reconstruct clean image
    - Forces model to learn robust structure instead of copy pixels
    - moderate impact
- Sparse Autoencoder
    - Force many latent units to be zero 
    - Encourage disentangled features 
- Peceptual-loss AE
    - Use VGG features instead of pixel loss - improves texture sensitivity

#### Limitations
Autoencoders often reconstruct anomalies too well, and this is the main failure mode. It sometimes learn: how to copy input, especially when model capacity is high and defects is too small

---
## DRAEM
It is designed specifically to fix the weakness of autoencoders, where vanilla AEs often reconstruct anomalies too well. 

It's two stage, trained end-to-end, and combines reconstruction + descriminative segmentation, trained using synthetic anomalies. 

> Train the model to reconstruct clean images and explicitly segment anomalies by injecting fake defects during training 

```
Input image
   ↓
[ Reconstruction Network ]
   ↓ reconstructed image
   ↓
concat(original, reconstructed)
   ↓
[ Discriminative Network ]
   ↓
anomaly map
```
**Reconstruction network**
Usually a UNet-style autoencoder  
Goal: 
$$\hat{x} = R(x_{corrupted})$$

It learns to remove anomalies and reconstruct clean image 

**Discrminative network**
Another UNet-like segmentation model 
Input:
- original image
- reconstructed image

Output:
$$A(x, y) = \text{anomaly probability}$$

This explicitly predicts anomaly mask 


#### Step 1. Synthetic anomaly generation
For each normal image:
- sample random texture from external dataset 
- paste irregular blob onto image
- create ground-truth anomaly mask 

No real defect labels needed 


#### Step 2. Train reconstruction network
Objective is to remove synthetic anomaly so teh reconstructor learns a **normal prior**:
$$\mathcal{L} - ||x_{clean} - R(x_{corrupted})||$$


#### Step 3. Train discriminative network 
Input:
$$[x_{corrupted}, R(x_{corrupted})]$$
Loss:

Binary cross-entropy with anomaly mask. Essentially a binary semantic segmentation network
Goal: explicitly segment anomaly regions

---

## PaDiM  - Patch Distribution Modeling
Unsupervised visual anomaly detection - Fit a gaussian distribution for each spatial location. This is a **parametric density estimation** approach. 

PaDiM assumes: For each patch location, normal features follow a multivariate Gaussian. 

During inference: if a patch's feature is unlikely under the Gaussian -> anomaly. 

**Training Phase (normal images only):**
1. Extract CNN features
2. For each spatial location:
    1. collect features across normal images
    2. estimate Gaussian (mean + covariance)

For each normal image: extract feature map, flatten spatially, group by location across dataset. So for each location $(i,j)$, you collect `N_normal x D features`

**Inference:**
1. Extract features from test image
2. Compute Mahalanobis distance per patch
3. Upsample to anomaly heatmap
4. Take max -> image score 

#### Step 1. Feature Extraction
Same as PatchCore

#### Step 2. Build per-location nGaussian 
For each spatial index $(i, j)$, normal features follow:

$$f_{i,j} = \mathcal{N}(\mu_{i,j}, \Sigma_{i,j})$$

Each spatial location has its own Gaussian. Its standard multivariate Gaussian fitting


#### Step 3. Inference anomaly scoring
Now for a test image compute Mahalanobis distance for each patch `f_test[i, j]`:

$$s_{i,j} = \sqrt{(f_{i,j} - \mu_{i,j})^T\Sigma_{i,j}^{-1}(f_{i,j} - \mu_{i,j})}$$

Which measures how unlikely the patch is under normal distribution


---
## PatchCore
Unsupervised visual anomaly detection. Learning what normal looks like at the patch level, then flagging regions that deviates. Unlike global methods, it can detect small local defects, which is perfect for scratches, dents, missing components. It uses ImageNet features, and memory efficiency via coreset. 

This is **non-parametric density estimation**



Key principles: feature matching between the test sample and the nominal samples while exploiting the multi-scale nature of deep feature representations. 

image -> pretrained CNN -> patch features -> store in memory

1. Learns **feature representations** of normal images only
2. Stores representative normal patches in memory
3. At test time:
    1. Compares each patch of the new image to the memory
    2. Large distance -> anomaly

#### 1. Feature Extraction
Use a pretrained CNN (ImageNet pretrained). Features are taken from intermediate layers 
- Resnet50

Because ImageNet CNN already encode: edges, textures, shapes, and parts

Empiracally: normal patches cluster tightly, anomalous patches fall outside, so nearest neighbor distance works well. 

#### 2. Patch Embedding
Input image -> CNN -> feature map (CxHxW) -> HxW patch vectors of dim C

#### 3. Memory Bank Construction
Instead of storing ALL patches, PatchCore uses **Coreset Subsampling** to keep only the most representative normal patches  

We want a subset $C$ such that: every normal patch is close to at least one selected memory vector, This preserves the support of the normal distribution

Greedy k-center (fartheset point sampling)

#### 4. Inference (Anomaly Scoring)
For a test image:
1. extract patch feature
2. for each patch, find nearest normal patch and compute distance 
$$score = \min_{m\in memory} ||f_{path} - m||$$


#### 5. Image-level Score
1. max patch score
$$S_{image} = \max_i S_{patch_i}$$
2. Top-k average


#### 6. Pixel-level Anomaly Map
PatchCore upsamples patch distances back to image size to produce:
1. anomaly heatmap
2. segmentation of defects  

This is why patchcore is strong for local defects. 


#### Limitations
- large rotations, unseen viewpoints 

--- 
Q: Whates the difference between PaDiM and PatchCore?

PatchCore is non-parametrics: kNN distance to memory

PaDiM is parametrics: mahalonobis under Gaussian

