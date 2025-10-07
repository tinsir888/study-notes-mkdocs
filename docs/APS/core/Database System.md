---
title: APS 重点复习课程之数据库系统
author: tinsir888
date: 2022/10/20
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 数据库系统
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: eded6502
---

# Overview

Main Concept

- Candidate Key
- Primary Key
  - Primary Attribute

- Foreign Key

The Structure of DBS

- Internal Schema
- Schema
- External Schema

SQL

Norm Form

- 1NF
- 2NF
- BCNF
- 3NF

ER Model

Transaction

- Atomic
- Durability
- Isolation
- Consistency

Failure and Recovery

- Transaction Failure: Backward Scan Log File
- System Failure: Forward Scan Log File
- Media Failure

Concurrency Control

- Lost Update
- Not-repeatable Read
- Dirty Read

Lock

- S-Lock
- X-Lock

Two Phase Locking

- in the former phase add locks only
- in the later phase release locks only

Deadlock

- Reasons
- Solutions

# Main Concept

DBMS: abbr. of Data Base Management System, is a piece of software designed to store and manage databases. z.B. DB2, Oracle, MySQL, MS SQL Server, ...

DBS: abbr. of Data Base System = DBMS + data + application programs

Entity, Attribute, Key

Domain: a collection of the same data type

Entity type, Relationship

Super keys: set of attributes of table for which every row has distinct set of values

Candidate Key: The value of an attribute group can uniquely identify a tuple, but its subset cannot. aka "minimal" super keys.

Primary Key: One of the candidate key, chosen by DBA

Primary Attribute: the attributes of candidate key

Foreign Key

# The Structure of DBS

## 3-level schema structure of the DB

### Internal Schema

A description of the physical structure and storage of the data.

Methods included: Hash map or B+ Tree

### Schema

A description of the logical structure and characteristics of all data in the database, such as the type of data and the length of data, the relationships between data as well.

A specific value of schema is called instance.

### External Schema

A description of the logical structure and characteristics of the local data that users can see a database have a lots of external schema.

It's the view of users, an external schema can be used by multiple applications.

It's effective precaution of database security, a user can only see the data in the external schema while other data out of external schema is invisible.

## Secondary Image Function of the DB

### External mode/mode image

- When the schema is changed, we can adjust the external mode/mode image to let the external schema remain unchanged. Because the application is designed based on external mode, if external mode remain unchanged, then the applications don't need any changes as well.
- Each external mode have a image to the unique mode.
- This kind of image ensure the logical independence between data and program.

### Mode/Internal mode image

- When the internal schema is changed, we can adjust the internal mode/mode image to let the schema remain unchanged, thus the applications don't need any change.
- That ensure the physical independence between data and program.

## Why 3-level Schema

1. User can access the data without too much care about the storage and organization data.
2. Simplify the design of application, the independence between data and program ensure that.

# SQL

## Create/Drop Schema

Create a schema can be seen as the definition of the namespace.

```mysql
CREATE SCHEMA XX Authorization <usrname>
```

Drop schema:

```mysql
DROP SCHEMA XX CASCADE | RESTRICT
```

CASCADE: drop all the related objects of the schema as well

RESTRICT: It can be dropped only when there is no existed objects of this schema.

## Table

Create table:

```mysql
CREATE TABLE XX
(
    some attribute TYPE PRIMARY KEY ()
    some attribute TYPE UNIQUE
    cpno CHAR(4) CHECK (cpno IN ('one','two'))
    FOREIGN KEY(cpno) REFERENCE course(cno)
);
```

- Only one PRIMARY KEY, several UNIQUE attributes.
- No attribute of a PRIMARY KEY can ever be NULL in any tuple. But attributes declared UNIQUE may have NULL.

Alter and drop table:

```mysql
ALTER TABLE Persons
ALTER COLUMN Birthday Year#修改列名
DROP COLUMN Year#删除列

ALTER TABLE Persons ADD CONSTRAIN CK_AGE CHECK(SEX = 1 OR SEX = 0)#添加约束

ALTER TABLE XX ADD Name type CONSTRAIN OF COLUMN
ADD CONTRAIN OF TABLE
```

