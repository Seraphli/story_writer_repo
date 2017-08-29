---
title: HIGH-DIMENSIONAL CONTINUOUS CONTROL USING GENERALIZED ADVANTAGE ESTIMATION
---

This is a good start for understanding policy gradient.

But the discounted problem can be handled as an instance of the undiscounted problem in which we absorb the discount factor into the reward function, making it time-dependent.

See Greensmith et al. (2004) for a more rigorous analysis of the variance of policy gradient estimators and the effect of using a baseline.

There are two notable special cases of this formula, obtained by setting λ = 0 and λ = 1.

GAE(γ; 1) is γ-just regardless of the accuracy of V , but it has high variance due to the sum of
terms. GAE(γ; 0) is γ-just for V = V π;γ and otherwise induces bias, but it typically has much
lower variance. The generalized advantage estimator for 0 < λ < 1 makes a compromise between
bias and variance, controlled by parameter λ.