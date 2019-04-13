# Computational Statistics

## Overview
There are two perspectives we can look on statistical problems while solving them: analytical and computational.

**The analytical methods** are based on mathematics (mainly statistics and probability) and use of domain and theoretical knowledge. **The computational methods** are based on computer science (mainly software engineering) and use of fundamental computing techniques, like looping or use of random number generator.

Analytical methods were in many cases the only ones practically available in the past as data collection and computing power were expensive. Nowadays, computational methods become more and more attractive as data collection and computing gets cheaper and more easily available than before.

The main analytical tools are statistical tests and probability whereas computational approach is based on simulations.

I will walk you through two examplary problems and compare how they can be solved using both methods: simulating outcomes and permutation testing.

## Example 1: Simulating Outcomes

This example shows how we can do an efficient simulation of probabilities of common events and statistics.

As an example let's try to answer the following question using both methods: **If you roll two 6-sided dice, how likely is it that the sum of results is greater than 7?**

First, let's answer with the **analytical method**. The problem above is known as variations with repetition. We have 6 to the power of 2 possible outcomes, 36 pairs in total. A pair (x, y) would consist of two numbers, x being a roll result of the first die and y being a roll result of the second one.

Our results would look like like these: (1, 1), (1, 2), (1, 3), ..., (6, 6) but we are interested in the ones where the sum of pair elements is greater than 7. Let's then enumerate them:

- (2, 6) -> 1
- (3, 5), (3, 6) -> 2
- (4, 4), (4, 5), (4, 6) -> 3
- (5, 3), ..., (5, 6) -> 4
- (6, 2), ..., (6, 6) -> 5

There are 15 pairs where the sum of elements is greater than 7. We can calculate the likelihood of getting the sum of results as:

\<number of pairs with the sum greater than 7> / \<number of all possible pairs> = 15/36 = 0.4166(6)

The answer is: There is 42% of likelihood that if we roll two 6-sided dice we get the sum of the results greater than 7.

The example above is simple to solve as the number of variations is reasonable small (36) but what if our problem is more complex? For example, how we could solve analitically the following question: If you roll seven 6-sided dice, how likely is it that the sum of results is greater than 35? In this case we would have 6 to the power of 7 possible outcomes, which is 279,936 variations of pairs. Solving this problem analitically would take much more time...

Here is how we could answer the same question (If you roll two 6-sided dice, how likely is it that the sum of results is greater than 7?) with a simulation.

We will use `choices()` function from `random` module and `Counter` class from `collections` module.

```python
from random import choices
from collections import Counter
```
Then we define our die.

```python
# Define a 6-sided die
faces = list(range(1, 7))
faces

[1, 2, 3, 4, 5, 6]
```
We will use `choices()` function to simulate a dice roll. The function takes our die definition as the first argument, and `k` defines a number of dice. If we only wanted we can also apply different weights to die faces.

```python
# A function for rolling a pair of dice
roll_2_dice = (lambda: choices(population=faces, weights=None, k=2))
pair = roll_2_dice()
pair

[6, 5]
```
We can sum a pair of result using the `sum()` function.

```python
sum(pair)

11
```
Now, the main part begins. We repeat the step described above 50,000 times and save the sums in the `rolls` list.

```python
# Simulate rolling a pair many times and tracking the outcomes
rolls = [sum(roll_2_dice()) for _ in range(50_000)]
```
We can use `Counter` class to return a dictionary, where dictionary keys are sum values and dictionary values are number of pairs with the same value of sum as a dictionary key.

```python
# Count those outcomes (number of sums of paired elements)
rolls_counts = Counter(rolls)
print(rolls_counts)

Counter({7: 8293, 6: 6987, 8: 6878, 9: 5646, 5: 5465, 4: 4252, 10: 4058, 11: 2832, 3: 2781, 12: 1417, 2: 1391})
```
As a bonus, using the technique below, we can sort the dictionary by keys or values in both ascending and descending order. This would be difficult to achieve with an analytical approach.

```python
# Sort by faces [0] or counts [1] and store the sorted results as a dictionary
rolls_counts = dict(sorted(rolls_counts.items(), key=lambda x: x[1], reverse=True))
print(rolls_counts)

{7: 8293, 6: 6987, 8: 6878, 9: 5646, 5: 5465, 4: 4252, 10: 4058, 11: 2832, 3: 2781, 12: 1417, 2: 1391}
```
Another thing we can do easily with the computational approach is results visualization.

```python
# Plot the outcome of simulating many dice rolling (Binomial Distribution)
import matplotlib.pyplot as plt
%matplotlib inline

labels, values = zip(*rolls_counts.items()) # Unpack dict
plt.bar(x=labels, height=values);
```

<center><img src="images/2019-04-12-001.png" width="50%"/></center>


Now, we are ready to answer our question. The code below sums the number of occurences of sums greater than 7 and divides it by all occurences.

```python
# If you roll two dice, how likely is it that your sum is greater than 7
sum(v for k, v in rolls_counts.items() if k > 7) / sum(rolls_counts.values())

0.41662
```
**The result is** at the same time the likelihood that if we roll two 6-sided dice we get the sum of the results greater than 7.

## Resources

This repository contains different materials regarding computational statistics:

- [ComputationalStatistics-master.zip](https://github.com/ksatola/Computational-Statistics/blob/master/ComputationalStatistics-master.zip) - a copy of [Brian Spiering](https://github.com/brianspiering/ComputationalStatistics) repo. Brian's talk during [DSCO19](https://www.sfdatainstitute.org/) in San Francisco was a trigger for me to start dealing more systhematically with computational statistics.
- [analytical\_vs\_computational.pdf](https://github.com/ksatola/Computational-Statistics/blob/master/analytical_vs_computational.pdf) - a few slides with a context and brief summary of analytical and computational methods.
- Analytical vs. Computational Methods - **Example 1: Simulating Outcomes** ([Jupyter](https://github.com/ksatola/Computational-Statistics/blob/master/Example1.ipynb), [HTML](https://ksatola.github.io/projects/ComputationalStatisticsExample1.html))
- Analytical vs. Computational Methods - **Example 2: Permutation Testing** ([Jupyter](https://github.com/ksatola/Computational-Statistics/blob/master/Example2.ipynb), [HTML](https://ksatola.github.io/projects/ComputationalStatisticsExample2.html))