## Trigger

```mysql
CREATE TRIGGER trigger_name trigger_time trigger_event ON tb_name FOR EACH ROW trigger_stmt
```

trigger\_name: name of trigger

trigger\_time: time to trigger, `BEFORE` or `AFTER`

trigger\_event: incident to trigger, `INSERT`, `DELETE` or`UPDATE`.

tb\_name: Name of table to set trigger on.

trigger\_stmt: What does trigger do, can be a SQL instruction or multi-statement started after `BEGIN` and end before `END`.

There are 6 types of triggers in MySQL:

```mysql
BEFORE INSERT
BEFORE DELETE
BEFORE UPDATE
AFTER INSERT
AFTER DELETE
AFTER UPDATE
```

## Query

### select... from... where...

```mysql
SELECT AttributeName
FROM TableName
WHERE X=XX | X LIKE XX | IN XX |
```

## Joint Query

```mysql
SELECT student sno,sname
FROM student, sc
WHERE student.sno=sc.sno AND sc.cno='2' AND sc.grade>90
```

### exists

z.B. Find name of employee in department which only have only one employee.

```mysql
SELECT empname FROM employee e1
WHERE NOT EXISTS (SELECT * FROM employee WHERE deptno=e1.deptno AND empname<>e1.empname)
```

## Nested Query

Nest a internal query inside the WHERE of external query.

### any & all

Find the beers not sold for the lowest price.

```mysql
SELECT beer FROM sells WHERE price>ANY(SELECT price FROM sells)
```

- x = ANY is true if and only if x equals at least one tuple in the relation.
- Similarly, = can be replaced by any comparison operator.
- x > ANY, means x is larger than at least one tuple in the relation.

Find the beers sold for the highest price.

```mysql
SELECT beer FROM sells WHERE price>=ANY(SELECT price FROM sells)
```

- x > ALL, means x is larger than any one tuple in the relation.

### union, intersection, difference

Find the drinkers and beers such that the drinker likes the beer and frequents a bar that serves it.

```mysql
(SELECT * FROM likes) INTERSECT (SELECT drink,beer FROM sells,frequents WHERE frequents.bar=sells.bar)
```

### distinct

Find the different price for beers.

```mysql
SELECT DISTINCT price FROM sells
```

### aggregations

sum, avg, min, max, count, count(\*).

```mysql
SELECT AVG(price)
FROM sells
WHERE beer='bud'

SELECT COUNT(DISTINCT price)
FROM sells
WHERE beer='bud'
```

### grouping

Given a table workson(empno, projectno, job, enterdate)

How many projects does each employee participate in?

```mysql
SELECT empno,COUNT(*)
FROM workson
GROUP BY empno;
```

How many employees participate in each project?

```mysql
SELECT projectno,COUNT(*)
FROM workson
GROUP BY projectno;
```

How many employees for each job?

```mysql
SELECT job,COUNT(*)
FROM workson
GROUP BY job;
```

### having

HAVING clauses are selections on groups.

Find the average price of those beers that are either served in at least 3 bars or manufactured by Busch.

Beers(name(p-key),manf); Sells(bar(p-key), beer(p-key), price)

```mysql
SELECT beer,AVG(price)
FROM Sells
GROUP BY beer
HAVING COUNT(*)>=3 OR beer IN (SELECT name FROM beers WHERE manf='Busch')
```

## SQL Authorization

### Privileges

Important privileges on a relation:

SELECT INSERT DELETE UPDATE

### Grant and Revoke

GRANT:

```mysql
GRANT <list_of_privileges> ON <relation_or_object> TO <list_of_authorization_ID>
```

if you want the recipients to be able to pass the privileges to others, add:

```mysql
WITH GRANT OPTION
```

REVOKE:

```mysql
REVOKE <list_of_privileges> ON <relation_or_object> FROM <list_of_authorization_ID>
```

