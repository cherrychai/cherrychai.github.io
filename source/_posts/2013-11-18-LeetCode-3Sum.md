---
layout: post
title: LeetCode - 3Sum
date: 2013-11-18 00:13:15
categories: [leetcode]
tags: [array, two pointers]
---

> [15. 3Sum](https://leetcode.com/problems/3sum/)
>
> Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.
>
> **Note**: The solution set must not contain duplicate triplets.
>
>     For example, given array S = [-1, 0, 1, 2, -1, -4],
>     A solution set is:
>     [
>         [-1, 0, 1],
>         [-1, -1, 2]
>     ]

给定一个数组，求出所有和等于0的所有的三个数字的组合。也是[LeetCode - Two Sum](http://chaihua.me/leetcode/2016/11/11/LeetCode-Two-Sum/)的变形，不过相对要麻烦很多，而且题目不允许解中有重复，这一点需要注意。

我解决这道题目的核心思路就是先对数组进行排序，然后从左向右遍历，对于每一个数字nums[i]按照[Two Sum II - Input array is sorted](http://chaihua.me/leetcode/2016/11/17/LeetCode-Two-Sum-2-Input-Array-Is-Sorted/)中的方法求后半段数组中和等于-num[i]的两个数字，这样就能得到所有满足条件的三元组。

逻辑已经清楚了，但是AC的时候仍然会很容易超时，这里需要注意以下几点：

- [Two Sum II - Input array is sorted](http://chaihua.me/leetcode/2016/11/17/LeetCode-Two-Sum-2-Input-Array-Is-Sorted/)中有假设仅有一个满足条件的解，所以找到之后可以直接退出，但是本题不可以。

- 判断重复最直观的可能是找到一组解时直接与vector中元素比较，但是这样显然会超时，那么直接与最后一个元素比较呢，毕竟是排好序的，答案仍然是超时，所以只能在双指针循环的那一步就直接skip掉重复的元素。

- 只在双指针循环时去除重复元素仍然会有重复解，所以还要对nums[i]进行判重。

注意以上几点应该就没问题了，不多说了，直接上代码，仍然是我做题时很爱，工作时无法爱的C++。
<!--more-->

###### C++:
``` c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        int size = nums.size();
        if (size < 3) {
            return res;
        }

        sort (nums.begin(), nums.end());
        
        for (int i = 0; i < size; ++i) {
            // skip duplicate triplets

            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int left = i + 1;
            int right = size - 1;
            while (left < right) {
                if (nums[i] + nums[left] + nums[right] == 0) {
                    vector<int> solution;
                    solution.push_back(nums[i]);
                    solution.push_back(nums[left]);
                    solution.push_back(nums[right]);
                    res.push_back(solution);

                    // skip duplicate triplets

                    while (left < right && nums[right] == solution[2]) {
                        --right;
                    }
                    while (left < right && nums[left] == solution[1]) {
                        ++left;
                    }
                } else if (nums[i] + nums[left] + nums[right] > 0) {
                    --right;
                } else {
                    ++left;
                } 
            }
        }
        return res;
    }
};
```

> The result should not contain duplicate triplets. So there are two place need to take care.
>
> The first one is when enumerate the first element, we should check that whether the current one is the same as last one.
>
> The second place is we need to check that when sum is 0, not only just j ++ or k --, but also pass by the duplicate one.

