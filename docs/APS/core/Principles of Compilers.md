---
title: APS 重点复习课程之编译原理
author: tinsir888
date: 2022/10/26
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 编译原理
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: cad1b837
---

# Overview

Definition

Difference between compiler & interpreter

Front-end of Compiler

- Lexical Analyzer

  - Read src l2r and produces a sequence of tokens. check the error of lexical and register on the symbol table.
  - Token consists of work-type-code and word-value
  - formal language

- Parser

  check if the sequence of tokens follow the grammatical rules, group the tokens into grammatical units and construct the Parse Tree, by middle-order traverse the Parse Tree we will get the original input string.

- Semantic Analyzer

  phase checks the program for semantic errors(the type, out of range) and gathers information(the allocation of variable, calculate the value of an expression) for the successive stages.

- Intermediate code Generator

Back-end of Compiler

- Code optimizer
- Code Generator

# Definition

> A compiler is a special program that transforms a higher-level program language into a lower-level language(assembly language). A good reason for transform is to get an executable program.

## Compiler Structure

**front end**: lexical analyzer - parser - semantic analysis & intermediate code gen

**back end**: optimizer - object code generator.

every stage may interact with Error handling.

# Compiler V.S. Interpreter

> A compiler translates the higher level programming language into the lower level programming language. This step is called compiling, the process consist of 4 steps (pre-processing, compile, assemble, linking). this is translation from the code face to the developer to the machine.
>
> An interpreter translates a line of program immediately once the interpreter reads it, it will turning the program instructions, or code, directly into actions, not producing the object file. so that's slower, and can't share the object code.

# Front-end

## Lexical Analyzer

LA reads the program from left-to-right and produces a sequence of tokens. Check the error of lexical and register on the symbol table.

token consists of word-type-code and word-value

### Formal Language

Grammar G=(V,T,P,S) variable, terminal, production, start symbol

e.g. $\{a^nb^n|n\geq1\}$, G=$\{\{S\},\{a,b\},\{S\rightarrow ab|aSb\},S\}$

Regular Expression

### Finite Automata

Finite automata

Deterministic Finite-state Automata DFA

Non-deterministic Finite-state Automata NFA

Language to NFA

#### NFA to DFA

z.B. we have a NFA as follow:

{% mermaid %}
graph LR
0((S))
1((q0))
2((q1))
3((q2))
4(q3)
0-->1
1-->|ε|2
1-->|ε|3
3-->|ε|2
2-->|b|3
2-->|a|2
3-->|b|4
2-->|ε|4
{% endmermaid %}

find ε-closure then merged.

ε-closure(q0)=\{q0,q1,q2,q3\}=A

ε-closure(q1)=\{q1,q3\}=B

ε-closure(q2)=\{q1,q2,q3\}=C

ε-closure(move(A,a))=\{q1,q3\}=B

ε-closure(move(A,b))=\{q1,q2,q3\}=C

ε-closure(move(B,a))=B

ε-closure(move(B,b))=C

ε-closure(move(C,a))=B

ε-closure(move(C,b))=C

Thus, the DFA is:

{% mermaid %}
graph LR
0((S))
1(A)
2(B)
3(C)
0-->1
1-->|a|2
1-->|b|3
2-->|a|2
2-->|b|3
3-->|a|2
3-->|b|3
{% endmermaid %}



#### DFA Minimize

> move(\{A,B,C\},a)=\{B\}
>
> move({A,B,C},b)={C}
>
> so \{A,B,C\} can merged in a state
>
{% mermaid %}
graph LR
0((S))
1(ABC)
0-->1
1-->|a|1
1-->|b|1
{% endmermaid %}

## Parser

Tokens are grouped into grammatical phrases represented by a Parse Tree which gives a hierarchical structure to the source program. The hierarchical structure is expressed by recursive rules, called Productions(产生式).

Group the tokens into grammatical phrases construct the Parse Tree, by in-order traverse the Parse Tree we will get the original input string.

Problems from up to bottom meet: Ambiguity 歧义

- leftmost derivation 最左推导

### First Set

first set contains all first token which may occur at the first.

for non-terminal tokens!

z.B. Given a set of CFG:

> E→TE'
>
> E'→+TE'|ε
>
> T→FT'
>
> T‘→*FT'|ε
>
> F→(E)|id

The first set are:

> first(F)=\{ ( , id \}
>
> first(T)=first(F)=\{ ( , id \}
>
> first(E)=first(T)=\{ ( , id \}
>
> first(E')=\{ + , ε \}
>
> first(T')=\{ * , ε \}
>
> first( ( )=\{ ( \} not necessary, ( is a terminal token.
>
> first( ) )=\{ ) \} not necessary, ) is a terminal token.
>
> first(id)=\{ id \} not necessary, id is a terminal token.

### Follow Set

for non-terminal tokens

follow(A) contains:

> for any CFG B→αAβ, follow(A) contains first(β)\\{ε}
>
> for any CFG B→αA, follow(A) contains follow(B)
>
> if A is a start token, follow (A) contains #.

z.B. (last example)

follow set for non-terminal tokens are:

> follow(E)=\{ ) , # \}
>
> follow(E')=\{ ) , # \} 读作 E apostrophe
>
> follow(T)=\{ + , ) , # \}
>
> follow(T')=\{ + , ) , # \}
>
> follow(F)=\{ * , + , ) , # \}

### LL(1) Grammar

Build Parse Tree **from top to bottom**.

For any token A in G, A→α\_1|α\_2|...|α\_n is all productions of A, if:

- first(α_i)∩first(a\_j)=NULL (i≠j)
- when ε∈first(a\_j), follow(A)∩first(a\_i)=NULL

Then G is LL(1) grammar.

- The first L means scanning input string from Left to right.
- The second  L means leftmost derivation

### LR Analysis

**from bottom to top**

ACTION: state i, terminal token a

- move in j: j is a state, push j into stack.
- reduce A→β, replace stack top β with A.
- accept: accept input, finish analysis.
- error: find syntax error in input.

GOTO: if GOTO(I\_i, A)=I\_j, then GOTO(i, A)=j.

#### Basic structure of LR Analyzer

LR main controller connect to:

1. input buffer
2. stack: state/token stack
3. action list, goto list
4. production sequence.

## Semantic Analyzer

Phase checks the program for semantic errors(the type, out of range) and gathers information(the allocation of variable, calculate the value of an expression) for the successive stages.

## Intermediate Code Generator

This stage transforms parse tree that pre-processed by the semantic analyze into an intermediate-language representation of the source program.

The intermediate code is more simple and it's easier to transfer and optimize the three address code:

`id1+id2*id3` is translated to 3-addr code:

`T1=id2*id3`

`T2=id1+T1`

# Back-end

## Code Optimizer

Attempts to improve the intermediate code by equivalent transferring the intermediate code, seeking for better operating effective and save storage space(in the loop, move the unchanged value in the loop out of loop, use the faster operation to replace the slower operation). Different compilers adopt different optimization techniques.

## Code Generator

Generates the target code which is consisted of assembly code with the absolute address.