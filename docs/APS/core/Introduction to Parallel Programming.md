---
title: APS 重点复习课程之并行程序设计
author: tinsir888
date: 2022/10/22
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 并行程序设计
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: d5a99cbd
---

# Overview

Brief Introduction

Parallel Algorithm Design and Analysis

SIMD

Pthread

OpenMP

MPI

# Brief Introduction

## Why Parallel

Development of frequency: slow down, due to restriction of power and cooling.

All computers are parallel computers.

## Application of Parallel Computing

Scientific simulation: climate modeling, Parallel Ocean Program, Biology informatics, medicine...

## Super Computer Hardware

Parallel architecture

von Neumann architecture

- temporal locality&spatial locality
- Lower memory access latency
- pipeline, multiple launch, SIMD

parallel control mechanism

- SIMD
- MIMD
- SPMD

parallel architecture organization

- shard memory
- distributed memory

## Software Technology

sufficient concurrency (law of Amdahl)

concurrency granularity 并发力度

locality 局部性

load balancing 负载均衡

Coordination and Synchronization

## Multi-core/GPU

Tianhe-1A Tianhe-2

Nvidia 皮衣黄，黄狗

GPU vs CPU: GPU is throughput-orient, while CPU is latency-orient.

AMD Ryzen Zen3

ARM v8

# Parallel Algorithm

## Design

### Task Decomposition, Data Dependence, Compete Condition

Design

- Already know serial version, then implement a parallel version
  - Not sure to be best strategy: in some cases, best serial algorithm has nothing to do with best parallel algorithm
- Parallel algorithm strongly close to computer architecture!

how to design?

1. decompose computing task
2. keep dependence
3. try best to minimize extra cost

If output rely on multi-incident's sequence of time, then it exists race condition（竞争条件）.

Data dependence means in order to ensure the output correct, keep sequence of 2 storage operate.

Synchronization: serialize multi thread.

atomicity: a set of operations is either done all or not execute.

mutual exclusion: in any time only a thread running.

barrier: stop thread continuing until all thread has done.

## Analysis

### Basic Indicators

How to judge a serial algorithm: time/space complexity.

How to judge a parallel algorithm: number of processors, relative computing speed, communication speed.

#### Extra cost

- communication between processes
- process idle（进程空闲）
- extra computing

#### Performance Evaluation Criteria

- Running Time
  - Serial: $T_s$, from beginning to end.
  - Parallel: $T_p$, from beginning to the end of last process.
- Extra cost for Parallel
  - $T_o=pT_p-T_s$
- Speedup ratio: $S=\frac{T_s}{T_p}$

### Scalability

$E=\frac{S}{P}=\frac{T_s}{pT_p}=\frac{1}{1+\frac{T_o}{T_s}}$

# SIMD

## Serval Concepts

Vector computer

SIMD

- Multi-media: SSE, AVX
- Graphics processor: CUDA

### Application of SIMD

- image process
  - 3D game, movie
  - image identification
  - video encode/decode: jpeg, mpeg4
- audio
  - encode/decode: IP Phone, mp3
  - audio identification
  - DSP
- scientific computing

### Features suitable for Application

- Regular data access patterns: continuous storage
- short data: 8/16/32 bit
- streaming data
- in some cases:
  - lots of constant
  - short loop or iteration
  - arithmetic saturation 算术饱和

## Problems of SIMD

Multiple arithmetic→a SIMD operation

Multiple fetch/store operation→a wider storage operation

### Complexity

- High level: utilize compiler
- Low level: intrinsic or assembly (cumbersome and error-prone)
- Data must be align and continuous in storage
  - misaligned data may introduce wrong result.
- Control flow introduces more complexity, may cause inefficiency.

## SSE/AVX

AMD&Intel: x86 ISA support SIMD.

SSE

- Streaming-Single instruction multiple data Extensions, 1999

- SSE2, 2001; SSE3, 2004; SSE4, 2007

