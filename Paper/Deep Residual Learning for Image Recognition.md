---
title: Deep Residual Learning for Image Recognition
tags: Reinforcement Learning
---

What they try to solve

When deeper networks are able to start converging, a degradation problem has been exposed: with the network depth increasing, accuracy gets saturated (which might be unsurprising) and then degrades rapidly. 

Why they try to solve

Many other nontrivial visual recognition tasks [8, 12, 7, 32, 27] have also greatly benefited from very deep models.

Benefit of their method

Identity shortcut connections add neither extra parameter nor computational complexity. The entire network can still be trained end-to-end by SGD with backpropagation, and can be easily implemented using common libraries (e.g., Caffe [19]) without modifying the solvers.

How they do it

1. assume all function can be approximated using multiple nonlinear layers.
2. instead of using one layer to output hidden layer result H(x), they use multiple layers to represent a hidden layer
3. there is a residual between hidden layer output H(x) and input x
4. use F(x) to represent H(x)-x, which F(x) is approximated by multiple nonlinear layers
5. 