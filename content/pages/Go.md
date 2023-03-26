---
title: Go
created: 2022-11-20 01:56:59
modified: 2023-03-26 11:19:47
aliases: [Golang]
tags: [CS, ProgrammingLanguage]
---

-   Go is an open source programming language supported by Google
-   Easy to learn and get started with
-   Built-in concurrency and a robust standard library
-   Growing ecosystem of partners, communities, and tools

## 基础概念

零值可用

```go
var arr []int
// 未初始化，即可使用
arr = append(arr, 1)

// Go为基础类型提供了初始化(零值)，且具有传递性。(struct)可以继承了此能力
var mu sync.Mutex{}

mu.Lock()
mu.Unlock()
```

### Map

1. 不要依赖map的元素便利顺序【hash，无序】
2. map不是线程安全的，不支持并发写
3. 不要尝试获取map中元素的地址
4. 尽量使用 cap 参数创建 map，以提升 map 平均访问性能，减少频繁扩容带来的不必要损耗

### String

1. **string 是不可变的**
2. 零值可用
3. 获取长度的时间复杂度是O(1)
4. 支持通过+/+=操作符进行字符串连接
5. 支持各种比较关系操作符：`==、!= 、>=、<=、>和<`
6. 对非ASCII字符提供原生支持
7. 原生支持多行字符串

在Go语言中，无论是字符串常量、字符串变量还是字符串字面量，类型统一被设置为 `string`。

```go
const s = "string constant"

func main() {
    var s1 string = "string variable"
    fmt.Printf("%T\n", s) // string
    fmt.Printf("%T\n", s1) // string
    fmt.Printf("%T\n", "string literal") // string
}
```

#### 字符串操作效率

1. 做了预初始化的 `strings.Builder` 连接构件字符串的效率最高
2. 带有预初始化的 `bytes.Buffer` `strings.Join` 分列二三位
3. 未做预初始化的 `strings.Buffer` `bytes.Buffer` 和操作符连接在第三档次
4. `fmt.Sprintf` 性能最差

### 变量初始化顺序

在Go包中，包级别变量的初始化按照变量声明的先后顺序进行。

如果某个变量（如变量a）的初始化表达式中直接或间接依赖其他变量（如变量b），那么变量a的初始化顺序排在变量b后面。

未初始化的且不含有对应初始化表达式或初始化表达式不依赖任何未初始化变量的变量，我们称之为"ready for initialization"变量。

包级别变量的初始化是逐步进行的，每一步就是按照变量声明顺序找到下一个"ready for initialization"变量并对其进行初始化的过程。反复重复这一步骤，直到没有"ready for initialization"变量为止。

位于同一包内但不同文件中的变量的声明顺序依赖编译器处理文件的顺序：先处理的文件中的变量的声明顺序先于后处理的文件中的所有变量。

#### 普通求值顺序

Go规定表达式操作数中的所有函数、方法以及channel操作按照从左到右的次序进行求值。

#### 赋值语句的顺序

1. 对于等号左边的下标表达式、指针解引用表达式和等号右边表达式中的操作数，按照普通求值规则从左到右进行求值；
2. 按从左到右的顺序对变量进行赋值。

#### 初始化顺序

![[../images/go-package-calling-order.png]]

## References

- [Go Dev](https://go.dev/)
