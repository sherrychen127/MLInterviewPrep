# CLIP 
CLIP is a VLM model that learns to connect images and text in the same embedding space. CLIP is trained on (image, caption) pairs scraped from the internet:
- Image encoder (CNN or Vision transformer) - output image embedding vector
- Text encoder (Transformer) - outputs text embedding vector

CLIP uses contrastive learning to compute similarity matrix, and learns language alignment, and enable zero-shot classification. 
- Open Vocabulary detection 
    - detect objects never explicitly trained on
- Anomaly detection
    - compute similarity to normal prompt
- Region-level matching 


#### Limitations
- Not great at fine-grained localiztaion
- Struggles with subtle anomalies 
- Sensitive to prompt engineering


## Zero-shot learning
Zero-shot learning is the ability to classify categories that were never seen during supervised training. 
There are two generations of zero-shot learning:

#### Classical zero-shot learning 
Before models like CLIP, zero-shot learning relied on semantic attributes, not langugae models. 

| Class  | Has Stripes | Has Hooves | Long Ears |
| ------ | ----------- | ---------- | --------- |
| Zebra  | 1           | 1          | 0         |
| Horse  | 0           | 1          | 0         |
| Donkey | 0           | 1          | 1         |

At test time:
- predict attributes from image
- match to unseen class description

#### Modern zero-shot learning
Zero-shot learning requires a shared embedding space between seen and unseen classes, and language is just the most powerful way to define unseen classes. 

Now we do:
- encode image
- encode text prompt
- compare similarity

Language gives rich semantic supervision, which encode text into embedding space. 


## Open Vocabulary Detection
Detecting objects whose class names were not in the detector's training set

It extends zero-shot from classification to localization. 
- Region proposal network
- CLIP-stype text embedding
- Similarity matching 

Examples:
- Grounding DINO