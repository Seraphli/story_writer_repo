---
title: Proximal Policy Optimization Algorithms
---

It is published by openai.

Our experiments compare the performance of various different versions of the surrogate objective, and find that the version with the clipped probability ratios performs best. We also compare PPO to several previous algorithms from the literature. On continuous control tasks, it performs better than the algorithms we compare against. On Atari, it performs significantly better (in terms of sample complexity) than A2C and TRPO; similarly to ACER though it is much simpler; and worse than DQN, though it is much more efficient with regard to wall-time and computation.

It is better on continuous control tasks.