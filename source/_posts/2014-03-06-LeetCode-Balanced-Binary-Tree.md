---
layout: post
title: LeetCode - Balanced Binary Tree
date: 2014-03-06 00:13:14
categories : [leetcode]
tags: [tree, depth-first search]
---

> [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
>
> Given a binary tree, determine if it is height-balanced.
>
> For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

加班回到家已经11点半了，随便刷到简单的题目，求一个二叉树是不是平衡二叉树（本题目中定义为左右子树的深度相差不超过1）。

没什么算法，只需知道如果一个二叉树的左右子树都是平衡二叉树，然后左右子树的深度相差不超过1，那么这个二叉树也是平衡二叉树，如此可见，就是深搜的过程中记录树的深度便可以了。

实现了两种判断二叉树是否平衡并求树的深度的方式：

- Java版的把深度作为返回值，如果是平衡二叉树则返回树的深度，如果不平衡则返回-1

- C++版的在主函数中定义一个深度的变量，然后将此变量的引用作为一个参数传进递归函数中，遍历的的过程中更新其值，同时将是否平衡作为返回值，平衡返回true，不平衡则返回false

传引用进入函数然后通过指针更新其值的时候还是有些地方需要注意，许久不写其实我也有些生疏，这种小细节考考校招同学的基本功还是可以有的。
<!--more-->

###### Java:
``` java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (Depth(root) == -1) {
            return false;
        } else {
            return true;
        }
    }
    
    public int Depth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = Depth(root.left);
        int right = Depth(root.right);
        if (left >= 0 && right >= 0 && Math.abs(left - right) <= 1) {
            return Math.max(left, right) + 1;
        }
        return -1;
    }
}
```

###### C++:
``` c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        int depth;
        return isBalancedTree(&depth, root);
    }
    
    bool isBalancedTree(int* depth, TreeNode* root) {
        if (root == NULL) {
            *depth = 0;
            return true;
        }
        int left, right;
        if (isBalancedTree(&left, root->left) && isBalancedTree(&right, root->right)) {
            if (abs(left - right) <= 1) {
                *depth = (left > right ? left : right) + 1;
                return true;
            }
        }
        return false;
    }
};
```