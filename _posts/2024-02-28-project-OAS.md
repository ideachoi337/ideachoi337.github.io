---
layout: post
title: Online Action Spotting
categories: Projects
image: /images/2024-02-28-project-OAS-02.png
tags: video online
---

### Intro
This is a project for SoccerNet challenge @ CVPR 2024.<br>
Using long soccer game video dataset, SoccerNet-v2 dataset, conduct action spotting to find the point where a specific action appers.<br>
Using existing model, MiniROAD, i tried to solve this task in online setting.

### What is Online Action Spotting

![Image](/images/2024-02-28-project-OAS-01.png)
**Action Spotting** is a task that finds the moment when a particular action occurs in videos.<br>
Online means that it deals with streaming videos, so online action spotting is task to perform action spotting with streaming videos.

### Proposed method

Becuase Action Detection and Action Spotting task is very similar, existing model is used for this project.<br>
Using <aggr>MiniROAD</aggr> model, which is Online Action Detection model, i tried action spotting with online setting.

Experimented with ResNet feature and Baidu Soccer Embeddding on the SoccerNet dataset with Input.
- ResNet feature: 2FPS, 512dim
- Baidu Soccer Embedding: 1FPS, 8576dim

For performing Action Spotting with OAD model, these inputs are used.
- Shape: (# of frames, # of classes)
- Using One-Hot to represent action for each frame.
- While training, make action appears across multiple frames instead of a point in one frame, so that it is similar to the data of OAD task.
    - *action_length*: the number of frames in which an action occurs.
    - we delated to action because the model may predict action eariler when inference.

![Image](/images/2024-02-28-project-OAS-02.png)



### Result

| Feature | Embedding_dim | Hidden_dim | num_layers | action_length | average mAP (tight) |
| === | === | === | === | === | === |
| ResNet | 1024 | 2048 | 1 | 1 | 31.48 |
| Baidu Soccer Embeddings | 1024 | 2048 | 1 | 1 | 32.88 |
| Baidu Soccer Embeddings | 512 | 1024 | 1 | 1 | 35.47 |
| Baidu Soccer Embeddings | 512 | 1024 | 2 | 1 | 35.89 |
| Baidu Soccer Embeddings | 512 | 1024 | 1 | 3 | 48.26 |
| Baidu Soccer Embeddings | 512 | 1024 | 2 | 3 | **48.57** |
| Baidu Soccer Embeddings | 512 | 1024 | 2 | 5 | 44.40 |

- Better performance when using Baidu Soccer Embedding instead of ResNET.
- Better performance when using small model instead of large model.
- Better performance when 2 RNN layers instead of 1 layer.
- Better performance when action_length > 1 and best when action_length is 3.

| Method | Average mAP (tight) | Shown only (tight) | Unshown only (tight) |
| === | === | === | === |
| 2023 Baseline | 68.33 | 73.72 | 60.88 |
| MiniROAD | 48.57 | 53.84 | 23.52 |

- Action Spotting using MiniROAD is slightly lower in performance than 2023 Baseline, but it is meaningful in that it is an online method.

![Image](/images/2024-02-28-project-OAS-03.png)

- Performance decline in the unshown sithuation is greater than baseline.