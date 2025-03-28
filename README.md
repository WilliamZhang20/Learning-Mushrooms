﻿# Learning Mushrooms

In this project, I implemented a decision tree from scratch using NumPy to determine if a mushroom is edible. This enabled me to dive deep into the atomics of how decision trees work.

## Table of Contents
- [How Do Decision Trees Work](#how-do-decision-trees-work)
- [Dataset Overview](#dataset-overview)
- [Model Implementation](#model-implementation)
  - [Calculating Entropy](#calculating-entropy)
  - [Information Gain](#information-gain)
  - [Tree Construction](#tree-construction)
- [Conclusion](#conclusion)

## How Do Decision Trees Work

A decision tree is a data structure that is capable of both classification & regression.

While machine learning algorithms like logistic regression & neural networks make a decision boundary based on a mathematical estimation, a _classification_ decision tree splits the data based on the value of certain features to make the left & right children more distinct categories. The process continues recursively until it reaches the leaf, where the data there should be distinct, and is assigned a category.

When a new mushroom is checked for edibility, its leaf in the decision tree is computed based on the splits that were optimized by the model. Whatever the label at that leaf is the label given to it by the model. 

## Dataset Overview

The dataset in this project is relatively small, to demonstrate & visualize a working tree, rather than make a super accurate model. It contains information about a number of mushrooms, specifically their cap color, stalk shape, and solitude (i.e. does the mushroom come in a bunch). That is already pre-one-hot-encoded. The labels are binary, a 0/1 to say not edible or edible.

## Model Implementation

### Calculating Entropy

Let's think of how we would intuitively split a decision tree. We want the leaves to contain data from *only* **one** category. So we want the smallest number of variations possible in the data, specifically the edibility label. In other words, we want the nodes to be as "pure" as possible, and the quantity for that is **entropy**. 

We want maximum certainty about the data at the nodes, so minimum entropy. Here's a [link](https://math.stackexchange.com/questions/331103/intuitive-explanation-of-entropy) for a more intuitive explanation.

For binary data in this case, entropy for dataset S is defined to be $$H(S) = -p log_2(p_1) - (1-p) log_2(1-p)$$, where $$p$$ is the probability of observing 1, i.e. a positive sample. 

### Information Gain

At each node split, we hope to make the left & right children's partition sets more pure. Since data labels are correlated with given features, splitting on whether a mushroom is 0/1 (since they're all one-hot) should help us increase purity, decrease entropy, or in other words give us "more information." 

So, we want to find the feature that gives us the highest information gain. My favourite websites on information gain are [here](https://en.wikipedia.org/wiki/Information_gain_(decision_tree)) and [this](https://homes.cs.washington.edu/~shapiro/EE596/notes/InfoGain.pdf).

Of course any "gain" is a difference, so it is a difference between the entropy of the parent node and the *weighted* average entropy of the children. That weighted average is the sum of entropy at the child node multiplied by its relative size to the parent node. 

The formula is hence, $$\text{Information Gain} = H(p^{node}) - (w^{left} H(p^{left}) + w^{right} H(p^{right})$$

where
- $$H(p^{node})$$ is entropy at parent node
- $$H(p^{left})$$ and $$H(p^{right})$$ are entropies of left & right nodes as a result of splitting on some feature
- $$w^{left}$$ and $$w^{right}$$ are the proportion of examples at the left and right branch, respectively, over the number of parent node samples

So, I implemented a function used by each node that will compute this information gain for all available data features, and choose the feature with the largest gain.

### Tree Construction

The tree construction function is given the data, the current node & its depth, as well as the maximum depth for the tree, a limit set to prevent overfitting.

It uses the information-gain-based feature-selection function to choose features, split, and make recursive calls for the new left & right subtrees.

This continues until maximum depth is reached, a kind of base case of recursion.

## Conclusion

Based on the NumPy implementation of all the above theory, I obtained this tree split which classified the training data perfectly at the leaves:

![image](https://github.com/user-attachments/assets/dd28246f-a5c6-43f1-890e-0c73843252f4)