AVX

- Advanced Vector eXtensions, 2011
- AVX2, 2013
- AVX-512, 2017

### SSE instructions

- Data move instruction: move in/out vector register
- Arithmetic instruction: arithmetic operation on multiple data
- Logic instruction: logic operation on multiple data
- Compare instruction: compare on multiple data
- Shuffle instruction: move data within vector register

SSE instruction in C++: intrinsic

```c++
#include <xmmintrin.h>  //SSE
#include <emmintrin.h>  //SSE2
#include <pmmintrin.h> //SSE3
#include <tmmintrin.h>  //SSSE3
#include <smmintrin.h> //SSE4.1
#include <nmmintrin.h>  //SSSE4.2
#include <immintrin.h> //AVX、AVX2、AVX-512
```

Compile option: `-march=corei7`

Data type (map to XMM register)

```c++
__m128//float
__m128d//double
__m128i//integer
```

Data move and initial

```c++
_mm_load_ps, _mm_loadu_ps, _mm_load_pd, _mm_loadu_pd, etc
_mm_store_ps//und so weiter…
_mm_setzero_ps
```

Arithmetic intrinsics:

```c++
_mm_add_ss, _mm_add_ps//und so weiter…
_mm_add_pd, _mm_mul_pd
_mm_hadd_ps// [x4,x3,x2,x1][y4,y3,y2,y1] = [y3+y4, y1+y2, x3+x4, x1+x2]
```

Logic: `_mm_and_ps` (andnot, or, xor)

Compare: `_mm_compeq_ps` result register all 0/1

`_mm_comieq_ss` result in EFLAGS, return boolean

Prefetch intrinsics: `_mm_prefetch(char const *a, int sel)`

# Pthread

## POSIX Thread

Pthreads are POSIX (Portable Operating System Interface for UNIX) compliant

## Pthread API

### Fork a thread

`int pthread_create(pthread_t *,const pthread_attr_t *, void *(*)(void *), void *);`

example: `errcode=pthread_create(&thread_id,&thread_attribute,&thread_func, &func_arg);`

- `thread_id` is ID of thread
- `thread_attribute` are attributes, `nullptr` means default attributes.
- `thread_func` means function to run
- `func_arg` means arguments for function
- `errcode` means if fail to fork, return a non-zero value.

effect of `pthread_create`:

- create a new thread with the help of OS.
- execute a certain function `thread_func`

a simple example:

```c++
int main() {
  pthread_t threads[16];
  int tn;
  for(tn=0; tn<16; tn++) {
    pthread_create(&threads[tn], NULL, ParFun, NULL);
  }
  for(tn=0; tn<16 ; tn++) {
    pthread_join(threads[tn], NULL);
  }
  return 0;
}
```

Global variables are shared.

Object arranged in heap may be shared.

Variables in stack are private.

### Join

`int pthread_join(pthread_t *,void **value_ptr);`

### Other APIs

`void pthread_exit(void *value_ptr);` return result to callee through `value_ptr`

`int pthread_cancel(pthread_t thread);` cancel the exe of thread

Synchronization: busy-wait, mutex

### semaphore

aka 信号量

Initialize semaphore

```c++
#include <semaphore.h>
int sem_init(sem_t *sem, int pshared, unsigned value);
```

`pshare`: non-zero for shared between process, zero for shared between thread in a process.

using semaphore

```c++
int sem_wait(sem_t *sem); // sem decrease by 1, block if 0
int sem_post(sem_t *sem); // sem increase by 1, may wake if 0
```

free semaphore

```c++
int sem_destroy(sem_t *sem);
```

### Barrier

initialize barrier:

```c++
pthread_barrier_t b;
pthread_barrier_init(&b,NULL,num_of_thread);
```

the second arg means attributes of object, NULL if default.

wait for barrier:

```c++
pthread_barrier_wait(&b);
```

