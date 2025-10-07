---
title: APS 重点复习课程之软件工程
author: tinsir888
date: 2022/10/28
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 软件工程
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: a37b98d7
---

# Overview

software development models

- waterfall model

  Divided the whole model into parts, we must finish the last task first, before starting the next task. The output of the last task is the input of the next task.

- incremental model

  Using Waterfall Model iteratively, develop a subset of the whole task, then do it repeatedly.

- rapid prototype model

  Quickly develop a subset of the program that is usable and deliver it the costumer. Gradually adjusting it according to the customers' demand.

agile development (敏捷开发), develop in a incremental and iterative way

- XP
  - plan
  - design
  - code: pair-programming
  - test
- Scrum
  - product backlog
  - sprint backlog
  - sprint
  - stand-meeting

Testing

- Black Box: look from user's way, is the function can be realized?
- White Box: look from inside of the project, check the rightness of each logical point.

# Definition

Software engineering is an engineering science that guides the development and maintenance of computer software.

# Software Development Models

## Waterfall Model

Each stage must have documentation, and each stage will check whether there are problems in the previous stages. 

Program is easier to maintain.

The final software product my not meet the needs of users.

## Incremental Model

use Waterfall Model iteratively

First develop a subset of the system.

Follow the same method to increase system functions.

## Rapid Prototype Model

Quickly build a runnable program and provide it to users.

Adjusting the program, according to users' changing demands.

# Agile Development

Software development in a fast incremental and iterative manner.

## Pair Programming

planning: collect user stories and split them into little tasks.

design: the principle of keep it simple

coding: pair programming, the task has high technical requirements

test: unit test, continuous integration.

## Scrum (Sprint)

The core is iteration.

1. product backlog: find out what should we commit
2. spring backlog: sprint backlog
3. sprint: coding and testing
4. daily scrum meeting: standing and keep focusing

# Testing

## Black Box Test

only test the system external characteristics

looking from users' view + equivalence partitioning (等效分区)

## White Box Test

every logical point in the program

Huge workload.