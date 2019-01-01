---
layout: post
title: LeetCode - LRU Cache
date: 2014-05-01 17:13:14
---

> [146. LRU Cache](https://leetcode.com/problems/lru-cache/)
> 
> Design and implement a data structure for [Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU). It should support the following operations: get and put.
> 
> get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
> put(key, value) - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.
> 
> **Follow up:**
> Could you do both operations in **O(1)** time complexity?
> 
> **Example:**
> 
> LRUCache cache = new LRUCache( 2 /* capacity */ );
> 
>     cache.put(1, 1);
>     cache.put(2, 2);
>     cache.get(1);       // returns 1
>     cache.put(3, 3);    // evicts key 2
>     cache.get(2);       // returns -1 (not found)
>     cache.put(4, 4);    // evicts key 1
>     cache.get(1);       // returns -1 (not found)
>     cache.get(3);       // returns 3
>     cache.get(4);       // returns 4

最近最少使用算法，面试很喜欢考的一道题目，可以用LinkedHashMap实现。
<!--more-->

###### Java:
``` java
class LRUCache {
    private Integer capacity;
    private Map<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        cache = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true) {
            // 在afterNodeInsertion函数中调用
            // Returns <tt>true</tt> if this map should remove its eldest entry
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }
    
    public int get(int key) {
        if (cache.containsKey(key)) {
            return cache.get(key);
        } else {
            return -1;
        }
    }
    
    public void put(int key, int value) {
        cache.put(key, value);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```