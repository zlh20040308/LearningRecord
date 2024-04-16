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
* [Day   5    (2024-04-16)](#4)
  

<span id="0"></span>
## Day 1


### 计划

***

1. 阅读《Rust语言圣经》和《Rust程序设计语言》巩固所有权和生命周期的概念  
2. 写rustlings  
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
3. 写rustlings

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
3. 今天没写rustlings，《Rust编程之道》坏（我）我（太）好（菜）事（了）  

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

1. 写数据结构课设的开题报告    

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
2. 阅读笔记  
第10.3章

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

在了解了这一点后，再来看上面这段代码，你就会开始理解Rust为了达成这一设计理念而做出的努力，假设上面这段代码能通过编译，那么**返回的引用的生命周期就无法在编译时期确定下来**，而Rust不喜欢不确定的东西！因此会拒绝编译这段代码。  

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

编译器就可以在编译时期获取到足够多的信息，函数签名表达以下约束：**返回的引用将在两个参数都有效的情况下有效**，而Rust喜欢确定的东西！因此会使这段代码编译通过  

上面的讨论到此为止，接下来解释一下 **'a** -- **泛型生命周期参数**。


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

<span id="4"></span>
## Day 5  

### 计划  

***  

1. 跟进《RISC-V上的操作系统 - 汪辰 - 2021春》，做lab  
2. 阅读《The Rust Programming Language》  

### 成果  

***  

1. 阅读笔记  

第13.1章--**闭包**  

```rust  
fn main() {
    let mut list = vec![1, 2, 3];
    println!("Before defining closure: {:?}", list);

    let mut borrows_mutably = || list.push(7);

    borrows_mutably();
    println!("After calling closure: {:?}", list);
}
```  

上面这段代码中，在 borrows_mutably 定义和调用之间不再有 println!：当 borrows_mutably 被定义时，它捕获了对 list 的可变引用。在闭包被调用后，我们不再使用该闭包，因此可变借用结束。在闭包定义和闭包调用之间，不允许进行不可变借用以进行打印，因为当存在可变借用时不允许进行其他借用。尝试在那里添加一个 println!，看看你会得到什么错误消息！  

也就是说**可变借用**发生在**闭包定义**的时候，而不是在**闭包调用**的时候，这一点和函数不同  

第13.2章--**迭代器**  

在 Rust 中，迭代器是惰性的，这意味着在调用消耗迭代器的方法以使用它之前，它们不会产生任何效果。  

一些思考：模式匹配  

今天写rustlings的时候发现自己不是很理解模式匹配（我原以为我理解了），在看了书和一些教程后依旧没有解决，下面是例子：  

```rust  
fn main() {
    let my_option: Option<i32> = Some(1);
    match my_option {
        Some(value) => {
            println!("Value: {}", value);
        }
        None => {
            println!("No value");
        }
    }

    println!("Value: {:?}", my_option);
}
```  

这段代码是正确的，在这个例子中 my_option 会匹配第一个分支，`value`会绑定到`my_option`中的数据，问题来了，什么叫做绑定？  

在 Rust 中，"绑定" 是指将一个值与一个变量或模式相关联的过程。当我们声明一个变量并将一个值赋给它时，我们实际上在创建一个绑定。例如：  

```rust  
let x = 42;
```  

在这个例子中，let x 是一个声明语句，它将变量`x` 绑定到值 `42` 上，我们可以使用变量 x 来访问和操作这个值。  

也就是说在进行match匹配时也会发生这样的事，在上面这个例子中，`value`绑定到了`my_option`中的`1`上面，而因为`1`是`i32`类型（内建类型），所以这里会发生拷贝  

接下来稍作改变：  

```rust  
fn main() {
    let my_option: Option<String> = Some(String::from("hello"));
    match my_option {
        Some(value) => {
            println!("Value: {}", value);
        }
        None => {
            println!("No value");
        }
    }

    println!("Value: {:?}", my_option);
}
```  

这段代码无法通过编译，编译器报了这样的错：  

```  
feng@Feng:~/code/Rust/Rust_Learning/projects/closures$ cargo check 
    Checking closures v0.1.0 (/home/feng/code/Rust/Rust_Learning/projects/closures)
error[E0382]: borrow of partially moved value: `my_option`
  --> src/main.rs:12:29
   |
4  |         Some(value) => {
   |              ----- value partially moved here
...
12 |     println!("Value: {:?}", my_option);
   |                             ^^^^^^^^^ value borrowed here after partial move
   |
```  

这里发生了部分移动，`my_option`所绑定的值移动到了`value`里，这是因为`String`这一类型没有实现`Copy`这一特性，在绑定的时候发生的是移动，而不是拷贝  

在明确了上述理论后，我依旧没有解决我所遇到的问题。。。  

```rust  
impl<T: PartialOrd + Copy> LinkedList<T> {
    pub fn merge(list_a: LinkedList<T>, list_b: LinkedList<T>) -> Self {
        let mut merged_list: LinkedList<T> = LinkedList::new();
        let mut current_a: Option<NonNull<Node<T>>> = list_a.start;
        let mut current_b: Option<NonNull<Node<T>>> = list_b.start;

        while let (Some(node_ptr_a), Some(node_ptr_b)) = (current_a, current_b) {
            let node_a = unsafe { &*node_ptr_a.as_ptr() };
            let node_b = unsafe { &*node_ptr_b.as_ptr() };

            if node_a.val <= node_b.val {
                merged_list.add(node_a.val);
                current_a = node_a.next;
            } else {
                merged_list.add(node_b.val);
                current_b = node_b.next;
            }
        }

        // Add remaining elements from list_a, if any
        while let Some(node_ptr) = current_a {
            let node = unsafe { &*node_ptr.as_ptr() };
            merged_list.add(node.val);
            current_a = node.next;
        }

        // Add remaining elements from list_b, if any
        while let Some(node_ptr) = current_b {
            let node = unsafe { &*node_ptr.as_ptr() };
            merged_list.add(node.val);
            current_b = node.next;
        }

        merged_list
    }
}
```  

问题就出现在上述代码中的`while let`代码块中，不难看出的是，每次我们只会更新current_a和current_b的其中一个值，但是`NonNull<T>`这种的类型应该会在匹配时发生移动才对呀！  

后来这才发现是我想当然了，我以为这么复杂的类型应该不会实现`Copy`这一特性，但没办法，穷途末路，我只好打开源码，结果你猜怎么着：  

```rust  
#[stable(feature = "nonnull", since = "1.25.0")]
impl<T: ?Sized> Copy for NonNull<T> {}
```  

这段代码表示对于 NonNull<T> 类型，实现了 Copy 特性。#[stable(feature = "nonnull", since = "1.25.0")] 是一个属性注解，表示此实现是稳定的，并且从 Rust 版本 1.25.0 开始可用。  

OMG...他居然实现了！？！？！那没事了zzzzzzzzzz  

### 待解决的问题  

***  
1. 完成剩余的rustlings  

### 感想与总结  

***  

1. 在今天对于找bug的过程中，我觉得我要修改我在解决错误时，对资料优先级的判定，以前遇到bug，我会：  

```
chatgpt > Rust权威指南 > b站教程 > 技术blog > 源码
```  

今天是一次惨痛的教训，原因就在于`NonNull<T>`这东西比较冷门，基本没人去讲，Rust权威指南也没写，除了在官方文档和源码，你基本不会看到，那如果按照我的这个排错手段，就会浪费很多时间，接下来要改正：  

```
源码 > Rust权威指南 > chatgpt > b站教程 > 技术blog
```  
我太依赖chatgpt了，对于Rust这门迭代速度十分快的语言，他的数据实在不够，我今天一再问他“NonNull<T>有没有实现Copy”，它一直在回我“没有“，真是气死我了。

2. 令我烦躁的是，感觉最近效率低下，我有在以正常的速度进步吗？