Assign a init val to initialize barrier:

```c++
PTHREAD_BARRIER_INITIALIZER(num_of_thread)
```

### Mutex

mutex means simply lock/unlock.

Take lots of OS resource.

API:

```c++
pthread_cond_init(condition, attr);
pthread_cond_destory(condition);
pthread_condattr_init(attr);
pthread_condattr_destory(attr);
pthread_cond_wait(condition, mutex);//If the condition is not true, it will be blocked, and it was unlocked before
pthread_cond_signal(condition);//Trigger condition, possibly waking up a blocked thread
pthread_cond_broadcast(condition);//wake up multiple threads.
```

## Synchronization

Due to modern OS features.

- Processor put memory-write operator in write-pipeline queue.
- Merging "redundant" writes

### Basis

2 basic operation

- Atomic Instruction Sequence
- voluntary release execution

# OpenMP

## OpenMP Parallel Model

A common alternative to Pthread, simpler but also more restrictive.

Portable

Scalable

Rely on Compiler

OpenMP can

- hide stack managing
- provide synchronize mechanism

OpenMP can not

- Auto - parallel
- ensure acceleration
- Avoid data competition

OpenMP exe model

- Main thread
- Start Parallel: main proc create a set of threads (work thread)
- End Parallel: synchronization for thread set, latent barrier
- main thread

Compile instruction: `pragma`

OpenMP:

```c++
#ifdef _OPENMP
#include <omp.h>// Header for OpenMP
#endif

int bigdata[1024];//global shared
void foo(void *bar){
    int tid;//private in-stack
    #pragma omp parallel \
	shared ( bigdata ) \
	private ( tid )
	{
	    /* calc here. */
	}
}
```

Runtime seek function:

- `int omp_get_num_threads(void);`: return number of threads in current parallel part.
- `int omp_get_thread_num(void);`: return id of current thread in current parallel part. Value is between 0 and `omp_get_num_threads()`-1.

Parallel part

- multi-threads parallel execute code block

- SPMD

- `#pragma omp directive_name[clause[clause]...]`

  `//语句块`

- compile option: `gcc -fopenmp`

## Loop Parallel

Compiler's duty:

- Each compile instruct begins with `#pragma`

- Computing loop scale for each thread
- Manage data division
- Auto barrier

Restriction:

- loop should satisfy:
  1. loop iterator is a signed integer
  2. break check: \[iterator\] <, <=, >, >= \[a constant\]
  3. for each iteration, loop iterator +1/-1
  4. in the interior, not enter/exit control flow
- create thread, assign iteration among threads
- iteration times must be predictable
- do not check dependence!

## Data Dependence

When will data dependence not happen?

> Bernstein condition (1966): $I_j$ is a set of memory position read by process $P_j$, $O_j$ is a set of memory position written by process $P_j$. Now concurrency $P_j$ and $P_k$:
>
> - $I_j\bigcap O_k=\emptyset$ write after read
> - $I_k\bigcap I_j=\emptyset$ read after write
> - $O_j\bigcap O_k=\emptyset$ write after write

## Loop Scheduling

Static Mapping: Data-orient mapping

z.B. Matrix (by row, by column, by block)

## Locality

Cost of parallel algorithm: arithmetic operations (FLOPS) and communication (moving data)

optimize memory level

- reduce memory visit latency
- maximize memory bandwidth
- control cost

data reuse

- use same or adjacent data
- innate feature of computing

data locality

- data reuse, visit in "fast" memory
- use same data or same data expression

if reuse in computing, how can we get locality?

- suitable data layout
- code rearrangement

Unroll

## New Features of OpenMP

task-level parallel, atomic operation

SIMD, offload, thread bind, thread set

taskloop, target parallel

metadirective, flush, wider reduction

provider-consumer

# MPI

aka Massage Passing Interface

## Basic Concepts and Primitives

Message passing

Process is a PC + addr space, may contain serval threads.

