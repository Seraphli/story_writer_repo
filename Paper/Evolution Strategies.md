---
title: Evolution Strategies
---

we start with some random parameters and then repeatedly 1) tweak the guess a bit randomly, and 2) move our guess slightly towards whatever tweaks worked better.

**Injecting noise in the parameters.** However, RL injects noise in the action space and uses backpropagation to compute the parameter updates, while ES injects noise directly in the parameter space. Another way to describe this is that RL is a “guess and check” on actions, while ES is a “guess and check” on parameters.

No need for backpropagation. Highly parallelizable.