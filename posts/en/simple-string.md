---
title: "Implementing a Simple C++ String Class"
title-en: "Implementing a Simple C++ String Class"
date: "2022-09-24"
tags: ["cpp"]
summary: " "
draft: false
author: kratos
---

# Overview

The core of implementing a String class is rewriting constructors and overloading operators.

## Header File

We need to define:

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