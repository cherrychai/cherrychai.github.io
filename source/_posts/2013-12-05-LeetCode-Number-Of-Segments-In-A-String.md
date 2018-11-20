---
layout: post
title: LeetCode - Number of Segments in a String
date: 2013-12-05 00:13:14
categories : [leetcode]
tags: [string]
---

> [434. Number of Segments in a String](https://leetcode.com/problems/number-of-segments-in-a-string/)
>
> Count the number of segments in a string, where a segment is defined to be a contiguous sequence of non-space characters.
>
> Please note that the string does not contain any **non-printable** characters.
>
> **Example:**
>
>     Input: "Hello, my name is John"
>     Output: 5

周日竞赛的第一题，当然也是最简单的一道题目，就是给定一个句子，求出用空格相隔的区块的个数，一开始居然还审错题目了，去求了最大长度的区块长度，我也是服了自己，英文不是母语真是伤不起啊。

直接逐个字符遍历吧，遍历的过程中遇到空格时区块个数+1，注意特殊处理一下string开头的连续空格，以及句子结尾没有空格的情况即可。

以前一直用C++做题，这次突然想说写个Java吧，不曾想居然从来没有用Java取过String中的单个字符（工程中确实很难用到），囧，于是第一次用到了charAt，感觉换个语言做做题也是可以涨很多知识的，毕竟现在也是Java工程师了，很多东西还是需要熟练掌握的，加油。
<!--more-->

###### C++:
``` c++
class Solution {
public:
    int countSegments(string s) {
        int len = s.size();
        int res = 0;
        bool sequence = false;
        for (int i = 0; i < len; ++i) {
            if (s[i] == ' ') {
                if (sequence == true) {
                    ++res;
                    sequence = false;
                }
            } else {
                sequence = true;
            }
        }

        // remember to +1 if there's no space at the end of the string

        if (sequence == true) {
            ++res;
        }
        return res;
    }
};
```

###### Java:
``` java
public class Solution {
    public int countSegments(String s) {
        if (s == null) {
            return 0;
        }
        int res = 0;
        int sequence = 0;
        for (int i = 0; i < s.length(); ++i) {
            if (s.charAt(i) == ' ') {
                if (sequence == 1) {
                    ++res;
                    sequence = 0;
                }
            } else {
                sequence = 1;
            }

        // remember to +1 if there's no space at the end of the string
        return res + sequence;
    }
}
```