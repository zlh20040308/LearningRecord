# 第二章 指令：计算机的语言  

## 2.1 引言  

要控制计算机硬件，就必须使用他的语言。计算机中的单词称为指令，其词汇表称为**指令系统**  

本书所选的指令系统为 **RISC-V** ，2010年初由加州伯克利分校开发  

通过学习如何表示指令，还能发现计算的秘密：**存储程序概念**  

> **存储程序概念**：指令与多种类型的数据不加区别地存储在存储器中并因此易于更改，因此产生了存储程序计算机  

## 2.2 计算机硬件的操作  

每台计算机都必须能够实现算数运算。**RISC-V**汇编语言的符号

```riscv  
add a, b, c
```  

指示计算机将两个变量b和c相加并将总和放入a中  

硬件简单的设计原则：操作数数量可变的硬件比操作数数量不变的硬件更加复杂。这种情况说明了硬件设计三条基本原则之一：  

- 设计原则1：简单源于规整  

    现使用以下两个示例展示用高级语言编写的程序和用初级符号表示的程序间的关系  
    例1：将C程序转换成RISC-V  

    ```c  
    a = b + c;
    d = a - e;
    ```  
    ```riscv
    add a, b, c
    sub d, a, e  
    ```  
    例2：将复杂C程序转换成RISC-V  
    ```c  
    f = (g + f) - (i + j);
    ```  
    ```riscv
    add t0, g, h
    sub t1, i, j  
    sub f, t0, t1    
    ```  

## 2.3 计算机硬件的操作数  

与高级语言程序不同，算术指令的操作数会受到限制；他们必须取自寄存器，而寄存器数量有限并且内建于硬件的特殊位置。  

在**RISC-V**体系结构中，寄存器大小为64位  

> 双字：计算机中一种访问基本单位，通常是64位一组；对应于RISC-V体系结构中的寄存器大小。  
> 
> 字：计算机中的另一种访问基本单位，通常是32位一组  

程序语言的变量和寄存器之间的一个主要区别是寄存器数量有限，在当前**RISC-V**等计算机上通常有32个  

寄存器个数限制为32个的原因可以在我们硬件设计原则的第二条中找到  

- 设计原则2：更少则更快  
    简单来讲，数量过多的寄存器可能会增加时钟周期，因此电信号传输的距离越远，所花费的时间就越长  

### 2.3.1 存储器操作数  

寄存器无法存储复杂且庞大的数据，但内存可以  

因此**RISC-V**必须包含在内存和寄存器之间传输数据的指令——数据传输指令  

要访问内存，就必须提供内存**地址**。内存只是一个大型一维数组，其地址作为该数组的下标  

将数据从内存复制到寄存器的数据传输指令称为载入指令（load）。  

对于内存，地址小的叫做低地址，地址大的叫做高地址  

对于数据，从左到右由高字节到低字节  

高字节存到低地址为大端存储，反之则是小端存储  

RISC-V属于小端编址  

将数据从寄存器复制到内存的数据传输指令称为存储指令（store）。  

> 在许多体系结构中，字的起始地址必须是4的倍数，双字的起始地址必须是8的倍数。该要求称为对齐限制，这会使得数据传输更快，RISC-V和Intel x86没有对齐限制，但是MIPS确实有此限制  

许多程序有着比计算机中数量更多的变量。所以，编译器会尽量把最常用的变量存放在寄存器中，剩下的的放在内存里  

### 2.3.2 常数或立即数操作数  

常数操作是经常出现的，**RISC-V**也提供了另一个版本的算术指令，它的其中一个操作数是常数  

```riscv  
addi x22, x22, 4      // x22 = x22 + 4
```  

## 2.4 有符号数与无符号数  

计算机可以计算正数和负数，因此需要一种表示方法来区分正数和负数。最显然的解决方法是加一个符号位来表示，这种表示方法称为原码  

在寻找一个更具吸引力的替代方法时出现了这样一个问题，如果试图从一个小数中减去一个大数，无符号表示方法的结果是什么？答案是他会尝试从一串前导零中借位，所以会导致有一串前导一  

最终的解决方法是选择简化硬件的表示方法：前导零表示正数，前导一表示负数。这种有符号二进制数的约定称为二进制补码表示法  

## 2.5 计算机中的指令表示  

人们使用计算机指令的方式和计算机识别指令的方式是不同的  

例1：将一条RISC-V指令翻译为机器指令  

```riscv  
add x9, x20, x21  
```  

其十进制表示为：  

|  1  |  2 | 3  | 4 | 5 | 6  |
|-----|----|----|---|---|----|
| 0   | 21 | 20 | 0 | 9 | 51 |  

