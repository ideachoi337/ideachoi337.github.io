---
layout: post
title: Unsupervised TAS
categories: Projects
image: /images/2023-12-03-project-unsupervised-TAS-01.png
tags: Video 
---


### What is TAS

<abbr>TAS</abbr> is an abbreviation for "temporal action segmentation".<br>
This is a task that splits untrimmed video to video clips depending on actions in the video.

![Image](/images/2023-12-03-project-unsupervised-TAS-01.png)


### TW-FINCH: Algorithm for Unsupervised TAS

<abbr>TW_FINCH</abbr> is from paper <q>Temporally-Weighted Hierarchical Clustering for Unsupervised Action Segmentation</q> (CVPR 2021)<br>

This algorithm is
- Clustering based TAS
- Using Hierarchical Clustering
- Using temporally weighted distances

Becuase this algorithm uses hierarchical clustering, it can be used in unsupervised setting.
But there are some limitation.
1. **Needs** Ground Truths for mapping clusters to action name (Hungarian Matching using tIoU)
2. **Needs** the number of actions in video


### Experiment 1: Predict Action of Frame Using CLIP
By using CLIP for matching video frame and activity name, we **do not need** Ground Truth for mapping clusters to action name.<br>
First of all, there is an experiment to predict action of frame using clip.<br>
Using CLIP similarity score, all frames in the video were labeled as activity name.<br>

![Image](/images/2023-12-03-project-unsupervised-TAS-02.png)

Pros
- No need to know the average action number for each activity.
- No need for cluster mapping (Hungarian Matching)

Cons
- Stil need for information on what activities the video is about.
- Very low accuracy

**Result**<br>
Too low accuracy, we need other methods.

| | Cluster Mapping | # actions per viceo | Activity per video | Accuracy (MOR) | IoU |
| === | === | === | === | === | === |
| Baseline | O | O | O | **62.7%** | **43.4%** |
| Exp. 1 | | | O | 13.1% | 5.9 % |

### Experiment 2: Predict # of actions Using CLIP
Method 2 using CLIP to predict number of actions for each video using CLIP.<br>
First, predict action of frame using CLIP like method 1 and count how many actions appear. And perform TAS using this information and TW-FINCH algorithm.

![Image](/images/2023-12-03-project-unsupervised-TAS-03.png)

Pros
- **Do not needs** to know the average action number for each activity.

Cons
- **Still needs** cluster mapping and information on what activities is about.

**Result**<br>
Comparable accuracy with less information.

| | Cluster Mapping | # actions per viceo | Activity per video | Accuracy (MOR) | IoU |
| === | === | === | === | === | === |
| Baseline | O | O | O | **62.7%** | 43.4% |
| Exp. 1 | | | O | 13.1% | 5.9 % |
| Exp. 2 | O | | O | 61.8% | **43.9%** |

### Experiment 3: Predict Activity of Video Using CLIP
Method 3 using CLIP to predict activity of video using video frames and select activity with most votes.

![Image](/images/2023-12-03-project-unsupervised-TAS-04.png)

Pros
- **Do not needs** to know the type of activity of each video.

Cons
- **Still needs** cluster mapping and information of number of actions per video.

**Result**<br>

| | Cluster Mapping | # actions per viceo | Activity per video | Accuracy (MOR) | IoU |
| === | === | === | === | === | === |
| Baseline | O | O | O | **62.7%** | 43.4% |
| Exp. 1 | | | O | 13.1% | 5.9 % |
| Exp. 2 | O | | O | 61.8% | **43.9%** |
| Exp. 3 | O | O | | 60.85% | 39.94% |

![Image](/images/2023-12-03-project-unsupervised-TAS-06.png)

Abalation study of exp.3 : Weighted against time axis with normal distribution when predict activity.

![Image](/images/2023-12-03-project-unsupervised-TAS-05.png)