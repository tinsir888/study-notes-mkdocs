---
title: 汇编语言与逆向技术基础 五、过程
author: tinsir888
date: 2021/10/26
cover: /img/assembler.png
tags:
  - 笔记
  - 汇编
categories:
  - 南开计科
  - 汇编语言与逆向技术基础课程笔记
abbrlink: d4f83506
---
# 汇编语言与逆向技术基础 五、过程

## 本章知识点

- OllyDbg简介
- JMP和LOOP指令
- 过程的定义和使用
- 调用链接库中的函数

## OllyDbg简介

32 位 Windows 调试器（Debugger）

- 反汇编，将 CPU 指令翻译为汇编语句
- 动态追踪汇编语句的执行
- Ring3 级
- 官网地址， http://www.ollydbg.de/

## JMP和LOOP指令

### 控制转移
- 控制转移（ transfer of control ）是一种改变汇编语句执行顺序的方法。
  - 无条件转移
  - 条件转移

### 无条件转移

将 CPU 控制权直接转移到指定的汇编语句

- 修改 EIP 为指定的内存地址
- CPU 从 EIP 指定的内存地址读取下一条机器指令

### JMP指令

JMP 目的地址

JMP 指令实现 CPU 控制权的无条件跳转

目的地址是代码标号

- 代码标号被 汇编器 翻译成内存地址
- CPU 看到的是内存地址，不是代码标号

### CMP指令
CMP 指令，比较目的操作数和源操作数

- CMP reg reg
- CMP reg imm
- CMP mem reg
- CMP mem imm
- CMP reg mem

不能mem和mem比较！

- 执行从源操作数中 减 掉目的操作数的减法操作

- 设置相应的标志位， 不改变操作数
- 标志位： OF 、 SF 、 ZF 、 AF 、 PF 、 CF

### 条件跳转指令 

JA JB JE JNE……

### LOOP指令
LOOP 目的地址

LOOP 指令可以指定循环执行的次数

- ECX 寄存器作为循环计数器
- LOOP 指令执行时， ECX 减 1
- 如果 ECX 不等于 0 ，跳转到目的地址
- 如果 ECX 等于 0 ，不跳转，顺序执行

## 过程的定义和使用

### 过程

- C++ 中的 函数定义

- 返回类型 一个函数可以返回一个值。 return_type 是函数返回值的数据类型。

- 函数名称 这是函数的实际名称。函数名和参数列表一起构成了函数签名 。

- 参数 参数就像是占位符。当函数被调用时，向参数传递一个值，这个值被称为 实际参数 。参数列表包括函数参数的类型、顺序、数量。

- 函数主体 函数主体包含一组定义函数执行任务的语句。



- 汇编语言中，一般使用术语“ 过程 procedure。表示高级语言中的函数、方法
  - 函数 function C/C++ 中称为函数
  - 方法 method JAVA 中称为方法
- 汇编语言 早于 “面向对象”、“面向函数”的编程语言
- 汇编语言把过程定义为 以返回语句结束 命名的语句块 。
  - 使用 PROC 和 ENDP 伪指令来声明过程
  - 必须定义一个 过程名字 （标识符）
- 除启动过程之外，其它过程以 ret 指令结束
  - 将 CPU 控制权转移到过程被调用的地方
- 启动过程（ main ）的 返回语句 是
  - INVOKE ExitProcess , 0
  - 将 CPU 的控制权转移给 Windows 操作系统

### 过程的定义

- MyProc 过程，计算寄存器 EAX 、 EBX 、 ECX 之和

```assembly
MyProc PROC
	ADD EAX, EBX
	ADD EAX, ECX
	RET
MyProc ENDP
```

### 过程的调用和返回

- CALL 指令将 CPU 的控制权转移到新的内存地址执行指令， 实现过程的调用

- RET 指令将 CPU 的控制权返回到程序中过程被调用的地方继续执行

- 过程返回地址的保存
  - CALL 指令调用之后，将过程的 返回地址 压入堆栈，将过程入口地址赋值给 EIP ，实现 CPU 控制权的转移
  - RET 指令调用之后，将过程的 返回地址 赋值给 EIP 寄存器，实现 CPU 控制权的转移

## 调用链接库中的函数

### 链接库

- 链接库（ Link Library ）是一个文件，包含已经编译成机器码的过程。
  - includelib \masm32\lib\masm32.lib
  - includelib \masm32\lib\kernel32.lib

### PROTO伪指令
PROTO 伪指令用于声明链接库中的过程

- include \masm32\include\masm32.inc
- include \masm32\include\kernel32.inc

- StdOut

  StdOut PROTO :DWORD

- StdIn

  StdIn PROTO :DWORD,:DWORD

- ExitProcess

  ExitProcess PROTO STDCALL :DWORD

