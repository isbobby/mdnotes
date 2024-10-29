# Context
The **h-index** is a metric used to evaluate the productivity and citation impact of a researcher or scholar. It is defined as follows:

A researcher has an **h-index** of **h** if **h** of their **N** papers have at least **h** citations each, and the other **N - h** papers have no more than **h** citations each.

We can represent the number of citations with a one dimensional integer array. For example - 
```
[6,5,3,1,0]
```

The **h-index** will be 3 for the above scholar - the scholar has 3 papers each with at least 3 citations. Across any one dimensional integer array, we want to calculate the h-index correctly and efficiently.

The problem to calculate the h-index is available on [leetcode](https://leetcode.com/problems/h-index/description/).
# Intuition & Sketch


```
#
# #
# # #
# # # # # #
4 3 2 1 1 1 
```

# Proof
