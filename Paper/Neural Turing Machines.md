---
title: Neural Turing Machines
tags: 
---

Crucially, every component of the architecture is differentiable, making it straightforward to train with gradient descent. We achieved this by defining ‘blurry’ read and write operations that interact to a greater or lesser degree with all the elements in memory (rather than addressing a single element, as in a normal Turing machine or digital computer). 

The first mechanism, “content-based addressing,” focuses attention on locations based on the similarity between their current values and values emitted by the controller.

Arithmetic problems fall into this category: the variable x and the variable y can take on any two values, but the procedure f(x; y) = x × y should still be defined. A controller for this task could take the values of the variables x and y, store them in different addresses, then retrieve them and perform a multiplication algorithm. In this case, the variables are addressed by location, not by content. We call this form of addressing “location-based addressing.”