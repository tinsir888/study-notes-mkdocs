---
title: APS 重点复习课程之汇编语言与逆向技术基础
author: tinsir888
date: 2022/10/24
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 汇编
  - 逆向
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 551befad
---

# Overview

Brief Intro

- Application of AL & RE

- Big/Little-endian

Basic info of IA32

- Architecture
- Work Mode
- Register

Assembly Instruction

- Data Define
- mov, add, cmp, ja, jb, jg, jl, in&out, push&pop
- jmp&loop

Addressing Mode

- Direct Addressing
- Register Direct Addressing
- Register Indirect Addressing
- Register Relative Addressing
- Base Index Addressing

Interrupt

- INTR
- INTP
- Interrupt Type Code
- Keep the break point
- Interrupt Vector Table

Block

- Memory attribute: readable, writeable and executable.

Static Reversing

- IDA Pro
- Function Call
  - Stack: ESP EBP
  - Stack Frame
- Calling Convention:warning:
  - \_\_cdecl
  - \_\_stdcall

Software Protection

- Sequence ID Protection
- Warning Windows
- Time Restriction
- Menu Function Restriction
- KeyFile

# Brief Introduction

## Application of AL & RE

Assembly language application

- Embedded system
- Real-time system
- Driver
- Videospiel Console

Reverse Engineering

- Debugging
- Vulnerability mining
- Computer Virus Analysis
- Software Intellectual Property Protection

## Big/Little-endian

Big-endian:

> Usually in network transmit. The high-byte store in low-byte
>
> z.B. 127.0.0.1 in memory: 7F 00 00 01

Little-endian:

> In x86 etc, The low-byte store in low-byte
>
> z.B. 127.0.0.1 in memory: 01 00 00 7F

# Basic info of IA32

## Architecture

- 32-bit memory address
- 32-bit oprand

## Work Mode

### Real-Address Mode

- 16 data lines
- 20 address lines

- Memorize space 1 MiB, is divided into 4 parts: CS DS SS(stack seg) ES(extra seg)

  physical address = segment address * 16 + logical address

Why we need segment?

> 1. Reduce the length of instructions and the execution time of instruction.
> 2. We have 20 address lines, which means we have 1 MiB storage space, but we have only 16 bits registers, so we use segment address * 16 + offset to access 20 bits physical address with 16 bits registers.

### Protected Mode

- 32 bit
- multi-task OS
- Memory space: 4 GiB

## Register

[more detail](https://tinsir888.github.io/posts/d1b092c1.html)

### GPR

aka general purpose register.

In IA-32, there are 8 gpr.

`EAX, EBX, ECX, EDX, ESP, EBP, ESI, EDI`

for `E[ABCD]X`,

- Right half of `E?X` is `?X`.
- Left half of `?X` is `?H`, right half is `?L`

Stack pointer register: `ESP`, right half `SP`

Basic-address pointer register: `EBP`, right half `BP`

Source Index register: `ESI`, right half `SI`

Destination index register: `EDI`, right half `DI`.

### Segment Register

CS code segment

DS data segment

SS stack segment

ES extra data segment

FS data segment (handle exception)

GS data segment

# Assembly Instruction

[more detail](https://tinsir888.github.io/posts/74c15c4f.html)

## Data Define

- BYTE db 8-bit
- WORD dw 16-bit
- DWORD dd 32-bit
- QWORD dq 64-bit

## Several Common Instructions

```assembly
MOV AX, BX
MOV [1000H], AL

PUSH AX
POP AX

IN AL
PORT IN AX
PORT

OUT PORT
AL OUT PORT AX

INC EBX
DEC EAX
ADD AX, BX
SUB AX, BX

CMP AX, BX
SHL/SHR reg/mem, 1/CL;logic left/right shift
SAL/SAR;arithmic left/right shift

JNE/JE/JA/JB

LOOP START
INT 21H
```

# Addressing Mode

- Direct Addressing

  `MOV, AX, 1234H`

- Register Direct Addressing

  `MOV AX,[2000H]`

  `if (DS) = 3000H (AX)=[32000H] EA=2000H`

- Register Indirect Addressing

  `MOV AX,[BX]`

- Register Relative Addressing

  `MOV AX,[label+BX/SI/DI]`

- Base Index Addressing

  `MOV AX,[BX+DI]`

# Interrupt

`INT N`, N is the interrupt type code.

How do CPU response to the interrupt?

1. receive a interrupt signal from the INTR pin
2. send a responding signal to the INTA pin
3. read the interrupt type code from the data bus and store it to a temporary register.
4. keep a breakpoint for the next instruction, store the address of next instruction to the stack including the address of code segment CS and current offset IP.
5. use the interrupt type code to find the starting address of the interrupt service program at the interrupt vector table.

# Block

- Memory attribute: readable, writeable and executable.

# Static Reversing

IDA Pro

## Function Call

- Stack: ESP(store address of stack top) EBP(store address of stack bottom)
- Stack Frame [more detail](https://tinsir888.github.io/posts/6eaeff74.html)

- Calling Convention:warning:

  - \_\_cdecl
    1. C/C++
    2. passing args from right to left
    3. caller function pop stack.
  - \_\_stdcall
    1. Win32 API
    2. passing args from left to right
    3. callee function pop stack.

  How to identify?

  > check whether ESP is changed after calling, if true, \_\_cdecl; else, \_\_stdcall.

# Software Protection

- Sequence ID Protection
- Warning Windows
- Time Restriction
- Menu Function Restriction
- KeyFile