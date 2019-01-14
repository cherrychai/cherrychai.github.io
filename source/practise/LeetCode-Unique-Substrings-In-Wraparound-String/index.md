---
layout: post
title: LeetCode - Unique Substrings in Wraparound String
date: 2014-02-27
---

> [467. Unique Substrings in Wraparound String](https://leetcode.com/problems/unique-substrings-in-wraparound-string/)
>
> Consider the string s to be the infinite wraparound string of "abcdefghijklmnopqrstuvwxyz", so s will look like this: "...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....".
>
> Now we have another string p. Your job is to find out how many unique non-empty substrings of p are present in s. In particular, your input is the string p and you need to output the number of different non-empty substrings of p in the string s.
>
> **Note:** p consists of only lowercase English letters and the size of p might be over 10000.
>
> **Example 1:**
>
>     Input: "a"
>     Output: 1
>     Explanation: Only the substring "a" of string "a" is in the string s.
>
> **Example 2:**
>
>     Input: "cac"
>     Output: 2
>     Explanation: There are two substrings "a", "c" of string "cac" in the string s.
>
> **Example 3:**
>
>     Input: "zab"
>     Output: 6
>     Explanation: There are six substrings "z", "a", "b", "za", "ab", "zab" of string "zab" in the string s.

周日竞赛的题目，有点小tricky，但也很有趣。题意是说在由26个字母从a到z按顺序循环组成的无限封闭字符串 s中，给定字符串p，找到所有符合条件的p的子串，条件就是这些子串需要在封闭字符串中出现（翻译的好拗口）。

解决这道题目需要明白以下几点：

- 需要注意到封闭字符串s是按照字母顺序排列的，所以每2个相邻字母之间的ASCII码相差1（特殊注意za的情况），那么满足这个条件的子串一定是可以在s中出现的

- 可以看到对于一串连续字母bcde，字母串的长度即为以e结尾的符合要求的子串的个数，那么求p.substring(0,i)中以p.charAt(i)结尾的满足条件的解便可以转化为求以p.charAt(i)结尾的最长连续字母串的长度

然后这便是一个典型的动态规划的问题了，用一个数组维护更新以每个字母结尾的p的连续字母子串的最大长度，最后加和。其实感觉讲的非常不清楚，还是代码更加直观，let's communicate with code ...
<!--more-->

### Java:
``` java
public class Solution {
    public int findSubstringInWraproundString(String p) {
        int[] dp = new int[26];

        int len = 0;
        for (int i = 0; i < p.length(); ++i) {
            // pay attention to [za]
            if (i > 0 && (p.charAt(i) == p.charAt(i-1) + 1 || p.charAt(i) == p.charAt(i-1) - 25)) {
                ++len;
            } else {
                len = 1;
            }

            // update the dp set
            dp[p.charAt(i) - 'a'] = Math.max(dp[p.charAt(i) - 'a'], len);
        }

        int res = 0;
        for (int i = 0; i < 26; ++i) {
            res += dp[i];
        }
        return res;
    }
}
```