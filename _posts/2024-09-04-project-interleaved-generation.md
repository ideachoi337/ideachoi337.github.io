---
layout: post
title: Interleaved generation using LMM
categories: Projects
image: /images/2024-09-04-project-interleaved-generation-01.png
tag: LMM
---

### Interleaved Data Generation: Illustrated Instruction Task
![Image](/images/2024-09-04-project-interleaved-generation-01.png)
Interleaved text & image generation means generating images and text together. Without specific output format, model can generate images between generated texts freely.<br>
Illustrated Instruction Task was introduced in paper <q>Generating Illustrated Insturction</q> (CVPR 2024).<br>
This is one of the interleaved data generation task and task for generating (step, image) pairs for given goal. Thus, model need to generate multiple images.<br>


### Approach
In this project, Anole model is used for Illustrated Instruction task. Anole model is fine-tuned Chameleon model and by token-based approach, it can generate image and text in same manner.<br>
7B size version is used and two method are used.
1. Training-free method
- Only using prompt engineering. Parameter tuning is not used.
- Aim for generating in (text, image) pair format.
2. Fine-tuning
- For prompt tuning
- LoRA training is used.

### Method 1
![Image](/images/2024-09-04-project-interleaved-generation-02.png)
By prompt engineering, model can generate interleaved data in desired format.

Problem: Context length limit<br>
![Image](/images/2024-09-04-project-interleaved-generation-03.png)
While output token length limit of vanilia Anole model is 4096 tokens, an image is equal to 1024 tokens. This mean model can generate upto 3 images with this setting. By simpliy extending this limit cannot be solution because it drives to bad quality of output such as duplicated images.

### Method 2
Second method is fine-tuning model using illustrated instruction dataset.

For training model, these dataset can be used.

First is YouCook2 Dataset.<br>
This is dataset of cooking video with cooking step annotations. Because this dataset is not designed for this task, re-formating dataset is needed.<br>
![Image](/images/2024-09-04-project-interleaved-generation-04.png)
Instead of simply extract middle frame of cooking-step, frame with high CLIP score with cooking-step string are extracted.

Another dataset is VGSI Dataset.<br>
This is WikiHow dataset with (goal, step, image) data and this can be directly used for this task. data of 'reciple' category are used.

Problem of this method is GPU memory constraints. using RTX A6000 GPU with 48GB RAM, it is unable to learn a whole sequence in test case.<br>
Thus, reducing the number of training layer is needed. Upto step 4 can be used when only training 8 layers but this resulting in poor generation.<br>
For experiments upto step 2 text are used with training whole transformer layer.

Ablation study: Training model head only for image tokens
![Image](/images/2024-09-04-project-interleaved-generation-05.png)
As like Anole fine-tuning approach, model head only for image tokens (Token 3 ~ 8196) are trained.<br>
But this approach resulting in poor quality of image generation.


### Result
- Metrics
![Image](/images/2024-09-04-project-interleaved-generation-06.png)
    - Goal Faithfulness (GF): (Goal text, Image) CLIP similarity
    - Step Faithfulness (SF): (Step text, Image) CLIP similarity
    - Use average value of all images in test set
    - Use VIT-B/32 CLIP model
    - *TODO: Need to struct multiple-choic quiestions for accuracy*

As a result, our methods get lower score than StackedDiffusion (T2I Diffusion Model from Generating Illustrated Instruction paper). But StackedDiffusion model is specialist for generating image and our approach is generalist (because this is LMM), we cannot compare these directly.<br>

Also, by fine-tuning, we can get low Goal Faithfulness and High Step Faithfulness. Then, Which is more important, SF or GF?<br>
![Image](/images/2024-09-04-project-interleaved-generation-07.png)
In this task, Step Faithfulness will be more important. Because High GF and Low SF means model generate image corresponding given goal but not for steps. Our goal is generating images for step, thus SF is more important.

### The Remaining Things
- Model parallelism
    - For full sequence & full transformer layer training
- More advanced Training-Free methods?
    - Changing prompt (Method 1) may be the most basic method.
- Only using 'Recipe' category?
    - Training with dataset of other category
    - Full VGSI Dataset has lots of categories.
- Metrics for step text?
    - GF and SF are metrics only evaluating images, not texts.