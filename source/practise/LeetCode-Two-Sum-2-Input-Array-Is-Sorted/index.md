---
layout: post
title: LeetCode - Two Sum II - Input array is sorted
date: 2013-11-16
---

> [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
>
> Given an array of integers that is already **sorted in ascending order**, find two numbers such that they add up to a specific target number.
>
> The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.
>
> You may assume that each input would have exactly one solution.
>
>     Input: numbers={2, 7, 11, 15}, target=9
>     Output: index1=1, index2=2

[LeetCode - Two Sum](http://chaihua.me/leetcode/2016/11/11/LeetCode-Two-Sum/)的变形，也是我真实面试过的一道题目，当时要求不能用哈希，我就分别从前后遍历了，也没问我有没有更好的方法，写好之后就让我补充了几个测试用例，然后面试就过了.

这道题目最直观的解决方案就是双指针的方法，一个指针left从最左端向右前进，一个指针right从最右端向左前进，left++会使结果增大，right--会使结果减小，然后通过比较numbers[left]+numbers[right]和target的大小决定接下来做left++还是right--，直到交汇。

LeetCode上面这道题目还标注了Binary Search的标签，但是我总感觉二分查找其实不如双指针更适合这道题，留着以后研究吧。
<!--more-->

### C++:
``` c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        vector<int> res;
        int left = 0;
        int right = numbers.size() - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                res.push_back(left + 1);
                res.push_back(right + 1);
            }
            if (sum < target) {
                ++left;
            } else {
                --right;
            }
        }
        return res;
    }
};
```

### Java:
``` java
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        for (int i = 1; left < right; ++i) {
            if (i % 2 == 1) {
                int expected = target - numbers[left];
                while (numbers[right] >= expected) {
                    if (numbers[right] == expected) {
                        return new int[] {left + 1, right + 1};
                    }
                    --right;
                }
            } else {
                int expected = target - numbers[right];
                while (numbers[left] <= expected) {
                    if (numbers[left] == expected) {
                        return new int[] {left + 1, right + 1};
                    }
                    ++left;
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution.");
    }
}
```