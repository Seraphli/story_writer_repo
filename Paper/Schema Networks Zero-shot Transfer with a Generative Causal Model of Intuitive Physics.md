---
title: Schema Networks Zero-shot Transfer with a Generative Causal Model of Intuitive Physics
tags: RL
---

## Main idea

Use linear programming and binary weight to keep predicting transitions of entity-attribute, and also predicting reward

## How

1. How to represent continuous variables?
	By using a set of binary variables
2. Algorithm
	> Extract entities and attributes from environment
	> Use binary weight to calculate possibility for transition or reward, with relationship defined as AND, OR
	> Use prediction for decision-making, applying Monte Carlo Tree Search

## Benefit

1. Easy to interpret model
2. Reusable model. Use to solve similar task.

## Limit

1. Need to extract entity and attribute from environment (they use their own environment in the paper)
2. You still need to train the model after changing the environment
3. Stochastic environments are not suitable for this architecture. I think it is because they are using binary variables in the calculation.
4. Not a main-stream model.