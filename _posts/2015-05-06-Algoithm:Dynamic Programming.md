---
layout: default
title: Algoithm : Dynamic Programming
comments: true
---

## 动态规划是计算机科学里面很重要的一类算法， 设计该算法有这么几个关键点：
* `最优子结构` -> 为了能够DP， 首先保证问题本身具有`最优子结构（Optimal Substructure）`
* `重叠子问题` -> 为了使DP能够比较好的发挥功力，需要问题具有重叠的子问题(overlapping subproblems)， 而解决这个问题可以采用记忆化，
或者采用自底向上递推的方法解决
* `时间空间的优化` -> 时间优化主要可以采用平行`四边形法则`， `单调队列`等方法， 而空间优化主要依靠 `滚动数组`
