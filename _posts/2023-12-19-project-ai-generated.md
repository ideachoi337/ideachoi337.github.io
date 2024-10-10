---
layout: post
title: Detecting AI generated text
categories: Projects
image: /images/2023-12-19-project-ai-generated-01.png
---

**This is course project for AAI4101 Introduction to Multimodal Deep Learning course @ Yonsei Univ.**

### Introduction
This is a task from kaggle competition, <q>LLM - Detect AI Generated Text</q>.
It is competition to detect whether given texts are AI generated or not.<br>
Based on Kaggle Notebook from 'SIDDHVR', hyper-parameters and some weighted are tuned.

### Dataset
Training dataset is consists of 1378 text sequences with ground-truth labels.<br>
To increase the amount of training data, DAIST-v2 training dataset with 44868 text sequences is also used.

![Image](/images/2023-12-19-project-ai-generated-01.png)
### Multinomial Naive Bayse
Multinomial Naive Bayes is good method for classification problems for discrete features, such as text classification.<br>
NultinomialNB from sklearn was used, and the following hyperparameters are used.
- alpha(=0.02): Additive smoothing parameter.
- fit_prior(=True): Learning class prior probabilities.

### Linear Classifiers
Linear model is implemented using SGD. SGDClassifier from scikit-learn is used and the following hyperparameters are used.
- loss(='modified_huber'): smooth loss robust to outliers.
- penalty(='l2'): L2 regularization term is used.
- alpha(=0.0001): weight of regularization term.
- max_iter(=8000): maximum iteration count.
- tol(=1e-4): early stop when loss > best_loss - tol

### LGBM Classifier
It is a type of Boosting model that compensates for the speed problem of XGBoost. In constrast to XGBoost's level-wise tree growth, leaf-wise tree growth is used and it uses less memory and faster.<br>
Lightgbm's LGBMClassifier is used and the following hyperparameters are used.
- boosting_type(='gdbt'): Traditional Gradient Boosting Decision Tree is used.
- n_iter(=2500)
- objective(='cross_entropy')
- learning_rate(=0.00581909898961407)
- min_data_in_leaf(=115)
- max_depth(=23): max depth of generated decision tree

### CatBoost Classifier
It is a type of Boosting model with level-wise tree growth. CatBoostClassifier from catboost library is used.
The following hyperparameters are used.
- iterations(=2000): maximum number of generated trees.
- l2_leaf_reg(=6.6591278779517808): weight of L2 regularization.
- learning_rate(=0.005599066836106983)
- loss_function(=CrossEntropy)

### Ensemble
Using ensemble with Multinomial Naive Bayes, Linear Classifier, LGBM Classifier and CatBoost Classifier. soft voting is used and weights of [0.068, 0.31, 0.311, 0.311] are used.

### Result
![Image](/images/2023-12-19-project-ai-generated-02.png)
(December 19, 2023) ranked 130/2716 with 0.961 accuracy.
