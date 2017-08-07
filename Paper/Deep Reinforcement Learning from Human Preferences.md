---
title: Deep Reinforcement Learning from Human Preferences
tags: RL
---

## Main idea

Present two sequences for human, and let contractors to choose one from them. 
Build a neural network to fit the reward function according to human feedback.

## Why

1. Why use human feedback in reinforcement learning? Why not use reward function?
	Handcrafted reward function can not truly represent what we want agent to do.
	
## How

1. Algorithm
	> Start with random policy
	> Generate video clips from trajectory segments,  select those trajectories for which the predictions have the highest variance across ensemble members
	> Send to human for comparison, and store human judgements in a database `!$D$`
	> Fit the reward function using supervised learning to minimize the cross-entropy loss between these predictions and the actual human labels
	> Use synthetic labels and human labels to improve policy by policy gradient method like actor-critic
	> Repeat `Generate -> Human feedback -> Policy improvement` process

## Future work

Future work may be able to improve the efficiency of learning from human preferences, and expand the range of tasks to which it can be applied.
	
## Other question

1. Is this better than DQN or A3C?
	There is a figure at the top of page 8, showing that this algorithm is better in the game Enduro, but not in Breakout. They didn't reason about why it performed worse than A3C, they just did some experiments.
	
## My thought

1. Can we use this idea on games like Dota 2? Dota 2 provides such huge data of replays, and it keeps generating replays. These replays in a way are human preferences of some trajectories. 
