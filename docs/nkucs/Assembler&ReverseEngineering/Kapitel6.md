---
title: 汇编语言与逆向技术基础 六、PE 文件结构
author: tinsir888
date: 2021/11/2
cover: /img/assembler.png
tags:
  - 笔记
  - 汇编
  - 逆向
categories:
  - 南开计科
  - 汇编语言与逆向技术基础课程笔记
abbrlink: d3a20d3e
---
# 汇编语言与逆向技术基础 六、PE 文件结构

## 本章知识点

- 可执行文件
- PE 文件结构
- DOS 文件头
- PE 文件头



为什么Windows上的exe文件不能在Linux或MacOS上运行？

答：文件结构不一样



## 可执行文件

- 可执行文件 executable file
  - 由操作系统进行加载、执行的文件
  - 在不同的操作系统环境下，可执行文件的格式不一样
  - 二进制文件，不同于txt、doc、excel等文本文件

### Windows 系统可执行文件

在 Windows 操作系统下，可执行文件可以是 .exe 文件、.sys 文件、.dll 文件、.com文件等类型文件

### .com 文件

- 主要用于命令行应用程序，最大 65280 字节
- 与 MS-DOS 操作系统的可执行文件兼容

### .exe .dll .sys 可执行文件

- 使用 PE 文件结构
- PE 可移植可执行文件结构

- 逆向技术的基础

## PE 文件结构

- PE 文件使用的是一个平面地址空间
  - 所有的代码和数据合并在一起，组成一个很大的结构
- 文件内容被分割位不同的节（Section）

### 节

- 代码节.code、数据节.data
- 各个节按页边界对齐
- 节是一个连续结构，没有大小限制
- 每个节都有自己的内存属性

### 模块

- 当 PE 文件通过 Windows 加载器载入内存后，内存中的版本被称为块（Module）
  - 映射文件的起始地址称为模块句柄（hModule）
  - 初始内存地址也成为基地址（ImageModule）

- 内存的模块代表进程将这个可执行文件所需要的代码、数据资源、输入表、输出表及其它有用的数据结构使用的内存都放在一个连续的内存节中

### 模块句柄

- Windows 将 Module 的基地址作为 Module 的实例句柄 Hinstance

  GetModuleHandle 获得 dll 句柄，通过句柄访问 DLL Module 内容

### 虚拟地址

- 每个程序都有自己的虚拟内存地址空间，虚拟空间的内存地址称为虚拟地址 VA
  - 4GB
  - 不同进程的虚拟地址空间是相互隔离的

### 相对虚拟地址

- 在可执行文件中，有许多地方需要指定内存中的地址
  - 例如引用全局变量时需要指定其地址
- PE 文件有一个首选的载入地址（基地址），但是它们可以载入进程空间的任何地方，所以不能依赖 PE 的载入点
- 为了避免绝对内存地址，引入 RVA 相对虚拟地址的概念
  - RVA 是相对于 PE 文件载入地址的偏移位置
- 将 RVA 转换成 VA 的过程，即用实际的载入地址 ImageBase 加相对虚拟地址 RVA，得到虚拟内存地址 VA
  - VA = ImageBase + RVA

### 文件偏移地址

- PE 文件储存在磁盘中，某个数据的位置相对于文件头的偏移量称为文件偏移地址 File Offset 或物理地址 RAW Offset
  - 文件偏移地址从 PE 文件的第一个字节开始计数，起始值为 0

## DOS 头

每个 PE 文件都是以一个 DOS 程序开始的

DOS MZ 头与 DOS stub 合称为 DOS 文件头

## PE 文件头

PE 文件头 PE Header 紧跟在 DOS stub 的后面

IMAGE_DOS_HEADER 结构的 e_lfanew 字段定位 PEHeader 的起始偏移量，加上基址，得到 PE 文件头的指针

- PNTHeader=ImageBase+DosHeader->e_lfanew

### 内容

- Signature
  - 在一个有效的 PE 文件， Signature 字段的值是 00004550h
  - ASCII 字符是“ PE”
- FileHeader
  - IMAGE_FILE_HEADER 结构中记录了 PE 文件的一些基本信息，并指出了 IMAGE_OPTIONAL_HEADER 的大小
- Machine
  - 可执行文件的目标 CPU 类型
  - PE 文件可以在多种 CPU 机器上使用，不同平台上指令的机器码不同。
- NumberOfSections
  - NumberOfSections 记录节的数量，节表紧跟在 IMAGE_NT_HEADERS 后面定义
- TimeDateStamp
  - 文件的创建时间
  - 1970 年 1 月 1 日到创建该文件的所有的秒数

### IMAGE _OPTIONAL_HEADER

可选映像头 (IMAGE_ OPTIONAL _ 结构中定义了更多的 PE文件数据

IMAGE_FILE_HEADER 和 IMAGE_OPTIONAL_HEADER 合在一起构成 PE 文件头

- Magic: 这是一个标记字，说明文件是 ROM 映像 0107h 还是普通可执行的映像 010Bh

- MajorLinkerVersion 链接程序的主版本号
- MinorLinkerVersion 链接程序的次版本号

- SizeOfCode ：代码段的大小
- SizeOflnitializedData : 已初始化数据块的大小
- SizeOfUninitializedData 未初始化数据块的大小
- AddressOfEntryPoint 程序执行的入口 RVA
- BaseOfCode 代码段的起始 RVA
- BaseOfData 数据段的起始 RVA
- ImageBase 文件在内存中的载入地址
- SectionAlignment (DWORD)：内存上区块间的对其大小
- FileAlignment (DWORD) 硬盘上区块间的对齐大小
- MinorOperatingSystemVersion (WORD)
  - PE 文件执行所需要的 Windows 系统最低版本号
  - Windows NT 的内部版本号是 4.0
  - Win2000 是 5.0 、 XP 是 5.1 、 Vista 是 6.0 、 Win7 是 6.1 、 Win8 是 6.2 、 Win10 是 10.0
- MinorImageVersion (WORD)
  - 程序的主版本号和次版本号
  - 由程序员自己定义
- MinorSubsystemVersion (WORD)
  - PE 文件所要求的子系统的版本号
- Win32VersionValue(DWORD): 通常被设置为 0
- SizeOfImage (DWORD): 映像载入内存后的总大小，是指载入文件从 ImageBase 到最后一个块的大小
- SizeOfHeaders (DWORD): MS DOS 头部、 PE 文件头、区块表的总尺寸
- SizeOfStackReserve DWORD
- SizeOfStackCommit DWORD
- SizeOfHeapReserve DWORD
- SizeOfHeapCommit DWORD
  - 栈、堆的设置信息
- LoaderFlags DWORD 与调试有关

### 数据目录

- NumberOfRvaAndSizes：数据目录的项数
- DataDirectory [16]: 数据目录表
- 定位 PE 文件的导入表、导出表、资源的时候，需要用到 数据目录
