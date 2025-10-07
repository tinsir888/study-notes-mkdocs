---
title: APS 非重点复习课程之可视化技术基础
author: tinsir888
date: 2022/11/10
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - MFC
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: c5bcea6e
---

The course is mainly about MFC (Visual C++)

Visual Studio

# C++ Basis

[detail](https://tinsir888.github.io/posts/72ba923e.html)

encapsulation, inheritance, polymorphism

Windows C++

- Event
- Handle
- Message

MFC aka Microsoft Foundation Class

Event-driven

message-handing

## MFC Basis

Visual Studio 2010

Compile - Link - Execute - Debug

Debug - smaller space, need compiler support

Release - larger space, execute compiler-free

MFC Class

- CObject
- CCmdTarget
- CWinThread
- CWinApp
- CDocument
- CView
- CMenu
- CDialog
- ......

Afx function

- AfxAbort
- AfxBeginThread
- AfxEndThread
- AfxMessageBox
- AfxGetApp
- AfxRegisterWndClass

Message

- PeekMessage: only check queue
- GetMessage: check and get message
- PreTranslateMessage: filter message
- DispatchMessage: find handing function

# Windows

Single Document Interface `CFrameWnd`

Multiple Document Interface `CMDIFrameWnd`, `CMDIChildWnd`

Dialog `CDialog`

Menu

- GetMenu
- GetSubMenu
- CheckMenuItem
- SetDefaultItem
- SetMenuItemBitmaps
- EnableMenuItem

# View

How to draw a pic?

How to output string?

...

# Dialog & Controls

`MessageBox(L"sdfekjfjke")`

layout: buttons...

return value: IDYES, IDNO, IDOK, IDCANCEL, IDABORT, IDIGNORE, IDRETRY.

