---
layout: post
title: LeetCode - Binary Tree Level Order Traversal
date: 2014-05-02 00:13:14
categories : [leetcode]
tags: [tree, breadth-first search]
---

> [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
>
> Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).
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
>       [3],
>       [9,20],
>       [15,7]
>     ]

二叉树的层序遍历，要求从上到下、从左到右输出结点信息，送分题，利用队列先进先出的特性按层次遍历，在遍历的过程中记录结点信息即可。另外由于本题要求已二维数组的形式输出结果，中间可以用一个null结点标注每一层的结束。
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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }

        List<Integer> temp = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        queue.add(null);
        
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if (node != null) {
                temp.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            } else {
                res.add(temp);
                temp = new ArrayList<>();
                if (!queue.isEmpty()) {
                    // if there is more node pending for travel, add null node to mark as the end of one level
                    queue.add(null);
                }
            }
        }
        return res;
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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (root == NULL) {
            return res;
        }
        
        vector<int> temp;
        queue<TreeNode*> queue;
        queue.push(root);
        queue.push(NULL);
        
        while (!queue.empty()) {
            TreeNode* node = queue.front();
            queue.pop();
            if (node != NULL) {
                temp.push_back(node->val);
                if (node->left) {
                    queue.push(node->left);
                }
                if (node->right) {
                    queue.push(node->right);
                }
            } else {
                res.push_back(temp);
                temp.clear();
                if (!queue.empty()) {
                    queue.push(NULL);
                }
            }
        }
        return res;
    }
};
```