Communication between processes is Message passing.

Message passing: synchronization and data moving.

### MPI features

- All Massage Passing and Synchronization rely on calling functions.

  No shared variable

- Provide functions as follow

  - Communication

    - P2P Communication
    - Group Communication

  - Synchronization

    - Barrier
    - Non-blocking

  - Query

    how many process? ID? Waiting?

### Primitives

To obtain basic info of runtime environment

- How many process involve?

  `int MPI_Comm_size(MPI_Comm comm,int *size);` reports numbers of process involve.

- Who am I?

  `int MPI_Comm_rand(MPI_Comm comm,int *rank);` reports callee's ID, ranging from 0 to size-1

Compiling MPI in Linux:`mpicc -g -Wall -o <runable file name> <src file name>`

Running MPI executable file: `mpiexec -n <number of process> <runable file name>`

In Windows, download MSMPI to enable MPI environment.

### Initialize and Finalize

`MPI_Init(int *argc_p,char ***argv_p);` Start essential initial work for MPI.

`MPI_Finalize(void);` End MPI, clear.

### Basic MPI Program Structure

```c++
//...
#include <mpi.h>
//...
int main(int argc, char *argv[]) {
  //...
  /* This part shouldn't call MPI */
  MPI_Init(&argc, &argv);
  /* MPI main body */
  MPI_Finalize();
  /* This part shouldn't call MPI */
  return 0;
}
```

### Message Passing in MPI

Process 0 `Send(data)` → Process 1 `Reveive(data)`

Process and Communication Domain

Communication domain don't have to contain all processes.

Each process in Communication domain has an ID "rank".

A process in different communication domain may have different rank.

MPI (blocking) Send: `int MPI_Send(void* buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm);`

- message buffer (`buf`, `count`, `datatype`)
- target process `dest`: rank of target process in `comm` assigned domain
- blocking send: when function returns, data has already submit to OS, but msg may haven't reach to target.

MPI (blocking) Receive: `int MPI_Recv(void* buf, int count, MPI_Datatype datatype, int source, int tag, MPI_Comm comm, MPI_Status *status);`

- blocking receive: wait until receive matched msg (with same `source` and `tag`)
- `source` can be ID in `comm`, or `MPI_ANY_SOURCE`
- `tag` are certain, or `MPI_ANY_TAG`
- It's allowed that the amount of data are less than the assigned, but more than are ERROR.
- `status` contains more information.

### MPI Datatype

- pre-define datatype correspond to high level language: `MPI_INT`, `MPI_DOUBLE`
- Continuous array of MPI datatype
- Leap block of MPI datatype
- Index array of MPI datatype
- Any structure of MPI datatype

A simple example:

```c++
#include<mpi.h>
#include<stdio.h>
#include<math.h>
int main(int argc,char *argv[])
{   
  int myid, namelen;
  char message[20];
  MPI_Status status;
  char processor_name[MPI_MAX_PROCESSOR_NAME];
  MPI_Init(&argc,&argv);
  MPI_Comm_rank(MPI_COMM_WORLD, &myid);
  MPI_Get_processor_name(processor_name,&namelen);
  if(myid == 0) {
    strcpy(message," Hello process 1");
  	printf("process 0 on %s send: %s\n", 
      processor_name, message);
    MPI_Send(message, 20, MPI_CHAR, 1, 99,
      MPI_COMM_WORLD);
  }
  else if (myid == 1) {
    MPI_Recv(message, 20, MPI_CHAR, 0, 99,
      MPI_COMM_WORLD, &status);
    printf("process 1 on %s received: %s\n", 
      processor_name, message);
  }
  MPI_Finalize();
  return 0;
}
```

## MPI Programming Model

Block Communication Pattern

It's important to receive before sending for highest performance.

Internal completion is soon followed by return of `MPI_Recv`.

2 proc can't receive and send at the same time. it may lead to dangerous communication or deadlock.