REVOKE OPTIONS:

```mysql
CASCADE#any grants made by a revoke are also not in force.
RESTRICT#if the privilege has been passed to others, the REVOKE fails as a warning that something else must be done to chase the privilege down.
```

### Grant Diagrams

......

# Norm Form

## Functional Dependency

X, Y is the **subset** of the attribute set U.

If there are no such 2 tuples (X, Y), if every X have only one corresponding Y, then $X\rightarrow Y$, aka Y functional depends on X.

Fully functional dependency:

- if $X\rightarrow Y$ and to every true subset of X named X', we have $X'\nrightarrow Y$ then Y fully functional depends on X, remarked as $X\stackrel{F}\rightarrow Y$.

Partial functional dependency:

- if $X\rightarrow Y$ and Y is not fully functional depends on X, then Y partly depends on X, remarked as $X\stackrel{P}\rightarrow Y$.

Transitive functional dependency:

- if $X\rightarrow Y$, $Y\nrightarrow X$, $Y\rightarrow Z$, then Z transitive functional depends on X. remark as $X\stackrel{transitive}\longrightarrow Z$

## Relational Patterns​ ​非常​重要:warning:

Schema design motivation:

1. redundancy
2. inconsistency
3. update anomalies
4. deletion anomalies
5. insertion anomalies

### 1NF first normal form

every attributes are inseparable.

### 2NF second normal form

Every non-primary attribute fully functionally depends on any candidate key.

### BCNF Boyce Codd normal form

A relation R is in BCNF iff:

- Whenever there is a nontrivial FD $A_1,A_2,\cdots,A_n\rightarrow B$. (nontrivial means A is not a member of set X s.t. $X\rightarrow A$)
- for R, it is the case that $\{A_1,A_2,\cdots,A_n\}$ is a super-key for R.

BCNF removes certain types of redundancy & avoids info loss.

### 3NF third normal form

$X\rightarrow A$ violates 3NF iff X is not a super-key and A is not prime.

z.B. FD: AB→C and C→B, we have keys AB and AC. Thus A, B and C are each prime. Although C→B violates BCNF, it doesn't violate 3NF.

A table is in 3NF iff both of the following conditions hold:

