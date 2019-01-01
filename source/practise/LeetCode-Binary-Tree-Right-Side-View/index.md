---
layout: post
title: LeetCode - Binary Tree Right Side View
date: 2014-05-02 17:13:15
---

> [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
>
> Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.
> 
> For example:
> Given the following binary tree,
> 
>        1            <---
>      /   \
>     2     3         <---
>      \     \
>       5     4       <---
>
> You should return [1, 3, 4].

同[LeetCode - Binary Tree Level Order Traversal 2](http://localhost:4000/leetcode/2017/05/03/LeetCode-Binary-Tree-Level-Order-Traversal-2/)一样，也是[LeetCode - Binary Tree Level Order Traversal](http://chaihua.me/leetcode/2017/05/02/LeetCode-Binary-Tre-Level-Order-Traversal/)的变形，二叉树的层序遍历，如果是从左向右遍历则需要在遍历到每一层的最后一个节点时进行输出，如果是从右向左遍历则需要输出没一层的第一个节点。
<!--more-->

###### Java（从左向右遍历）:
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
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        if (root == null) {
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        queue.add(null);
        TreeNode node = queue.peek();
        while (!queue.isEmpty()) {
            if (queue.peek() == null) {
                res.add(node.val);
                queue.poll();
                if (!queue.isEmpty()) {
                    queue.add(null);
                }
                continue;
            }
            node = queue.poll();
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
        return res;
    }
}
```

###### Java（从右向左遍历）:
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
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        queue.add(null);
        res.add(root.val);
        while (!queue.isEmpty()) {
            TreeNode current = queue.poll();
            if (current == null) {
                if (!queue.isEmpty()) {
                    res.add(queue.peek().val);
                    queue.add(null);
                }
            } else {
                if (current.right != null) {
                    queue.add(current.right);
                }
                if (current.left != null) {
                    queue.add(current.left);
                }
            }
        }
        return res;
    }
}
```