一条指令的每一段称为一个字段。第一四六个字段组合起来告诉RISC-V计算机该指令执行加法操作。第二个字段给出了作为加法运算的第二个源操作数的寄存器编号（21表示x21），第三个字段给出了加法运算的另一个源操作数（20表示x20）.第五个字段存放要接收总和的寄存器编号（9表示x9）  

其二进制表示为：  

|  7位    |  5位 | 5位   | 3位 |   5位  |   7位   |
|---------|------|-------|-----|-------|---------|
| 0000000 |10101 | 10100 | 000 | 01001 | 0110011 |  

这种指令的设计被称为**指令格式**。从位数可以看出，这个RISC-V指令只需要32位，事实上RISC-V的指令都是32位  

- RISC-V字段  

    给RISC-V字段命名：

|  7位    |  5位 | 5位   | 3位 |   5位  |   7位   |
|---------|------|-------|-----|-------|---------|
| funct7 | rs2 | rs1 | funct3 | rd | opcode |  

以下是各个字段名称的含义：  

- opcode（操作码）：指令的基本操作  
- rd：目的操作数的寄存器，用来存放操作结果  
- funct3一个另外的操作码字段  
- rs1：第一个源操作数寄存器  
- rs2：第二个源操作数寄存器  
- funct7：一个另外的操作码字段  

- I型  

|  12位 | 5位|  3位 |   5位  |   7位   |
|-------|---|-----|-------|---------|
| immediate |rs1| funct3 | rd | opcode |  

- S型  

|  7位    |  5位 | 5位   | 3位 |   5位  |   7位   |
|---------|------|-------|-----|-------|---------|
| immediate[11:5] | rs2 | rs1 | funct3 | immediate[4:0] | opcode |  

## 2.6 逻辑操作  

## 2.7 用于决策的指令  

## 2.8 计算机硬件对过程的支持  

RISC-V软件为过程调用分配寄存器时遵循以下约定  
- x10 ~ x17：八个参数寄存器，用于传递参数或返回值  
- x1：一个返回地址寄存器，用于返回到起点  

### 2.8.1 使用更多的寄存器  

假设对于一个过程，编译器需要比八个参数寄存器更多的寄存器。由于在任务完成后必须掩盖踪迹，调用者所需的所有寄存器都必须恢复到调用该过程之前所存储的值。这种情况是需要将寄存器换出到存储器中的一个例子  

换出寄存器的理想数据结构是栈（stack）——一种后进先出的队列。栈需要一个指向栈中最新分配地址的指针，以指示下一个过程应该放置换出寄存器的位置或寄存器旧值的存放位置。  

在RISC-V中，栈指针是寄存器x2，也称sp。栈指针按照每个被保存或恢复的寄存器按双字进行调整  

栈按照从高到低的地址顺序“增长”。这就意味着可以通过减栈指针将值压栈  

例1：编译一个没有调用其他过程的C程序  

```c  
long long int leaf_example(long long int g, long long int h, long long int i, long long int j)
{
	long long int f;
	f = (g + h) - (i + j);
	return f;
}
```   

参数变量g、h、i和j对应于参数寄存器x10、x11、x12和x13，f对应于x20。编译后的程序如下过程标号开始：  

```riscv   
leaf_example:
```  

下一步是保存该过程使用的寄存器。过程体的C赋值语句与之前的例题相同，使用两个临时寄存器（x5和x6）.因此，需要保存三个寄存器：x5、x6和x20.通过在栈中创建三个双字（24字节）空间，实现将旧值“压入”栈中：  

```riscv   
add sp, sp, -24     //调整栈空间来预留出放置三个值
sd  x5, 16(sp)  
sd  x6, 8(sp)  
sd  x20, 0(sp)  
```  

以下三条语句对应于C函数的过程体：  

```riscv   
add x5, x10, x11  
add x6, x12, x13    
sub x20, x5, x6    
```  

为了返回f的值，将其复制到一个参数寄存器中：  

```riscv   
addi x10, x20, 0   
```  

在返回之前，通过从栈中弹出数据来恢复寄存器的三个旧值：  

```riscv   
ld x20, 0(sp)  
ld x6, 8(sp)  
ld x5, 16(sp)  
addi sp, sp, 24   
```  
通过一个使用返回地址的跳转寄存器结束过程：  
```riscv   
jalr x0, 0(x1)  
```  

为了避免保存和恢复一个其值从未被使用过的寄存器（通常为临时寄存器），RISC-V软件将19个寄存器分成两组：  

