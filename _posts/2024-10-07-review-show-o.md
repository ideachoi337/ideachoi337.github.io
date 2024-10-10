---
layout: post
title: Show-O
categories: Review
image: /images/2024-10-07-review-show-o-02.png
tags: LMM Diffusion
---

**Year** 2024/08<br>
**arXiv** [https://arxiv.org/abs/2408.12528](https://arxiv.org/abs/2408.12528)

<hr>

### TL;DR
- Handle both text and image with a single Transformer model.
- Unifies autoregressive (for text) and diffusion modeling (for images).
- Onmi-Attention Mechanism: causal attention for text, bidirectional attention for images.

### Motivation
![Image](/images/2024-10-07-review-show-o-01.png)
- Existing models such as LLaVA use a separate model for multimodal understanding <br>
&rarr; Is it possible to process multimodal data throught one transformer?
- Chameleon model uses one transoformer model using image tokenization and generate autoregressively.
    - Chameleon model generates 1024 tokens for an image, and needs lots of sampling steps.<br>
    &rarr; Is it possible to perform AR for text and diffusion for image using a single transformer?
    - Show-o model needs only 20x lower sampling steps for image generation
- Show-o model generate text & image with an single transformer by Autoregressive & Diffusion modeling.

### Method

#### Model
![Image](/images/2024-10-07-review-show-o-02.png)
- Show-o uses existing model.
    - Pre-trained Phi-1.5 model is used. (1.5B)
    - Added QK-Norm layer in front of attention layer.
    - Size of Embedding layer is changed due to added 8,192 image tokens
- In constrast to other diffusion models, Show-o does not need additional text encoder for text conditioning.
    - Because transformer uses all previous sequence information.

#### Tokenization
- Text Tokenization: Uses Phi-1.5 text tokenizer.
- Image Tokenization: Experiments with three versions.

![Image](/images/2024-10-07-review-show-o-03.png)
- (a) - Trining lookup-free quantier using MAGVIT-v2 (default setting)
    - Codebook size: 8,192
    - Convert 256x256 image into 256 discrete tokens
    - Because it convert image to discrete tokens, it needs embedding layer to input model with continuous data.
- (b) - Using MAGVIT-v2 but without quantization.
    - Because transformer get continuous data, there is no needs for tokenization.
    - Only projection layer to align dimension is needed.
    - But this representation only can understanding becuase show-o uses discrete diffusion.
- (c) - Using CLIP-VIT
    - Same as method (b), but uses CLIP-VIT model.

#### Unified Prompting
![Image](/images/2024-10-07-review-show-o-04.png)
Special token are added to prompting for multimodal understanding and generation.
- \[MMU\] - Multimodal understanding
- \[T2I\] - Multimodal generation
    - Also can be used with interleaved generation.

#### Omni-Attention Mechanism
![Image](/images/2024-10-07-review-show-o-05.png)
- Causal Attention for text tokens
- Full Attention for image tokens

#### Training Objectives
- Next Token Prediction (NTP)
    - Default setting for language modeling
    - Training with autoregressive & causal settings.
    - Cross-entropy loss is used.
- Mask Token Prediction (MTP)
    ![Image](/images/2024-10-07-review-show-o-06.png)
    - Discrete diffusion is same with MTP
    - Using previous sequence as condition, model predict image tokens for masked token.
    - Cross-entropy loss is used.
- Final loss is weighted sum of two losses.

#### Training
Three-stage approach
1. Image Token Embedding and Pixel Dependency Learning
2. Image-Text Alignment for multimodal understadning and generation
3. High-Quality Data Fine-tuning

#### Inference
- Text generation: Autoregressive generation
- Image generation
    - Using T diffusion steps.
    - First, 256 \[MASK\] tokens are added to model input
    - Denoising using logit value
        - Classifier-free guidance is used.

### Experiments
- Multimodal Understanding
![Image](/images/2024-10-07-review-show-o-07.png)
- Visual Generation
![Image](/images/2024-10-07-review-show-o-08.png)
- Qualitative Results
![Image](/images/2024-10-07-review-show-o-09.png)
![Image](/images/2024-10-07-review-show-o-10.png)