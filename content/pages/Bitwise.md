---
title: Bitwise
created: 2022-09-29 07:50:06
modified: 2023-03-28 17:24:51
tags: [CS, Algorithm]
---

## Basic Operations

- AND (&): `101 & 011 = 001`
- OR (|): `101 | 011 = 111`
- XOR (^): `101 ^ 011 = 110`
- NOT (~): `~101 = 010`
- Left Shift (<<): `5 << 2 => 101 -> 10100 = 20`
- Right Shift (>>): `5 >> 2 => 101 -> 001 = 1`

## XOR

- find single number
  - `ret ^= nums[i]`
- find missing number `[3, 0, 1]`
  - `ret = i ^ nums[i]`
