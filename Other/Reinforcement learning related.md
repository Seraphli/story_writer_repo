---
title: Reinforcement learning related
tags: 
---

1. https://www.reddit.com/r/reinforcementlearning/comments/71a17u/problem_with_experience_replay/

	> My practical experience playing with replay buffers is limited, but I would follow what is done in UNREAL (“RL with unsupervised auxiliary tasks”, Jaderberg et al): split the replay buffer into rewarding and non rewarding subsets, and replay equally from both subsets. Section 3.3 in the paper.

	I had this idea before. But in my implement it didn't work well.