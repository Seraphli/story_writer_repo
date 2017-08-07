---
title: Mastering the Game of Go with Deep Neural Networks and Tree Search
tags: RL
---

## Fragment

1. All games of perfect information have an optimal value function
2. In large games, exhaustive search is infeasible
3. the depth of the search may be reduced by position evaluation
4. the breadth of the search may be reduced by sampling actions from a policy `!$p(a|s)$` that is a probability distribution over possible moves `!$a$` in position `!$s$`
5. We train the neural networks using a pipeline consisting of several stages of machine learning (Fig. 1). We begin by training a supervised learning (SL) policy network pσ directly from expert human moves. This provides fast, efficient learning updates with immediate feedback and high-quality gradients. Similar to prior work, we also train a fast policy pπ that can rapidly sample actions during rollouts. Next, we train a reinforcement learning (RL) policy network pρ that improves the SL policy network by optimizing the final outcome of games of selfplay. This adjusts the policy towards the correct goal of winning games, rather than maximizing predictive accuracy. Finally, we train a value network vθ that predicts the winner of games played by the RL policy network against itself. Our program AlphaGo efficiently combines the policy and value networks with MCTS.
6. The second stage of the training pipeline aims at improving the policy network by policy gradient  reinforcement learning (RL).
7. Randomizing from a pool of opponents in this way stabilizes training by preventing overfitting to the current policy. 
8.  Training on this data set led to MSEs of 0.226 and 0.234 on the training and test set respectively, indicating minimal overfitting. (How to identify overfitting)