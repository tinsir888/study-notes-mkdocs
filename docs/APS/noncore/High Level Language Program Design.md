---
title: APS 非重点复习课程之高级语言程序设计
author: tinsir888
date: 2022/10/31
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - C++
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 72ba923e
---

# Data Type

```c++
int aint;
float afloat;
long long alonglong;
double adouble;
typedef struct{
    int num;
    char name[20];
    float fsocre;
}student;
student stu1, stu[31];
```

# Pointer

A pointer holds the address of the other objects, and we can directly access the physical memory address of the pointed object by using pointer.

```c++
int var_run=10;
int *p;
p=&var_run;
```

datatype of pointer `p` must align with `var_run`.

# Function Overloading

```c++
#include<stdlib.h>
#include<iostream>
using namespace std;
void fun(int i=30,int x = 10, int j = 20);
void fun(double i, double j);
int main() { //Default value from function
  //fun();
  fun(100);
  fun(100,200);
  fun(100, 200, 300); //Function overloadingfun(1.1, 1.2);
return 0;
}
void fun(int i, int j, int k) {
  cout << i << "," << j << "," << k << endl;
}
void fun(double i, double j) {
  cout << i << "," << j << endl;
}
```

# OO

## Features of Object-oriented:warning:

1. Encapsulation: the use of class 封装性

   class contains data and method.

2. Inheritance: super class & sub-class reuse code 继承性

3. Polymorphism: reuse interface. 多态性

   same interface, different methods.

```c++
class A{
	void f1() {printf(“1\n”); }
	virtual void f2() {printf(“2\n”);
};
class B : public A
{
void f1() { printf(“3\n”); }
void f2() { printf(“4\n”); }
};
int main(){
	A a; B b;
	A *p=&a;
	p->f1(); //1
	p->f2(); //2
	p=&b;
	p->f1(); //1
	p->f2(); //4
}
```

## Constructor

Used to initialize the class so that we can give the data of the object an initial value.

1. The name of constructor is the same as the class.
2. have it's own parameters just like normal functions.
3. It's auto executed during the creation of objects.

## PO VS OO

procedure-oriented versus object-oriented

The most difference is that while procedural programming uses procedures to operate data on data structures, object-oriented programming bundles the two together, called an "object", which is an instance of a class, operates on its "own" data structure.