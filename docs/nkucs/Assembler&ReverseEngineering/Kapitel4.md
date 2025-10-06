---
title: 汇编语言与逆向技术基础 四、数据传送与寻址
author: tinsir888
date: 2021/10/19
cover: /img/assembler.png
tags:
  - 笔记
  - 汇编
categories:
  - 南开计科
  - 汇编语言与逆向技术基础课程笔记
abbrlink: e60ba705
---
# 汇编语言与逆向技术基础 四、数据传送与寻址


## 本章知识点

- 数据传送指令
- 加法和减法
- 数据相关的操作符和伪指令
- 间接寻址

## 数据传送指令

### 汇编语言与高级语言

- 汇编语言与高级语言最根本的不同之处在于，程序员必须掌握 内存中的数据存储 和 机器与系统相关的大量细节
- 汇编语言给了程序员极大的 自由 ，可以直接与机器对话，不需要依靠各种“翻译人员”

### 指令

- 一条汇编语句
  - 标号（ identifier）
  - 指令助记符（ 操作码 opcode）
  - 操作数（oprand）
  - 注释（comment）

### 操作数类型

- 立即数（ immediate）

  mov eax, 10h

- 寄存器（register）

  inc eax

- 内存（memory）

  mov eax, \[ebp+8\]

### 寄存器操作数的简写符号

r8 8位通用寄存器

r16 16位通用寄存器

r32 32位通用寄存器

reg 任意的通用寄存器

sreg 16 位段寄存器

### 立即数操作数的简写符号
imm 8 位、 16 位或 32 位立即数
imm8 8 位立即数
imm16 16 位立即数
imm32 32 位立即数

### 内存操作数的简写符号

r/m8 8 位通用寄存器或内存操作数
r/m16 16 位通用寄存器或内存操作数
r/m32 32 位通用寄存器或内存操作数
mem 8 位、 16 位、 32 位内存操作数

### MOV 指令

- mov 指令从 源操作数 向 目的操作数 复制数据

  mov destination source
  C++ 中， destination = source

- ==两个操作数的尺寸必须一致==

- **两个操作数不能同时为内存操作数**

- 目的操作数不能是 CS 、 EIP 和 IP

- 立即数不能直接送至段寄存器

- mov Move (Opcodes: 88, 89, 8A, 8B, 8C, 8E, ...)

- 语法
  mov \<reg\>,\<reg\>
  mov \<reg\>,\<mem\>
  mov \<mem\>,\<reg\>
  mov \<reg\>,\<imm\>
  mov \<mem\>,\<imm\>

例子

- mov byte ptr [var], 5

### 直接内存操作数

.data
var1 DWORD 1000h；内存位置偏移 00403000h
.code
mov EAX, var1；机器指令 A100304000

- 变量名（数据标号）

  数据段内偏移地址

### 内存寻址操作

- masm32 使用 方括号 表示内存寻址操作

  mov eax , [var1]

- 通常，直接内存操作数不使用中括号

  mov eax , var1

- 涉及到算术表达式时，使用中括号

  mov eax , [var1+5]

### 整数的零扩展

- 复制尺寸较小的操作数到尺寸较大的操作数
- MOVZX 指令 (move with zero extend)
  - movzx r32, r/m8
  - movzx r32, r/m16
  - movzx r16, r/m8

- MOVSX（move with sign extend ）符号扩展传送指令 **最高位循环填充所有扩展位**
- 有符号整数的存储空间扩展
  - movsx r32 r/m8
  - movsx r32 r/m16
  - movsx r16, r/m8

### LAHF 指令

LAHF load status flags into AH ）指令把 EFLAGS 寄存器的低字节
复制到 AH 寄存器
符号标志（SF）
零标志 （ZF）
辅助进位标志 （AF）
奇偶标志 （PF）
进位标志 （CF）

### SAHF指令

