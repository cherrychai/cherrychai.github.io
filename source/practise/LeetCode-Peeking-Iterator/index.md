---
layout: post
title: LeetCode - Peeking Iterator
date: 2014-10-26
---

> [284. Peeking Iterator](https://leetcode.com/problems/peeking-iterator/)
>
> Given an Iterator class interface with methods: next() and hasNext(), design and implement a PeekingIterator that support the peek() operation -- it essentially peek() at the element that will be returned by the next call to next().
>
> Here is an example. Assume that the iterator is initialized to the beginning of the list: [1, 2, 3].
>
> Call next() gets you 1, the first element in the list.
>
> Now you call peek() and it returns 2, the next element. Calling next() after that still return 2.
>
> You call next() the final time and it returns 3, the last element. Calling hasNext() after that should return false.
>
> Follow up: How would you extend your design to be generic and work with all types, not just integer?

基于Iterator实现一个PeekingIterator，使得这个类中包含一个peek()方法返回最顶端的元素，因为直接调用next()会遍历所有元素，所以需要定义一个变量保存每次next()的结果，比较简单，实现方法如下。
<!--more-->

### Java:
``` java
// Java Iterator interface reference:
// https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html
class PeekingIterator implements Iterator<Integer> {
    private Integer next;
    private Iterator<Integer> iter;
    private boolean empty;

    public PeekingIterator(Iterator<Integer> iterator) {
        // initialize any member here.
        this.iter = iterator;
        if (iter.hasNext()) {
            next = iter.next();
            empty = false;
        } else {
            next = null;
            empty = true;
        }
    }

    // Returns the next element in the iteration without advancing the iterator.
    public Integer peek() {
        return next;
    }

    // hasNext() and next() should behave the same as in the Iterator interface.
    // Override them if needed.
    @Override
    public Integer next() {
        Integer res = next;
        if (iter.hasNext()) {
            next = iter.next();
        } else {
            next = null;
            empty = true;
        }
        return res;
    }

    @Override
    public boolean hasNext() {
        return !empty;
    }
}
```