## Collective (Group) Communication

Communication, synchronization and computing

All process in a communication domain involved in communication at the same time.

Same function calling pattern

Context of collective communication is assigned by domain.

### Broadcast and Reduction

one-to-all broadcast

`int MPI_Bcast(void* buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm);` root means the same sender, count means number of msgs waiting to broadcast.

- a process send same msg to other processes
- at first, only source process have a m-byte msg; after broadcasting, all processes have same msg.

all-to-one reduction

`int MPI_Rduce(void* sendbuf, void* recvbuf, int count, MPI_Datatype datatype, MPI_Op op, int root, MPI_Comm comm);`

- at first, all processes have a m-byte msg.
- after reduction, p msgs become a data through calc, send to target process.

all-to-all broadcast & reduction

- application: matrix multiply, vector multiply from matrix.

Group collect

`int MPI_Allgather(void*sendbuf, int sendcount, MPI_Datatype sendtype, void* recvbuf, int recvcount, MPI_Datatype recvtype, MPI_Comm comm);` belongs to all2all broadcast

reduction then broadcast

`int MPI_Reduce_scatter(void* sendbuf, void* recvbuf, int *recvcounts, MPI_Datatype datatype, MPI_Op op, MPI_Comm comm);` belongs to all2all reduction

All reduction

`int MPI_Allreduce(void* sendbuf, void* recvbuf, int count, MPI_Datatype datatype, MPI_Op op, MPI_Comm comm);` equals to make each process as root to execute a same reduce operation.

Scan

`int MPI_Scan(void* sendbuf, void* recvbuf, int count, MPI_Datatype datatype, MPI_Op op, MPI_Comm comm);` means each process put reduction operation on its last process.

- Prefix sum

Scatter and Gather

- scatter: one-to-all, but different msg
- gather: all-to-one, but different msg, no merge/reduction.

All-to-all Individualized Communication

- all2all, but different msg.
- application: FFT, matrix transpose, parallel database join.

```c++
int MPI_Alltoall(void* sendbuf, int sendcount, MPI_Datatype sendtype, void* recvbuf, int recvcount, MPI_Datatype recvtype, MPI_Comm comm);
int MPI_Alltoallv(void* sendbuf, int *sendcounts, int *sdispls,MPI_Datatype sendtype, void* recvbuf,int *recvcounts, int *rdispls,MPI_Datatype recvtype, MPI_Comm comm);
```

### Broadcast Algorithm

#### Circle/Linear

Naive Algorithm: send to p-1 in order. inefficient utilize of mesh.

Reduction Double:

1. source P → P'
2. P, P' → two other process

in order to avoid conflict, introduce circle:

1. P → the most distant process (p/2)
2. two process → two (p/4) processes

#### Mesh

A extension of linear broadcast

1. broadcast by row
2. broadcast by column

3D mesh

- each dimension has $p^{\frac{1}{3}}$ nodes.

## Non-blocking Communication

Basic Meaning

- Callee return ≠ communication done

Advantage

- overlap of computing and communication, time-saving

Form

- non-blocking send & receive

Message passing and buffer

- send done only when buffer user provide can be reuse
- send done not means receive done
  - msg may in OS buffer
  - msg may in transmission procedure

Difficulty of non-blocking

- OS
  - hardware should support in the backend, CPU is not involved.
  - corresponding API
- Algorithm
  - must ensure in the overlap, no dependency exists between computing and communication
  - in most cases need to reconstruct algorithm

...... 不想复习了，40min 的 APS 其中 20 min 的面谈再怎么问也不可能问的这么细吧:astonished:

## Multi-thread Mix Programming

Multi-core cluster common programming form:

- MPI only
- MPI (among nodes)+OpenMP (within node)
- MPI (among nodes)+Pthread (within node)

the later 2 ways are called Mix Programming

...... 不想复习了，爱咋咋地吧:cry: