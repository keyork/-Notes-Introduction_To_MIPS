# 1 MIPS汇编中的第一个程序

本章介绍MIPS汇编中的Hello World程序，主要是理解寄存器和I/O机制。

## 1.1 MARS IDE

在这个网站下载即可：http://courses.missouristate.edu/kenvollmar/mars/index.htm，需要安装JAVA环境。

## 1.2 MIPS和内存

汇编语言将多种内存显式区分开来，并强迫编程的人使用它们，包括堆、静态数据、文本、堆栈和寄存器。

### 1.2.1 内存的种类

MIPS中的内存主要分成两大类，第一类是在CPU里的内存，通常称为寄存器；第二类是通常意义下的内存，是用来放置指令和数据的地方。

非寄存器部分的内存可以分成很多类，在这里我们仅研究文本、静态数据、堆和栈。

在这里，我们介绍的内存是一种比较老旧的模型，单线程且无虚拟机。

### 1.2.2 MIPS CPU

MIPS CPU包含32个寄存器，每个寄存器可以存储32bit的值。将CPU的基本目的看作对两个来自寄存器的值进行ALU运算并将结果存储到第三个寄存器是合理的。

需要通过lw和sw操作将地址和数据从内存中传入CPU，CPU才可以使用这部分数据。

除此之外，CPU还可以从外设中读取数据和地址。

### 1.2.3 寄存器

一共有32个寄存器，被分成了11类，每一类都有不同的功能，可以供编程者使用的、常用的寄存器包括：

- \$v0-\$v1：用于保存子程序的返回值，\$v0还用于syscall
- \$a0-\$a3：将参数传递给子程序
- \$t0-\$t9：存储临时变量，调用子程序时，临时变量的值会发生变化
- \$s0-\$s8：存储保存的值，在子程序调用中保持不变
- \$gp：指向全局内存的指针，与堆分配一起使用
- \$sp：堆栈指针
- \$fp：帧指针
- \$ra：返回地址，指向从子程序返回时使用的地址

### 1.2.4 MIPS内存的种类

MIPS的内存是32bit的平坦的内存，这意味着MIPS上的内存是连续的，从地址0x00000000到0xffffffff。

32bit的内存表明程序可以在4GB的数据中进行寻址，但编程者并不能使用所有的内存，有一些内存被操作系统使用，有一些被I/O子系统使用。但整个4GB的内存都是可以寻址的。

MIPS使用的内存类型：

<img src="image-20220504135835183.png" alt="image-20220504135835183" style="zoom: 67%;" />

## 1.3 MIPS汇编的第一个程序

第一个程序的代码：

```assembly
# 程序文件：Program2-1.asm
# 作者：Charles Kann
# 用途：第一个程序，Hello World
.text	# 定义程序指令
main:	# 主程序
	li $v0, 4	# 将4加载到$v0中，打印字符串
	la $a0, greeting	# 将greeting的地址加载到$a0中
	syscall	# 打印greeting
	
	li $v0, 10	# 将10（停止）加载到$v0
	syscall	# 程序结束
.data
greeting: .asciiz "Hello World"	# 要打印的字符串
```

### 1.3.1 细节

介绍Program2-1的一些细节。

等一会再介绍。

## 1.4 给出提示并从用户那里读取整数的程序

这个程序是，提示用户输入一个整数，然后将这个整数读入寄存器，并且打印回给用户。

```assembly
# 程序文件：Program2-2.asm
# 作者：Charles Kann
# 用途：提示用户输入一个整数，然后将这个整数读入寄存器，并且打印回给用户。
.text
main:
	# 提示输入整数
	li $v0, 4
	la $a0, prompt
	syscall
	
	# 读取整数并保存在$s0
	li $v0, 5
	syscall
	move $s0, $v0
	
	# 输出文字
	li $v0, 4
	la $a0, output
	syscall
	
	# 输出数字
	li $v0, 1
	move $a0, $s0
	syscall
	
	# 退出程序
	li $v0, 10
	syscall
	
.data
prompt: .asciiz "Please enter an integer: "
output: .asciiz "\n You typed the number: "
```

### 1.4.1 细节

介绍Program2-2的一些细节。

等一会再介绍。

## 1.5 给出提示并从用户那里读取字符串的程序

和上一个程序看起来很相似，但在概念上有很大的差别。

```assembly
# 程序文件：Program2-3.asm
# 作者：Charles Kann
# 用途：提示用户输入一个字符串，然后将这个字符串打印回给用户。
.text
main:
	# 提示输入一个字符串
	li $v0, 4
	la $a0, prompt
	syscall
	
	# 读取字符串
	li $v0, 8
	la $a0, input
	lw $a1, inputSize
	syscall
	
	# 输出文本
	li $v0, 4
	la $a0, output
	syscall
	
	# 输出字符串
	li $v0, 4
	la $a0, input
	syscall
	
	# 退出程序
	li $v0, 10
	syscall

.data
input: .space 81
inputSize: .word 80
prompt: .asciiz "Please enter an string: "
output: .asciiz "\n You typed the string: "
```

### 1.5.1 细节

介绍Program2-3的一些细节。

等一会再介绍。

## 1.6 总结

这一章主要是介绍如何在MARS中创建和运行带有I/O的简单汇编程序，涵盖了以下内容：

- MIPS程序的注释
- MIPS计算机中的寄存器和内存
- 汇编程序指令：`.text`, `.data`, `.asciiz`, `.space`, `.word`
- MIPS汇编程序中的labels
- MIPS汇编操作符：`li`, `la`, `lw`, `move`
- 不同的`syscall`，特别是1, 4, 5, 8
- 引用和数据值之间的差异

# 2 MIPS算术和逻辑运算符

## 2.1 3-地址机

MIPS的大多数操作允许指定三个寄存器作为指令的一部分，所以MIPS CPU的组织架构被称为“3-地址机”。