- x5 ~ x7 以及 x28 ~ x31：临时寄存器：在过程调用中不被被调用者（被调用的过程）保存

- x8 ~ x9 以及 x18 ~ x27：保存寄存器：在过程调用中必须被保存。  

### 2.8.2 嵌套过程  

不调用其他过程的过程称为叶子（leaf）过程。  

在调用非叶子过程时必须更加注意，例如，假设主程序调用过程A，参数为3，将值传入寄存器x10然后使用  

```riscv  
jal x1, A  
```

再假设过程A通过  

```riscv  
jal x1, B  
```

调用过程B，参数为7，也存入x10。由于A尚未结束任务，所以寄存器x10的使用存在冲突。同样在寄存器x1中的返回地址也存在冲突，因为他现在具有B的返回地址。除非采取措施阻止这类问题发生，否则该冲突将导致过程A无法返回其调用者  

解决的方法也是类似的，只不过这次是调用者要管理这些值  

例1：处理一个计算阶乘的递归过程：  

```c  
long long int fact (long long int n) 
{
    if ( n < 1 ) return (1);
    else return (n * fact(n - 1));
}
```  

RISC-V汇编代码是什么呢？  

参数变量n对应参数寄存器x10.编译后的程序从过程的标签开始，然后在栈中保存两个寄存器，返回地址和x10：  

```riscv  
fact:  
    addi sp, sp,-16    // adjust stack for 2 items  
    sd   x1, 8(sp)     // save the return address 
    sd   x10, 0(sp)    // sace the argument n
```  

第一次调用fact时，sd保存程序中调用fact的地址。下面两条指令测试n是否小于1，如果n>=1则跳转到L1。  


```riscv  
addi x5, x10, -1   // x5 = n - 1  
bge  x5, x0, L1    // if ( n - 1 ) >= 0, go to L1   
```  

如果n小于1，fact将1放入一个值寄存器中以返回1：他将1+0并将和存入x10中。然后从栈中弹出两个已保存的值并跳转到返回地址：  

```riscv  
addi x10, x0, 1  
addi sp, sp, 16  
jalr x0, 0(x1)     
```  

在从栈中弹出两项之前，可以加载x1和x10。因为当n小于1时x1和x10不会改变，所以跳过这些指令。  

如果n不小于1，则参数n递减，然后用递减后的值再次调用fact：  

```riscv  
L1: addi x10, x10, -1  
    jal  x1, fact  
```  

下一条指令是fact的返回位置，其结果在x10中。现在旧的返回地址和旧的参数与栈指针一起被恢复：  

```riscv  
addi x6, x10, 0  
ld   x10, 0(sp)  
ld   x1, 8(sp)  
addi sp, sp, 16  
```  

接下来，参数寄存器x10  得到旧参数与fact(n - 1)结果的乘积，目前在x6中。假设有一个乘法指令可用：  

```riscv  
mul x10, x10, x6  
```  

最后，fact再次跳转：  

```riscv  
jalr x0, 0(x1)  
```  




```riscv  
fact:  
    addi sp, sp,-16    // adjust stack for 2 items  
    sd   x1, 8(sp)     // save the return address 
    sd   x10, 0(sp)    // sace the argument n
 
addi x5, x10, -1   // x5 = n - 1  
bge  x5, x0, L1    // if ( n - 1 ) >= 0, go to L1   
 
addi x10, x0, 1  
addi sp, sp, 16  
jalr x0, 0(x1)     
 
L1: addi x10, x10, -1  
    jal  x1, fact  
 
addi x6, x10, 0  
ld   x10, 0(sp)  
ld   x1, 8(sp)  
addi sp, sp, 16  

mul x10, x10, x6  
  
jalr x0, 0(x1)  
```  


编写函数的一般流程是这样的：   

- 如果说函数内部要用寄存器，优先使用保存寄存器，前面几个用来存参数，如果该函数内部还要用调用其他函数，还要存ra寄存器  
- 函数返回值要放入a0寄存器  
- 编写完后，看看一共用了哪些寄存器，在函数头和函数尾分别加上开辟栈帧和释放栈帧的部分  
- 最后调用ret返回  

虽然按道理来说，如果一个函数中不会再调用其他函数，那么是不需要保存参数寄存器中的值的，可以直接使用参数寄存器，但是，直接使用参数寄存器可能会导致代码可读性和可维护性降低，因为其他人阅读你的代码时可能会期望看到参数被保存到局部寄存器中。  

将参数保存到函数的局部寄存器中，而不是直接在函数内部使用参数寄存器，是一种常见的做法，因为它使得函数内部的代码更加清晰明了。  

下面是两个例子：  

