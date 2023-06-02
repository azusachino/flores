---
title: Algorithm
created: 2022-09-29 07:50:06
modified: 2023-03-28 17:24:51
tags: [CS]
---

## Sliding Window

Generally, the sliding window problems have some kind of aggregate, atMost k, largest substring, min substring with k etc. They're always "given an array or string, find some computed sub problem" value.

### Slideing Window Ref

- [992. Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/solutions/523136/java-c-python-sliding-window/)

## Binary Search

1. correctly initialize the boundary variables `l` and `r`. Only one rule: set up the boundary to **include all possible elements**
2. Decide return value.**After exiting the while loop, `l` is the minimal k satisfying the `condition` funtion**
3. Design the `condition` function

```py
def binary_search(arr) -> int:
  def condition(value) -> bool:
    pass
  l, r = 0, len(arr)
  while l < r:
    m = l + (r - l) // 2
    if condition(m):
      r = m
    else:
      l = m + 1
  # left insert position
  return l 
```

### Binary Search Ref

- [Powerful Ultimate Binary Search Template. Solved many problems.](https://leetcode.com/problems/koko-eating-bananas/solutions/769702/python-clear-explanation-powerful-ultimate-binary-search-template-solved-many-problems/)
