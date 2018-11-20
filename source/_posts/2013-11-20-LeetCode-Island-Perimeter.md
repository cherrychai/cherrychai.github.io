---
layout: post
title: LeetCode - Island Perimeter
date: 2013-11-20 00:13:14
categories: [leetcode]
tags: [hash Table, math]
---

> [463. Island Perimeter](https://leetcode.com/problems/island-perimeter/)
>
> You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded by water, and there is exactly one island (i.e., one or more connected land cells). The island doesn't have "lakes" (water inside that isn't connected to the water around the island). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.
>
> **Example:**
>
>     [[0,1,0,0],
>      [1,1,1,0],
>      [0,1,0,0],
>      [1,1,0,0]]
>
> Answer: 16
>
> Explanation: The perimeter is the 16 yellow stripes in the image below:
>
> ![sss](/img/posts/20161120_LeetCode_Island_Perimeter_0.png)

周日竞赛中的一个题目，1代表陆地，0代表海洋，求陆地组成的岛屿的周长，也就是图中黄线的长度。

一开始把题目想简单了，打算直接暴力破解，对于每一块陆地分别判断上/左/下/右是否构成周长，如果是则+1，结果直接超时了。

然后就换了一种思路，不做加法，而是做减法，每一个陆地都有4条边，但是如果周围也是陆地则需要丢掉2条边，这样只需要在**从左到右&从上到下**遍历的过程中记录陆地的个数以及**右侧&下方**的相邻陆地的个数即可。
<!--more-->

###### C++:
``` c++
class Solution {
public:
    int islandPerimeter(vector<vector<int>>& grid) {
        int rowSize = grid.size();
        int colSize = grid[0].size();
        int island = 0;
        int neighbour = 0;
        for (int i = 0; i < rowSize; ++i) {
            for (int j = 0; j < colSize; ++j) {
                if (grid[i][j] == 1) {
                    ++island;
                    if (j < colSize - 1 && grid[i][j+1] == 1) {
                        ++neighbour;
                    }
                    if (i < rowSize - 1 && grid[i+1][j] == 1) {
                        ++neighbour;
                    }
                }
            }
        }
        return (4 * island - 2 * neighbour);
    }
};
```