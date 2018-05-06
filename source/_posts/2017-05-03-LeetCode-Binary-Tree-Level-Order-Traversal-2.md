---
layout: post
title: LeetCode - Binary Tree Level Order Traversal 2
comments: true
author: "柴华"
date: 2017-05-03
header-img: 
categories : [LeetCode]
tags: [Tree, Breadth-first Search]
---

> [107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)
>
> Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).
>
> For example:
>
> Given binary tree [3,9,20,null,null,15,7],
> 
>       3
>      / \
>     9  20
>       /  \
>      15   7
>
> return its level order traversal as:
> 
>     [
>       [9,20],
>       [15,7],
>       [3]
>     ]

[LeetCode - Binary Tree Level Order Traversal](http://chaihua.me/leetcode/2017/05/02/LeetCode-Binary-Tre-Level-Order-Traversal/)的变形，二叉树的层序遍历，但是需要**从上到下**，从左到右输出结点信息。方法多种多样，挑几种最常见的说下：

- 最呆的一种，在正常层序遍历之后将结果进行翻转，简单粗暴

- 层序遍历的过程中将每一层的结果数组放入栈中，然后出栈入二维数组，其实跟第一种方法思路类似

- 仍然利用队列先进先出的特性输出每一层的结点信息，不同的是这次要对二叉树递归的进行层次遍历，然后在回溯的过程中输出结点信息，下面C++版本实现的便是这种方法，Java版本递归思路一样，只不过没有用队列
<!--more-->

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
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> res;
        queue<TreeNode*> queue;
        if (root != NULL) {
            queue.push(root);
            levelOrderBottom(res, queue);
        }
        return res;
    }

    void levelOrderBottom(vector<vector<int>> &res, queue<TreeNode*> queue) {
        if (queue.empty()) {
            return;
        }

        int size = queue.size();
        vector<int> temp;
        for (int i = 0; i < size; ++i) {
            TreeNode* node = queue.front();
            queue.pop();
            temp.push_back(node->val);
            if (node->left) {
                queue.push(node->left);
            }
            if (node->right) {
                queue.push(node->right);
            }
        }
        
        levelOrderBottom(res, queue);
        
        res.push_back(temp);
    }
};
```

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
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        List<TreeNode> nodes = new ArrayList<>();
        if (root != null) {
            nodes.add(root);
            levelOrderBottom(res, nodes);
        }
        return res;
    }

    public void levelOrderBottom(List<List<Integer>> res, List<TreeNode> nodes) {
        if (nodes.isEmpty()) {
            return;
        }
        int size = nodes.size();
        List<Integer> temp = new ArrayList<>();
        List<TreeNode> childNodes = new ArrayList<>();
        nodes.forEach(
            n -> {
                temp.add(n.val);
                if (n.left != null) {
                    childNodes.add(n.left);
                }
                if (n.right != null) {
                    childNodes.add(n.right);
                }
            }
        );
        
        levelOrderBottom(res, childNodes);
        
        res.add(temp);
    }
}
```
