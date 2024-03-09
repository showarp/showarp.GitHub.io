---
title: leetcode.40 回溯算法，剪枝
date: 2022-11-08 02:30:54
comment: true
categories:
    - 数据结构
tags:
    - dfs
    - 剪枝
    - 回溯
---
# 题目:
给定一个候选人编号的集合 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的每个数字在每个组合中只能使用 一次 。
注意：解集不能包含重复的组合。

## 实例1
```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

## 实例2
```
输入: candidates = [2,5,2,1,2], target = 5,
输出:
[
[1,2,2],
[5]
]
```

如果这题按照最朴素的dfs搜索的话额。。。。大概率是会超时的。
![就像这样👆](https://files.catbox.moe/cve8lu.png)
主要的原因是dfs会遍历完所有不同candidate的组合（这个到没问题），以及同一种组合的不同排列方式，问题就是题目要求我们解集不能包含重复的组合，所以这个同一种组合的不同排列方式是非常没有必要的，比如[1,2,3]和[3,2,1]
比如下面这个例子


## candidates = [1,1',1"], target = 3
![](https://files.catbox.moe/709t9i.png)
这里可以看得出来其实我们想要的答案在最左边的那个分支就已经出现了，也就是[1,1',1"],后面的分支路径都是第一个分支的不同排序而已，是已经重复的了。

导致这个问题的根本原因就是同一个层级有相同的分支，就比如说从根节点0出发有三个候选分支1 1' 1" 他们虽然是不同的的1(分支不同) 但是都是数字1
所以路径[0,1] [0,1'] [0,1"] 本质上都是同一个结果的不同排序，所以选哪个都一样，其实有学过动态规划的伙伴可以理解为，只保留分支以[0,1*]开头的其中一个就好，这样就可以达到剪枝的效果。

![](https://files.catbox.moe/1wq0nw.png)

这里引用leetcode题解下面的某个评论，可以加深一下理解
![](https://files.catbox.moe/4nrh5o.png)

```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        self.target = target
        candidates = sorted([i for i in candidates if i<=target])
        self.result = []
        self.dfs([],candidates)
        return self.result
    def dfs(self,waitlist,next):
        sumlist = sum(waitlist)
        if sumlist==self.target:
            self.result.append(waitlist)
            return
        for i in range(len(next)):
            if (i>0 and next[i]==next[i-1]) or sumlist+next[i]>self.target:continue
            self.dfs(waitlist+[next[i]],next[i+1:])
```
