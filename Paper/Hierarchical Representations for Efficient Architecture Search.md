---
title: Hierarchical Representations for Efficient Architecture Search
---

url: https://arxiv.org/abs/1711.00436

Our evolutionary algorithm is based on tournament selection (Goldberg & Deb, 1991).
The selection pressure is controlled by the tournament size, which is set to 5% of the population size in our case.	
In this case, a population of genotypes is generated randomly, the fitness is computed for each genotype in the same way as done in evolution, and the genotype with the highest fitness is selected as the final output. The main advantage of this method is that it can be run in parallel over the entire population, substantially reducing the search time.