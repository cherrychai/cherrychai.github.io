---
layout: post
title: LeetCode - Add Binary
date: 2013-11-27
---

> [67. Add Binary](https://leetcode.com/problems/add-binary/)
>
> Given two binary strings, return their sum (also a binary string).
>
> For example,
>
> a = "11"
>
> b = "1"
>
> Return "100".

看似非常简单的一道题目，用string存储两个二进制数，对其做加法，结果仍然存放在string中，真正写起来确实有几个需要注意的点：

- 首先，a和b的长度未知，与其做题的过程中去判断，不如进行预处理，使得a的长度小于等于b的长度。

- 其次，string可以直接使用加法增加字符，但是是从右侧插入，所以最后需要做一个倒转，这个倒转可以通过交换首尾字符完成。

- 最后，对于每一位上面的二进制字符相加后，对2取余作为该位的和，除2作为溢出位带入下一轮计算，切记最后要把溢出位加上。
<!--more-->

### C++:
``` c++
class Solution {
public:
    string addBinary(string a, string b) {
        // make a.size() <= b.size()
        if (a.size() > b.size()) {
            string tmp = a;
            a = b;
            b = tmp;
        }
        
        string res;
        int carryon = 0;
        int alen = a.size();
        int blen = b.size();
        for (int i = 1; i <= alen; ++i) {
            int sum = (a[alen-i] - '0') + (b[blen-i] - '0') + carryon;
            res += '0' + sum % 2;
            carryon = sum / 2;
        }
        
        for (int i = alen + 1; i <= blen; ++i) {
            int sum = (b[blen-i] - '0') + carryon;
            res += '0' + sum % 2;
            carryon = sum / 2;
        }
        
        if (carryon == 1) {
            res += '1';
        }
        
        // revert the string
        int len = res.size();
        for (int i = 0; i < len/2; ++i) {
            char tmp = res[i];
            res[i] = res[len-1-i];
            res[len-1-i] = tmp;
        }
        
        return res;
    }
};
```