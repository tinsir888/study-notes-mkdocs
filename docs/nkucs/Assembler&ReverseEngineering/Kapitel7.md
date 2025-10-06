---
title: 汇编语言与逆向技术基础 七、区块表、输入表、输出表
author: tinsir888
date: 2021/11/9
cover: /img/assembler.png
tags:
  - 笔记
  - 汇编
  - 逆向
categories:
  - 南开计科
  - 汇编语言与逆向技术基础课程笔记
abbrlink: 17386aae
---
# 汇编语言与逆向技术基础 七、区块表、输入表、输出表

## 本章知识点

1. 区块表

2. 输入表

3. 输出表

## 区块表

保证程序的安全性

- 把 code 和 data 放在同一个内存区块中相互纠缠，很容易引发 安全问题

- code 有可能被 data 覆盖，导致崩溃

- PE 文件格式将内存属性相同的数据统一保存在一个被称为“区块”（Section ）的地方

### 内容

Name 8 BYTE ：块名

VirtualSize DWORD：在 内存空间 中，区块的大小

VirtualAddress DWORD：区块在 内存空间 中的起始 RVA

SizeOfRawData DWORD：该区块在 硬盘 中所占的空间

PointerToRawData DWORD：该区块在 硬盘 中的偏移

PointerToReLocations DWORD: 在 EXE 文件中无意义

PointerToLinenumbers DWORD: 行号表在文件中的偏移量

NumberOfReLocations WORD: 在 EXE 文件中无意义

NumberOfLinenumbers WORD: 该块在行号表中的行号数目

### 内存属性

块属性

- IMAGE_SCN_MEM_EXECUTE

  20000000h 可执行

- IMAGE_SCN_MEM_READ

  40000000h 可读

- IMAGE_SCN_MEM_WRITE

  80000000h 可写

- IMAGE_SCN_CNT_CODE

  00000020h ，包含 可执行代码

- IMAGE_SCN_CNT_INITIALIZED_DATA

  00000040h ，包含 已初始化数据

- IMAGE_SCN_CNT_UNINITIALIZED_DATA

  00000080h ，包含 未初始化数据

### 常见的区块

.text 代码区块，链接器把所有目标文件的 .text 区块连接成一个大的.text 区块

.data，读、写数据区块，全局标量

.rdata，只读数据区块，调试目录、 字符串 等

.idata，输入表

.edata ，输出表

.rsrc ，资源数据 菜单、图标、位图 等

.bss ，未初始化数据，被 .data 取代，增加 VirtualSize 到足够放下未初始化数据

### 区块的对齐

硬盘上的对齐

- FileAlignment
- 200h，扇区对齐，区块间隙

内存上的对齐

- SectionAlignment
- 1000h，内存页对齐（64位系统，8KB 内存页）

### 文件偏移与虚拟内存地址转换

Image （映像）

PE 文件加载到内存时，不会原封不动地加载

- 根据区块表的定义加载

PE 文件与内存中的 Image 具有 不同的形态

文件被映射到内存中时， MS DOS 头部、 PE 文件头 和 区块表 的偏移位置与大小均 没有变化

各 区块 被映射到内存中后，其偏移位置就发生了 变化

RVA to RAW

- RAW - PointerToRawData = RVA - VirtualAddress
- RAW = RVA - VirtualAddress + PointerToRawData

### RVA to RAW

RVA=2123h 在 .rdata 区块

- .rdata 区块的相对虚拟地址 RVA 范围是 2000h 到 3000h

VirtualAddress = 2000h

PointerToRawData = 600h

RAW=2123h (RVA) - 2000h (VirtualAddress)+600h(PointerToRawData) = 723 h

RVA 与 RAW （文件偏移）间的相互变换时 PE 头的最基本内容，需要熟悉并掌握。

## 输入表 IAT

输入表 Import Address Table IAT

IAT 中的内容与 Windows 操作系统的核心进程、内存、 DLL 结构等有关

- “理解了 IAT ，就掌握了 Windows 操作系统的根基”

IAT 是一种表格结构

标记程序需要使用哪些 库 中的哪些函数

### IMAGE_IMPORT_DESCRIPTOR
IMAGE_IMPORT_DESCRIPTOR 结构体中记录 PE 文件要导入哪些 库文件

PE 程序往往需要导入 多个库

导入多少个库，就存在多少个 IMAGE_IMPORT_DESCRIPTOR 结构体

多个结构体组成 数组 ，以 NULL 结构体结束

#### 内容

==OriginalFirstThunk DWORD INT 的地址 RVA==

==Name DWORD，库文件名字符串的地址 RVA==

==FirstThunk DWORD IAT 的地址 RVA==

Table：在 PE 头中， Table 即指 数组

INT 与 IAT 是 DWORD 数组，以 NULL 结束

INT 与 IAT 的各元素指向相同地址

## PE 装载器

- 读取 IID 的 name 成员，获取库名称字符串，例如“ kernel32.dll“
- 装载相应的库，类似 LoadLibrary （kernel32.dll）
- 读取 IID 的 OriginalFirstThunk 成员，获取 INT 地址
- 读取 INT，逐一获得 IMAGE_IMPORT_BY_NAME 的地址 RVA
- 使用 IMAGE_IMPORT_BY_NAME 的 Hint 或者 Name 项，获得函数的起始地址
  - 类似 GetProcAddress ExitProcess
- 读取 IID 的 FirstThunk 成员，获得 IAT 地址
- 将第 5 步获得的函数地址写入 IAT 数组相应位置
- 重复步骤 4 到 7 ，直到 INT 结束

INT是给Windows看的，IAT是程序自己用的。

## 输出表 FAT

### DLL

Windows 操作系统提供了数量庞大的 库函数

- 进程、内存、窗口、消息、文件、网络等

同时运行多个程序时，每个进程都包含相同的库，严重浪费内存

Dynamic Link Libary (DLL)，内存映射

### IMAGE_EXPORT_DIRECTORY

EAT 是 DLL 的核心机制

- 不同程序可以调用库文件中提供的函数
- 通过 EAT ，得到库文件导出 函数的入口地址

#### 内容

Name：库文件名字符串==地址==

NumberOfFunctions：实际 Export 函数的个数

NumberOfNames：Export 函数中具有名字的函数个数

AddressOfFunctions：Export 函数地址数组

AddressOfNames：函数名称地址数组

AddressOfNameOrdinals：Ordinal 地址数组

### GetProcAddress 操作原理
从库中获得函数地址的 API 为 GetProcAddress 函数

- 如何通过 EAT 获得函数地址？
  1. AddressOfNames 定位“函数名称数组”
  2. 在“ 函数名称数组 ”中，通过比较字符串 strcmp ），查找指定的函数名称，此时的数组索引称为 name_index
  3. 利用 AddressOfNameOridinals 成员，定位 ordinal 数组
  4. 在 ordinal 数组 中，通过 name_index 查找相应的 ordinal 值
  5. AddressOfFunctions ，定位 函数地址数组 EAT。在“函数地址数组”中，利用 ordinal 值作为索引，获得指定函数的起始地址

### EAT

如果函数是以 序号 导出的，那么查找的时候直接用序号减去 Base 得到的值就是函数在AddressOfFunctions 中的下标