---
title: APS 非重点复习课程之 Python
author: tinsir888
date: 2022/11/03
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - Python
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 1e2c55e
---

# Data Structure

## List

```python
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5, 6, 7 ]
print ("list1[0]: ", list1[0])
print ("list2[1:5]: ", list2[1:5])
```

## Tuple

similar to List, but the element in tuple can't be changed

```python
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5, 6, 7 )
print ("tup1[0]: ", tup1[0])
print ("tup2[1:5]: ", tup2[1:5])
```

## Dictionary

\{ key : value \}

```python
dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
print ("dict['Name']: ", dict['Name'])
print ("dict['Age']: ", dict['Age'])
```

# Class

```python
# -*- coding: UTF-8 -*-

class Parent: # 定义⽗类
	parentAttr = 100
	def __init__(self):
		print "调⽤⽗类构造函数"
	def parentMethod(self):
		print '调⽤⽗类⽅法'
	def setAttr(self, attr):
		Parent.parentAttr = attr
	def getAttr(self):
		print ("⽗类属性 :", Parent.parentAttr)
class Child(Parent): # 定义⼦类
	def __init__(self):
		print ("调⽤⼦类构造⽅法")
	def childMethod(self):
		print ('调⽤⼦类⽅法')
	def func(obj): #多态性 Polymorphism
		obj.run()

c = Child() # 实例化⼦类
c.childMethod() # 调⽤⼦类的⽅法
c.parentMethod() # 调⽤⽗类⽅法
c.setAttr(200) # 再次调⽤⽗类的⽅法 - 设置属性值
c.getAttr() # 再次调⽤⽗类的⽅法 - 获取属性值
```

# Pymysql

A Python Library used to connect the database

```python
import pymysql
conn = pymysql.connect(
	host=“你的数据库地址”,
	user=“⽤户名”,password=“密码”,
	database=“数据库名”,
	charset=“utf8”)
cursor = conn.cursor()
sql = """
CREATE TABLE USER1 (
id INT auto_increment PRIMARY KEY ,
name CHAR(10) NOT NULL UNIQUE,
age TINYINT NOT NULL
)ENGINE=innodb DEFAULT CHARSET=utf8; #注意：charset='utf8' 不能写成utf-8
"""
# 执⾏SQL语句
cursor.execute(sql)
# 关闭光标对象
cursor.close()
# 关闭数据库连接
conn.close()
```