```riscv  

# Calling Convention
# Demo to create a leaf routine
#
# void _start()
# {
#     // calling leaf routine
#     square(3);
# }
#
# int square(int num)
# {
#     return num * num;
# }

	.text			# Define beginning of text section
	.global	_start		# Define entry _start

_start:
	la sp, stack_end	# prepare stack for calling functions

	li a0, 3        // 存放接下来要调用的函数所需的第一个参数
	call square

	# the time return here, a0 should stores the result
stop:
	j stop			# Infinite loop to stop execution

# int square(int num)
square:
	# prologue
	addi sp, sp, -8     // 开辟栈帧
	sw s0, 0(sp)       // 因为下面用到了s0和s1，而这两个寄存器是Callee要维护的，所以要保存好
	sw s1, 4(sp)

	# `mul a0, a0, a0` should be fine,
	# programing as below just to demo we can contine use the stack
	mv s0, a0
	mul s1, s0, s0
	mv a0, s1

	# epilogue
	lw s0, 0(sp)
	lw s1, 4(sp)
	addi sp, sp, 8

	ret

	# add nop here just for demo in gdb
	nop

	# allocate stack space
stack_start:
	.rept 12
	.word 0
	.endr
stack_end:

	.end			# End of file
```  

```riscv  

# Calling Convention
# Demo how to write nested routines
#
# void _start()
# {
#     // calling nested routine
#     aa_bb(3, 4);
# }
#
# int aa_bb(int a, int b)
# {
#     return square(a) + square(b);
# }
#
# int square(int num)
# {
#     return num * num;
# }

	.text			# Define beginning of text section
	.global	_start		# Define entry _start

_start:
	la sp, stack_end	# prepare stack for calling functions

	# aa_bb(3, 4);
	li a0, 3
	li a1, 4
	call aa_bb

stop:
	j stop			# Infinite loop to stop execution

# int aa_bb(int a, int b)
# return a^2 + b^2
aa_bb:
	# prologue
	addi sp, sp, -16
	sw s0, 0(sp)
	sw s1, 4(sp)
	sw s2, 8(sp)
	sw ra, 12(sp)        // 因为这个函数内部还要调用其他函数，所以要保存ra寄存器

	# cp and store the input params
	mv s0, a0  
	mv s1, a1

	# sum will be stored in s2 and is initialized as zero
	li s2, 0

	mv a0, s0
	jal square
	add s2, s2, a0

	mv a0, s1
	jal square
	add s2, s2, a0

	mv a0, s2

	# epilogue
	lw s0, 0(sp)
	lw s1, 4(sp)
	lw s2, 8(sp)
	lw ra, 12(sp)
	addi sp, sp, 16
	ret

# int square(int num)
square:
	# prologue
	addi sp, sp, -8
	sw s0, 0(sp)
	sw s1, 4(sp)

	# `mul a0, a0, a0` should be fine,
	# programing as below just to demo we can contine use the stack
	mv s0, a0
	mul s1, s0, s0
	mv a0, s1

	# epilogue
	lw s0, 0(sp)
	lw s1, 4(sp)
	addi sp, sp, 8

	ret

	# add nop here just for demo in gdb
	nop

	# allocate stack space
stack_start:
	.rept 12
	.word 0
	.endr
stack_end:

	.end			# End of file
```

### 2.8.3 在栈中为新数据分配空间  

最后一点复杂性  

### 2.8.4 在堆中为新数据分配空间  

栈从用户地址的高端开始并向下扩展。地段内存的第一部分是保留的，之后是RISC-V机器代码，通常称为 **代码段**（text segment）。在此之上是静态数据段，用于存放常量和其他静态变量。  

动态数据放在堆上，与栈相向而长  

如果参数超过了八个，RISC-V约定将栈中额外的参数放在帧指针的上方  

```riscv  
sum:  
    addi sp, sp, -12
    sw s0, 0(sp)  
    sw s1, 4(sp)   
    sw ra, 8(sp)  

    mv s0, a0   
    mv s1, a1  
    bge zero, s0, L1
    subi a0, s0, 1
    add a1, s1, s0
    call sum  
    lw s0, 0(sp)  
    lw s1, 4(sp)  
    lw ra, 8(sp)  
    addi sp, sp, 12  
    ret    
L1:  
    mv a0, s1

    lw s0, 0(sp)  
    lw s1, 4(sp)  
    lw ra, 8(sp)  
    addi sp, sp, 12  
    ret  
```  

## 2.9 人机交互  

当前大多数计算机使用字节来表示字符，也就是每个人都遵守的表示方法ASCII。  

## 2.11 指令与并行性：同步  

