# LearningRecord

***

这是我参加2024春夏季开源操作系统训练营的学习文档

## Toc

***

***四月***
* [Day   1    (2024-04-12)](#0)  
* [Day   2    (2024-04-13)](#1)  
* [Day   3    (2024-04-14)](#2)  
* [Day   4    (2024-04-15)](#3)

<span id="0"></span>
## Day 1


### 计划

***

1. 阅读《Rust语言圣经》和《Rust程序设计语言》巩固所有权和生命周期的概念  
2. 写rustings  
3. 用Rust写leetcode  

### 成果 

***

1. 完成了对知识点的巩固，Rust在内存回收的机制上比C/C++（手动管理）更暴力--出作用域不管是在栈上还是在堆上的内存一律回收，比Java（GC）更高效--得益于Rust的查机制，内存回收策略更简单（暴力）  
2. 除了剩余的10道algorithm，其余部分全部完成  
3. 还没开始刷，发现迭代器和闭包学得不够扎实，还是不太熟悉Rust这门语言

### 待解决的问题

***

### 感想与总结

***

<span id="1"></span>
## Day 2


### 计划

***

1. 跟进《RISC-V上的操作系统 - 汪辰 - 2021春》，做lab  
2. 阅读《Rust编程之道》第三章类型系统  
3. 写rustings

### 成果

***

1.  RISC-V调试指令  
```shell
#一律都要做的
cd ~/asm/<practice>
make
#查看指令的十六进制形式
make hex
#反汇编
make code
#退出反汇编
q
#运行代码
make debug
#退出gdb
Ctrl-C
quit
y
#单步调试（single instruction）
si
#重复上一次的命令
直接回车
```  
2. 阅读笔记  
- 字符串类型：  
    &str 由两部分组成：指针和长度信息，存储于栈上
    str字符串序列存储于堆上
    包含了动态大小类型地址信息和携带了长度信息的指针，叫作胖指针（Fat Pointer），所以&str是一种胖指针  
    这里我不是很理解的一句话是“对于编译器来说，str类型的大小是无法确定的”，但是后来我解决了，具体参考[这篇文章](https://zhuanlan.zhihu.com/p/597405802)，简单来就是要分清楚类型大小和值大小的区别。比如说：

```cpp
char a = 1;//那么此时a的类型大小是一字节，但是值大小是1
```  

同样的，对于str来说：  

```rust
let other: &str = "other_hello";//你不能脱离"other_hello"去谈str的类型大小
```  
    再举个例子加深印象，假设有人问你u32的大小，你可以很确定地说是四字节，但是有人问你str的大小时，你是答不上来的，对于编译器也是如此，这取决于字符串序列的大小  

- 整数类型：
    - 在用Rust编程的时候，应尽量显式声明类型，这样可以避免一些麻烦
- 孤儿规则（Orphan Rule）
    - 孤儿规则规定，如果要实现某个trait，那么该trait和要实现该trait的那个类型至少有一个要在当前crate中定义。在代码清单3-28中，Add trait和u32、u64都不是在当前crate中定义的，而是定义于标准库中的。如果没有孤儿规则的限制，标准库中u32类型的加法行为就会被破坏性地改写，导致所有使用u32类型的crate可能产生难以预料的Bug。  

```rust
代码清单3-28：尝试重载整数的加法操作
use std::ops::Add;
impl Add<u64> for u32 {
    type Output = u64;
    fn add(self, other: u64) -> Self::Output {
        (self as u64) + other
    }
}
fn main() {
    let a = 1u32;
    let b = 2u64;
    assert_eq!(a + b, 3);
}
```  
3. 今天没写rustings，《Rust编程之道》坏（我）我（太）好（菜）事（了）  

### 待解决的问题  

***

1. trait对象和动态分发不是很理解，by the way，《Rust编程之道》这本书的编排方很劝退，按顺序读下去是个错误，起码不是我现在能读得下去的书，太浪费时间了，还是乖去看《Rust程序设计语言》罢（悲）  
2. 我的markdown编辑习惯太差了，要重新学一下了。。。  

### 感想与总结

***

1. Rust基本过了一遍了，明天开始二刷《Rust程序设计语言》  
2. RISC-V的学习优先级要提高，之前学过Mips，这个也该不会太难  

<span id="2"></span>
## Day 3


### 计划

***  

1. 今天一整天都在写数据结构课设的开题报告    

### 成果 

***

1. nothing~

### 待解决的问题

***

### 感想与总结

***  

<span id="3"></span>
## Day 4


### 计划  

***  

1. 跟进《RISC-V上的操作系统 - 汪辰 - 2021春》，做lab  
2. 阅读《The Rust Programming Language》  

### 成果  

***

1. 学了一些RISC-V指令  
2. 第10.3章阅读笔记  

    - 对于生命周期和所有权的一些思考：  

        先来看一段代码：  

```rust  
fn max_of_refs(a: &i32, b: &i32) -> &i32 {
    if *a > *b {
        a
    } else {
        b
    }
}
```  

如果你稍微有学过点Rust，就知道上面这段代码是有问题的--“你应该指明生命周期！”  

但为什么要指明生命周期呢--”这样就可以确定返回的引用的生命周期了，有助于借用检测器在编译时进行检查“  

我一直不理解的是Rust为什么这么”蠢“，这个函数返回哪个引用你就拥有返回引用的生命周期不就行了吗？  

我不理解的点就出现在这，我对Rust提出了自己的期望：我希望引用的有效性是在**运行时**检查的  

确实，在许多其他编程语言中，引用的有效性可能是在运行时检查的，因此可能会发生悬垂引用等问题。  
但是，但是Rust要求十分严格，要求在**编译时**就能直接确定每个值和引用的生命周期。  
Rust 的设计目标之一是在编译时提供尽可能多的保障，以减少运行时错误的发生。  

在了解了这一点后，再来看上面这段代码，你就会开始理解Rust为了达成这一设计理念而做出的努力，假设上面这段代码能通过编译，那么返回的引用的生命周期就无法在编译时期确定下来，而Rust不喜欢不确定的东西！因此会拒绝编译这段代码。  

而一旦我们为其指定了生命周期：  

```rust  
fn max_of_refs<'a>(a: &'a i32, b: &'a i32) -> &'a i32 {
    if *a > *b {
        a
    } else {
        b
    }
}
```  

编译器就可以在编译时期获取到足够多的信息，函数签名表达以下约束：返回的引用将在两个参数都有效的情况下有效。，而Rust喜欢确定的东西！因此会使这段代码编译通过  

上面的讨论到此为止，接下来解释一下 **'a** -- 泛型生命周期参数。


观察下面这段代码，我之前一直不理解的是 **'a** 为什么是一个泛型参数，既然是泛型，那么在调用这个函数的时候 **'a** 会被替代成什么呢?  

```rust  
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```  

**《The Rust Programming Language》** 给出了答案：  

> 当我们将具体引用传递给 longest 时，用于替换 'a 的具体生命周期是 x 的作用域与 y 的作用域重叠部分。换句话说，泛型生命周期 'a 将得到一个等于 x 和 y 的生命周期中较小者的具体生命周期。因为我们已经用相同的生命周期参数 'a 对返回的引用进行了注释，所以返回的引用也将在 x 和 y 的生命周期中较小者的长度内有效。



### 待解决的问题

***  

### 感想与总结  

***  

1. 在今天的阅读过程中，我加深了对Rust的一些设计理念的理解，我不能用看待C++或是Java的方式要求Rust，当然，对比是有好处的，这样才会产生矛盾，而这矛盾往往就是Rust不同于其他语言的地方  
2.  还有就是今天在读《Rust程序设计语言》发生了一些不愉快的事情，译者添油加醋地添加了几段不属于原书的解释，但对我来说没有达到解惑的作用，反而是我更困惑了，接下来看英文原版了，当然我并没有对译者有什么恶意，但这确实是一次不愉快的体验

