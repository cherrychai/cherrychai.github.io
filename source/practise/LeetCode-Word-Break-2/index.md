---
layout: post
title: LeetCode - Word Break II
date: 2014-03-04 16:13:14
---

> [140. Word Break II](https://leetcode.com/problems/word-break-ii/)
>
> Given a **non-empty** string s and a dictionary wordDict containing a list of **non-empty** words, add spaces in s to construct a sentence where each word is a valid dictionary word. You may assume the dictionary does not contain duplicate words.
>
> Return all such possible sentences.
>
> For example, given
>
> s = "catsanddog",
>
> dict = ["cat", "cats", "and", "sand", "dog"].
>
> A solution is ["cats and dog", "cat sand dog"].

[LeetCode - Word Break](http://chaihua.me/leetcode/2017/03/01/LeetCode-Word-Break/)的加强版，除了判断是不是能够拆分，还要求输出所有可能的拆分方案。一种方法就是在上一道题目解法的基础上增加一步在判断为true的时候将单词记录下来，最后再进行一个DFS获得所有解。

算是LeetCode上需要写的比较长的一道题目了，我只想说离开IDE裸写这道题还是犯了很多错误的，而且居然下意识用了foreach也是醉醉的了，我都不知道已经多久没有用到它了，不得不说在IDE里面写代码还是省不少心的，不过也会惯坏自己，好多放到IDE里面非常明了的错误竟然肉眼检查不出来了，费了好大劲修复了所有的compile error之后就一遍AC了，枉我一直标榜自己纸上写代码的功力，近年来竟也退化了太多，果然不练习手就慢慢生了。
<!--more-->

###### Java:
``` java
public class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0) {
            new ArrayList<>();
        }
        
        int sLen = s.length();
        Map<Integer, List<String>> dp = new HashMap<>();
        dp.put(0, new ArrayList<>());
        for (int i = 1; i <= sLen; ++i) {
            for (int j = 0; j < i; ++j) {
                if (dp.containsKey(j) && wordDict.contains(s.substring(j, i))) {
                    List<String> words = new ArrayList<>();
                    if (dp.containsKey(i)) {
                        words = dp.get(i);
                    }
                    // add all matched word into dp map
                    words.add(s.substring(j, i));
                    dp.put(i, words);
                }
            }
        }
        
        String solution = "";
        List<String> res = new ArrayList<>();
        DFS(dp, sLen, solution, res);
        return res;
    }
    
    public void DFS(Map<Integer, List<String>> dp, int index, String solution, List<String> res) {
        if (index == 0) {
            res.add(solution);
        }
        if (dp.containsKey(index)) {
            List<String> words = dp.get(index);
            for (String word : words) {
                if (solution.length() == 0) {
                    // first word has no space before it
                    solution = word;
                } else {
                    solution = word + " " + solution;
                }
                DFS(dp, index - word.length(), solution, res);
                if (solution.length() == word.length()) {
                    // first word has no space before it
                    solution = "";
                } else {
                    solution = solution.substring(word.length() + 1, solution.length());
                }
            }
        }
    }
}
```