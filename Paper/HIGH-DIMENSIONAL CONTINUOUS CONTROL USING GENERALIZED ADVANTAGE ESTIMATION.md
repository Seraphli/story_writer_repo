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

https://en.wikipedia.org/wiki/Moving_average#Exponential_moving_average
The generalized advantage estimator GAE(γ; λ) is defined as the exponentially-weighted average
of these k-step estimators.

https://github.com/openai/baselines/blob/902ffcb7674dd9f3c08a0037ae57ada852f13d74/baselines/common/misc_util.py#L123-L154
So it remembers me about the implement of running average in openai baselines.