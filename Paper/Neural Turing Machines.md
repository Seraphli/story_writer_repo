---
title: Neural Turing Machines
tags: 
---

Crucially, every component of the architecture is differentiable, making it straightforward to train with gradient descent. We achieved this by defining ‘blurry’ read and write operations that interact to a greater or lesser degree with all the elements in memory (rather than addressing a single element, as in a normal Turing machine or digital computer). 

The first mechanism, “content-based addressing,” focuses attention on locations based on the similarity between their current values and values emitted by the controller.