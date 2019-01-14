---
layout: post
title: LeetCode - Word Break
date: 2014-02-2
---

> [139. Word Break](https://leetcode.com/problems/word-break/)
>
> Given a **non-empty** string s and a dictionary wordDict containing a list of **non-empty** words, determine if s can be segmented into a space-separated sequence of one or more dictionary words. You may assume the dictionary does not contain duplicate words.
>
> For example, given
>
> s = "leetcode",
>
> dict = ["leet", "code"].
>
> Return true because "leetcode" can be segmented as "leet code".

某人亚马逊面到的一道题，给定一个包含多个不重复单词的词典和一个字符串，判断该字符串能不能拆分成若干个字典中的单词，当然亚马逊的面试是要求给出一组solution的，这道题比较简单，只需要判断能不能拆分就可以了。

典型的动态规划，思路清晰明了，判断s是不是true的问题又可以转化为判断s.substring(0,i)是不是true，如果s.substring(0,i)为true且s.substring(i,j)是字典中的单词，那么s.substring(0,j)为true，然后问题便解决了。
<!--more-->

### Java:
``` java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0) {
            return true;
        }

        int sLen = s.length();
        boolean dp[] = new boolean[sLen];
        for (int i = 0; i < sLen; ++i) {
            dp[i] = false;
            // pay attention that the substring begins at the specified beginIndex and extends to the character at index endIndex - 1
            if (wordDict.contains(s.substring(0, i+1))) {
                dp[i] = true;
                continue;
            }
            for (int j = 0; j < i; ++j) {
                if (dp[j] == true && wordDict.contains(s.substring(j+1, i+1))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[sLen-1];
    }
}
```

### Java:
``` java
public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0) {
            return true;
        }

        int sLen = s.length();
        boolean dp[] = new boolean[sLen+1];
        // use dp[0] as flag for initial state
        dp[0] = true;

        for (int i = 1; i <= sLen; ++i) {
            dp[i] = false;
            for (int j = 0; j < i; ++j) {
                if (dp[j] == true && wordDict.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[sLen];
    }
}
```