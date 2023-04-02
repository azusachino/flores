---
title: C语言
created: 2022-08-23 00:00:00
modified: 2023-04-01 22:23:31
tags: [CS, ProgrammingLanguage]
---

The Programming Language C

## Main Concepts

### The Progress of `gcc -o main main.c`

### 内存对齐

1. 首先确定每个域的长度和对齐长度，原始类型的对齐长度和类型的长度一致。
2. 每个域的起始位置要和其对齐长度对齐，如果无法对齐，则添加 padding 直至对齐。
3. 结构体的对齐大小和其最大域的对齐大小相同，而结构体的长度则四舍五入到其对齐的倍数。

### MISC

`memset` 用于将申请到的内存区域清零

```c
#include <stdio.h>
#include <string.h>

int main() {
    int array[10];
    // Use memset to set all elements of the array to 0 
    memset(array, 0, sizeof(array));

    // Print the contents of the array 
    for (int i = 0; i < 10; i++) { 
        printf("%d ", array[i]); 
    }
    return 0;
}
```

#### Socket & File Descriptor

A socket is an abstract representation of a network connection, and a file descriptor is a low-level integer identifier that is used to access a file or other input/output resource.

## Resources

- [Beej's Guide to C Programming](https://beej.us/guide/bgc/)
- [CSE325 Lecture Videos](https://www.youtube.com/playlist?list=PL3GWPKM6L17H0RyU2o7p9gCnepjSTaHia)

## References

- [What does gcc main.c do?](https://connorbrereton.medium.com/what-does-gcc-main-c-do-bc96824a60a4)
