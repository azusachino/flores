---
title: Classic Algorithm Problems
created: 2022-09-29 07:50:06
modified: 2023-03-28 17:24:51
tags: [CS, Algorithm]
---

## Find missing positive

```py
def find_missing_positive(nums):
  N = len(nums)
  for i in range(N):
    # 该算法的思路是把所有大于等于 1 且小于等于 n 的数放入桶中，然后遍历桶，找到第一个空桶的下标，该下标加一就是数组中不存在的最小正整数。在把数放入桶中的过程中，可以通过交换元素的方式来把数放到正确的位置上。如果遍历完整个桶后没有找到空桶，说明数组中不存在正整数，最小的正整数应该是 n + 1。
    while i <= nums[i] <= N and nums[nums[i] - 1] != nums[i]:
      nums[nums[i]-1], nums[i] = nums[i], nums[nums[i]-1]
  
  for i in range(N):
    if nums[i] != i+1:
      return i + 1
  return N + 1
```

## Find next permutation

```python
def next_permutation(nums):
  N = len(nums)
  i = N - 2
  # find reversed-order from right to left
  while i >= 0 and nums[i] >= nums[i+1]:
    i -= 1
  # the nums is fully reversed-order
  if i == -1:
    return nums[::-1]
  else:
    # find the first j is greater than i
    j = N - 1
    while j >= 0 and nums[j] <= nums[i]:
      j -= 1
    nums[i], nums[j] = nums[j], nums[i]
    # reverse i-right, make it the smallest permutation (compare to current)
    l, r = i+1, N-1
    while l < r:
      nums[l], nums[r] = nums[r], nums[l]
      l += 1
      r -= 1
  return nums
```
