---
title: "实现一个简易的C++ String类"
date: "2022-09-24"
tags: ["cpp"]
summary: " "
draft: false
author: kratos
---

# 概述

实现 String 类的核心就是重写构造器和重载运算符.

## 头文件

我们需要定义的有：

- Original Constructor
- Copy Constructor
- Move Constructor
- Copy Assignment
- Move Assignment
- Destructor
- Indexer
- Equal/NotEqual Operator

```cpp
#ifndef CAMILLE_STRING_H
#define CAMILLE_STRING_H

// using char to store
class String {
  char* str;

public:
  String(const char* s = "");
  String(const String&); // copy constructor
  String& operator=(const String&); // copy assignment
  String(String &&) noexcept ; // move constructor
  String& operator=(String&&) noexcept ; // move assignment
  ~String(); // destructor
  operator const char* ();
  String operator+(const String&);
  String &operator+=(const String&);
  char operator[](int) const;
  char &operator[](int);
  String operator()(int, int);
  bool operator==(const String&);
  bool operator!=(const String&);
};

#endif

```