- The [relation](https://en.wikipedia.org/wiki/Relation_(database)) R (table) is in [second normal form](https://en.wikipedia.org/wiki/Second_normal_form) (2NF).
- Every non-prime attribute of R is non-transitively dependent on every key of R.

### 4NF fourth normal form

Multivalued dependencies (MVD) $X\rightarrow\rightarrow Y$ is an assertion that if 2 tuples of a relation agree on all the attributes of X, then their components in the set of attributes Y may be swapped, and the result will be 2 tuples that are also in the relation.

Data redundancy.

z.B. Drinkers(name, addr, phones, beersLiked). 2 MVD in this: name→→phones, name →→beersLiked.

A relation R is in 4NF if whenever X→→Y is a *nontrivial* MVD, then X is a super-key.

nontrivial: Y is not a subset of X, and X & Y are not, together, all the attributes.

# ER Model

Entity-Relationship Model

## ER Diagram

### Relation Multiplicity

- one - one: ←菱形→
- many - one: —菱形→
- many - many —菱形—

### Multiway Relationships

Converting a multiway one into many binary ones.

## Constraints

Keys: unique identifier. z.B. social security number for a person

Referential integrity constraints: z.B. if you work for a company, it must exist in the database.

Domain constraints: z.B. people's ages are not negative.

General constraints: all other constraints.

## Design principles

### Be faithful

Design should reflect ideas of the DB.

### Avoid redundancy

- Redundancy occurs when we say the same thing in two different ways.
- Redundancy wastes space and **encourage inconsistency**.

### Entity Sets VS Attributes

Don't use an entity set when an attribute will do.

An entity set should satisfy at least one of the following:

- It has at least one non-key attribute.
- It is the "many" in a many - one or many - many relationships.

### Limit the use of weak entity sets

weak entity set: a weak entity set has one or more many - one relationships to other entity sets. *The key for a weak entity set is its own underlined attributes and the keys for the supporting entity sets.*

Chain of Weakness

## Translating Subclass Entities

3 approaches:

1. Object-oriented: each entity belongs to exactly one class; create relation for each class, with all its attributes.

2. ER style: create one relation for each entity set, with only the key attributes and attributes attached to that entity set; entity represented in all relations to whose entity set it belongs.

3. Use nulls: create one relation; entities have null in attributes that don't belong to them.

   space-wasting

## Integrity Constraints

### Entity Integrity

Attribute of primary key can't be NULL.

### Referential Integrity

It requires that if a value of one attribute (column) of a relation (table) references a value of another attribute (either in the same or a different relation), then the referenced value must exist.

### User-defined Integrity

User define which attributes should be UNIQUE or NOT NULL.

# Transaction

A sequence of operations, we can either accomplish all these operations or none of them.

## Atomic

Either all transaction are executed or none.

## Durability

Once after the commit of transactions, its operation to database should be permanently saved.

## Isolation

The execution of transactions won't disturb each others.

## Consistency

The database can only contain the successfully executed transactions, otherwise will the database not in a consistent state.

z.B. After a bank transfer the total deposits should remain unchanged.

# Failure and Recovery

## 3 Failure

### Transaction Failure: Backward Scan Log File

The transaction doesn't reach the commit or the rollback, then the database might not be in a consistent state, thereby we should undo the failed transaction without disturbing the other transactions.

### System Failure: Forward Scan Log File

The system corrupt and every transaction in the memory is interrupted, which means the transactions haven't reach to the commit or rollback need to be undo and the committed transactions maybe still store in the data buffer and haven't write to the physical database, these transactions need to be redone.

### Media Failure

The physical storage media corrupt, z.B. the disk is physical broken.

## Log File

Record the operation of every transaction, each log record consists of the begin transaction mark and the commit or rollback mark, the new value after update and the old value before update, as well as the ID of transaction.

1. The registration order should strictly correspond to the order of execution.
2. Register on the log file first, then write to the database.

## System Recovery Strategy

1. The recovery of transaction failure:

   Backward scan log file, undo the operation of the transaction, delete the insert, undo the update (write the value before update back to the database)

2. The recovery of system failure:

   Forward scan log file, put finished process before into redo-list, unfinished process into undo-list.

3. The recovery of media failure:

   Load the latest backup of database and the backup of log file, do the same thing as system failure.

# Concurrency Control

In order to ensure the isolation and consistency we need concurrency
control the concurrency can lead to following failures:

## Lost Update

Transaction A and B write the same data back to the database then the later update is lost.

## Not-repeatable Read

Transaction B update the data after transaction A have read it then the transaction A can't reproduce the previous data.

## Dirty Read

Transaction A write a data back to the disk and the same time transaction B read the updated data, but transaction A is undo by some reason and the data recover to the original value.

# Lock

## Share/Exclusive lock

### S-Lock

aka share lock:

If transaction T gives a S lock to data A, then T can read A but can't update A and the other transaction can only give S lock to A but not X locks.

### X-Lock

aka exclusive lock:

If transaction T gives a X lock to data A, then only T can read or update A and all the other transactions can't give any lock to A.

## Serializable Scheduling Method

The concurrency of multiple transaction is correct only if the result is the same as the result of serial execution.

### Precedence Graph

Top sort...

## Two Phase Locking Protocol (2PL)

In order to ensure two transactions are serializable.

- in the former phase add locks only

- in the later phase release locks only

z.B. S-lock A S-lock B X-lock C Unlock A Unlock C Unlock B

## Deadlock

### Reasons

Refer to operating system

The condition of the deadlock

1. mutual exclusion
2. Hold and wait or resource holding
3. no preemption 不剥夺条件
4. circular wait

### Solutions

1. Visit object at the same order
2. Avoid transactions between users.
3. Keep transactions short, at the same batch.

