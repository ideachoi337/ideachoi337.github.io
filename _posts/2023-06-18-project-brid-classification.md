---
layout: post
title: Bird Image Classification
categories: Projects
image: /images/2023-06-18-project-bird-classification-01.png
---

**This is course project for CSI4116 Computer Vision course @ Yonsei Univ.**

### Task
Classifing species of birds from images.<br>
Implementing deep learning models (ResNet-34) for bird classification using Pytorch.

### Model
![Image](/images/2023-06-18-project-bird-classification-01.png)
Backbone of pretrained ResNET-34 is used.<br>
For classifier, 2-layer-MLP is used.
- Linear(512, 512), Linear(512, 50)
- ReLU for activation function.
Between backbone and classifier, average pooling layer is used.
- Parameter count: ~21M

### Training
- Data Argumentation
    - TrivialArgumentWide() from PyTorch is used.
    - Using TrivialArgumentWide() is more accurate and faster then using RandomHorizontalFlip, RandomRotation and Colorjitter.
    - Only TrivialArgumentWide() and RandomResizedCrop() are used.

![Image](/images/2023-06-18-project-bird-classification-02.png)
- Freeze Backbone
    - Except last layer of ResNET backbone, backbone is freezed and not updated.
    - Training last layer is better than freezing all.

- Optimizer
    - Adam optimizer is used.
    - Better result then AdamW optimizer is used.

- Learning Rate: 1e-4
- Epoch: 30
- Batch size: 16

### Inference
- Ensemble
    - Training 5 times to same model, and ensemble them.
    - Soft voting is sued.
- FiveCrop is used.

### Experimental Result
- Result with different learning method
![Image](/images/2023-06-18-project-bird-classification-03.png)
- Result with ensemble
![Image](/images/2023-06-18-project-bird-classification-04.png)
    - With ensemble, accuracy has increased to 90.4% from 86%
![Image](/images/2023-06-18-project-bird-classification-05.png)