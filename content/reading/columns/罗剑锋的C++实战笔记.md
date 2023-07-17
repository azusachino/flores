---
title: 罗剑锋的C++实战笔记
created: 2023-01-06 09:34:23
modified: 2023-07-07 13:54:14
tags: [Column, Tech, C++]
author: 罗剑锋
read: 5
total: 22
status: doing
---

任何人都能写出机器能看懂的代码，但只有优秀的程序员才能写出人能看懂的代码。

## 生命周期

- 编码
- 预处理
- 编译、链接
- 运行

## 编码风格

1. 变量、函数名和名字空间用 snake*case，全局变量加“g*”前缀;
2. 自定义类名用 CamelCase，成员函数用 snake*case，成员变量加“m*”前缀;
3. 宏和常量应当全大写，单词之间用下划线连接;
4. 尽量不要用下划线作为变量的前缀或者后缀(比如 `_local`、`name_`)，很难识别。

```cpp
#define MAX_PATH_LEN 256

int g_sys_flag;

namespace linux_sys {
    void get_rlimit_core();
}

class FilePath final {
    public:
        void set_path(const string& str);
    private:
        string m_path;
        int    m_level;
};
```

## 预处理

1. 预处理不属于 C++ 语言，过多的预处理语句会扰乱正常的代码，除非必要，应当少用慎 用;
2. “#include”可以包含任意文件，所以可以写一些小的代码片段，再引进程序里;
3. 头文件应该加上“Include Guard”，防止重复包含;
4. “#define”用于宏定义，非常灵活，但滥用文本替换可能会降低代码的可读性;
5. “条件编译”其实就是预处理编程里的分支语句，可以改变源码的形态，针对系统生成 最合适的代码。

## References

- [罗剑锋的 C++实战笔记 - 极客时间](http://localhost)