SAHF store AH into status flags ）指令复制 AH 寄
存器的值至 EFLAGS 寄存器的低字节
修改 CPU 的符号标志（ SF ）、零标志 (ZF） 、辅助进位
标志 (AF） 、奇偶标志 (PF） 、进位标志 (CF）

### XCHG指令

- XCHG （exchange data ）指令交换两个操作数的内容

  - XCHG reg, reg

  - XCHG reg, mem

  - XCHG mem, reg

### 直接偏移操作数

- 在变量名后面加上一个偏移值，可以创建直接偏移（ direct offset
  操作数
- 访问没有显式标号的内存

## 加法和减法

### INC指令

- INC（ increment ）指令从操作数中加 1
- 语法
  - inc \<reg\>
  - inc \<mem\>

- 例子
  - inc eax
  - inc [var1+4]

### DEC指令

- DEC （decrement ）指令从操作数中减 1
- 语法
  dec\<reg\>
  dec\<mem\>
- 例子
  inc eax
  inc [var1+4]

### ADD指令

- ADD 指令将同尺寸的 源操作数 和 目的操作数 相加
  add \<reg\>,\<reg\>
  add \<reg\>,\<mem\>
  add \<mem\>,\<reg\>
  add \<reg\>,\<imm\>
  add \<mem\>,\<imm\>

- 相加的 结果 存储在目的操作数中

  - ADD 目的操作数，源操作数

  - 影响标志位 CF 、 ZF 、 SF 、 OF 、 AF 、 PF

### SUB指令

- SUB 指令将源操作数从目的操作数中减掉
  sub \<reg\>,\<reg\>
  sub \<reg\>,\<mem\>
  sub \<mem\>,\<reg\>
  sub \<reg\>,\<imm\>
  sub \<mem\>,\<imm\>
- SUB 目的操作数， 源操作数
- 影响的标志位有 CF 、 ZF 、 SF 、 OF 、 AF 、 PF

### NEG指令

- NEG negate ）指令通过将数字转换为对应的补码而求得其相反
  数
  neg \<reg\>
  neg \<mem\>
- 影响的标志位： CF 、 ZF 、 SF 、 OF 、 AF 、 PF

## 数据相关的操作符和伪指令

OFFSET 操作符
ALIGN 伪指令
PTR 操作符
TYPE 操作符
LENGTHOF 操作符
SIZEOF 操作符
LABEL 伪指令

### OFFSET操作符

- OFFSET 操作符返回数据标号的 偏移地址
- 偏移地址表示标号距离 数据段 开始的距离
  - CS 的值一般是 0
  - CS 为零的时候， OFFSET 等同内存虚拟地址

### ALIGN伪指令

- ALIGN 指令将变量的位置按 BYTE 、 WORD 、 DWORD 边界对齐

  - **可以对代码段指令对齐，填充的是NOP**

  - ALIGN 边界值
  - 边界值可以是 1 、 2 、 4 、 8 或 16 a power of 2
  - Aligned data can improve performance , at the expense of wasted space
    between data elements.

### PTR操作符

- PTR 操作符可以 重载 操作数声明的默认尺寸

### TYPE操作符

- TYPE 操作符返回变量的字节数

### LENGTHOF操作符

- LENGTHOF 操作符计算数组中元素的数目，元素由出现在同一行的值定义

### SIZEOF操作符

- SIZEOF 操作符的返回值等于 LENGTHOF 和 TYPE 返回值的乘积

### LABEL伪指令

- LABEL 伪指令允许插入一个标号，并赋予其尺寸属性而 无须分配
  任何实际的存储空间 。
- 为数据段内其后定义的变量提供一个 别名

```ass
.data
	dw_var LABEL DWORD
	var1 WORD 1234h
	var2 WORD 5678h
.code
	mov eax, dw_var

;两个 word 存储中为 34 12 78 56
;合成一个 dword 就是 56 78 12 34
;eax=56781234h
```

## 间接寻址

### 间接寻址

- 用 寄存器 作为指针并控制该寄存器的值称为间接寻址（indirect addressing）
  如果一个操作数使用的是间接寻址，就称之为间接操作数（indirect operand ）。

### 间接操作数

- 任何一个 32 位通用寄存器 EAX 、 EBX 、 ECX 、 EDX 、 ESI 、
  EDI 、 EBP 和 ESP ）加上 方括号 就能构成一个间接操作数

### 变址操作数

- 变址操作数（ indexed operand ）把常量和寄存器相加以
  得到一个有效地址
- 任何 32 位通用寄存器都可以作为变址寄存器
  constant [reg]
  [constant + reg]

### 指针

- 如果一个变量包含另一个变量的地址，则该变量称为指针
- 32 位模式下的 NEAR 指针和 FAR 指针
- NEAR 指针（课程使用 NEAR 指针）
  相对数据段开始的 32 位偏移地址
- FAR 指针
  48 位的段选择子 偏移地址

### TYPEDEF操作符

- TYPEDEF 操作符允许创建用户自定义的类型

- PBYTE TYPEDEF PTR BYTE ; 字节指针

- PWORD TYPEDEF PTR WORD ; 字指针

- PDWORD TYPEDEF PTR DWORD ; 双字指针