---
title: Mastering the game of Go without human knowledge
tags: Reinforcement Learning
---

Here we introduce an algorithm based solely on reinforcement learning, without human data, guidance or domain knowledge beyond game rules. 

Our program, AlphaGo Zero, differs from AlphaGo Fan and AlphaGo Lee12 in several important aspects. First and foremost, it is trained solely by self-play reinforcement learning, starting from random play, without any supervision or use of human data. Second, it uses only the black and white stones from the board as input features. Third, it uses a single neural network, rather than separate policy and value networks. Finally, it uses a simpler tree search that relies upon this single neural network to evaluate positions and sample moves, without performing any Monte Carlo rollouts. 