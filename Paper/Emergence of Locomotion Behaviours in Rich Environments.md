---
title: Emergence of Locomotion Behaviours in Rich Environments
---

Using a novel scalable variant of policy gradient reinforcement learning, our agents learn to run, jump, crouch and turn as required by the environment **without explicit reward-based guidance**.

Also, arguably, the requirement of careful reward design sidesteps a primary challenge of reinforcement learning: how an agent can learn for itself, directly from a limited reward signal, to achieve rich and effective behaviours. In this paper we return to this challenge.

Presenting the agent with a diversity of challenges thus increases the performance gap between different solutions and may favor the learning of solutions that are robust across settings.

First, we build upon robust policy gradient algorithms, such as trust region policy optimization (TRPO) and proximal policy optimization (PPO) [7, 8], which bound parameter updates to a trust region to ensure stability. Second, like the widely used A3C algorithm [2] and related approaches [3] we distribute the computation over many parallel instances of agent and environment. Our distributed implementation of PPO improves over TRPO in terms of wall clock time with little difference in robustness, and also improves over our existing implementation of A3C with continuous actions when the same number of workers is used.

The reward consists of a main component proportional to the velocity along the x-axis, encouraging the agent to make forward progress along the track, plus a small term penalizing torques. 

We structure our policy into two subnetworks, one of which receives only proprioceptive information, and the other which receives only exteroceptive information.

All of these behaviors emerged spontaneously, without special cased shaping rewards to induce each separate behaviour. 

It discovers that jumping up or forward (in some cases with surprising accuracy) is a suitable strategy to overcome hurdles, and gaps, and it learns to navigate walls, turning left and right as appropriate – in both cases despite **only receiving reward for moving forward**.