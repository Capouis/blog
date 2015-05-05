---
layout: default
title: Leetcode Solutions
comments: true
---

leetcode链接： [leetcode]("https://leetcode.com/problemset/algorithms/")

先用python刷一遍leetcode， 请期待我第二版用C++刷。

* Two Sum
给定一个target与一个列表， 求列表中两个数的和为target， 返回两个数下标。
可以用一个字典做一个简单的hash映射， 写的还是挺pythonic的。

```
class Solution:
    # @return a tuple, (index1, index2)
    def twoSum(self, num, target):
        res = {}
        for i in range(len(num)):
            if res.get(target - num[i], None) == None:
                res[num[i]] = i + 1
            else :
                return (res[target - num[i]], i + 1)
```
<br/><br/>

* Add Two Numbers 

模拟两个数的加法过程， 普通的大数加法做法就可以。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
# @return a ListNode
    def addTwoNumbers(self, l1, l2):
        nHead, flag = ListNode(0), 0
        head = nHead
        while flag or l1 or l2:
            node = ListNode(flag)
            if l1:
                node.val += l1.val
                l1 = l1.next
            if l2:
                node.val += l2.val
                l2 = l2.next
            flag = node.val // 10
            node.val %= 10
            head.next, head = node, node
        return nHead.next
  ```
  <br/> <br/>
  
* Longest Substring Without Repeating Characters
  
求一个字符串中最长的子串， 该子串满足里面没有重复的字符。
可以考虑维护两个指针， 分别指向子串的开头与结尾， 可以分析到：当两个指针指向的字符内容相同时， 必然要向后移动开头的指针， 一直移动到与结尾指针所指向的字符不同的位置为止。
  
  ```
  class Solution:
    # @return an integer
    def lengthOfLongestSubstring(self, s):
        ans = 1
        beg, end = 0, 1
        if len(s) == 0:#be care with the Null!
            return 0
        HashTable = set([ord(s[0])])

        while end < len(s):
            k = ord(s[end])
           
            if k in HashTable:
                while s[beg] != s[end]:
                    HashTable.remove(ord(s[beg]))
                    beg += 1
                beg += 1
                end += 1
            else:
                HashTable.add(ord(s[end]))
                end += 1
                if end - beg > ans:
                    ans = end - beg
        return ans
  ```
<br/> <br/>
