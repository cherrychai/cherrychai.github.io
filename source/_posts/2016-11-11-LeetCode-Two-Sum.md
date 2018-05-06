---
layout: post
title: LeetCode - Two Sum
comments: true
author: "柴华"
date: 2016-11-11
header-img: 
categories : [LeetCode]
tags: [Array, Hash Table]
---

> [1. Two Sum](https://leetcode.com/problems/two-sum/)
>
> Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.
>
> You may assume that each input would have exactly one solution.
>
> **Example:**
>
>     Given nums = [2, 7, 11, 15], target = 9,
>     Because nums[0] + nums[1] = 2 + 7 = 9,
>     return [0, 1].
>
> **UPDATE (2016/2/13)**: The return format had been changed to **zero-based** indices. Please read the above updated description carefully.

从一道以前做过的简单题目开始吧，练练手感。

题意很简单，就是给定一个数组和一个目标数字，在数组中找到相加结果等于这个目标数字的两个数字，返回他们的下标（从0开始），当然为了简单化有一个假设就是只有一个满足条件的结果。

简单粗暴的遍历当然是可以解决问题的，但是时间复杂度是O(n^2)，当然不推荐。

那就使用哈希吧，但是要注意不是先生成一个hash map再求解，而是一次遍历的时候遍构造边求解。

果然好久不练手了上来就是各种Compile Error，也是醉醉的。

其实这道题目看起来挺简单的，不过我还真的在某大公司被面试过，虽然当时面试的是一道变形题目，数组是有序的，而且要求不能用哈希，不过是校招面试的，我猜主要目的还是为了考查基本功吧。
<!--more-->

###### C++:
``` c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        map<int, int> map;
        for (int i = 0; i < nums.size(); ++i) {
            int expected = target - nums[i];
            if (map[expected] > 0) {
                res.push_back(map[expected]-1);
                res.push_back(i);
                break;
            }
            map[nums[i]] = i+1;
        }
        return res;
    }
};
```

###### Java:
``` java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; ++i) {
            int expected = target - nums[i];
            if (map.containsKey(expected)) {
                return new int[] {map.get(expected), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution.");
    }
}
```
