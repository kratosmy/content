---
title: "Git快速入门"
date: "2023-07-21"
summary: "A Fast Intro to Git Internals"
draft: false
author: "kratos"
tags: ["Basic Computer Science"]
---

## 基本概念

大部分和Git有关的文章都在告诉你Git有哪些命令，如何使用它们可以加速你的开发进程，但很少有人探讨Git的内部原理。

先考虑Git仓库的三个基本概念：Blob， Tree，Commits。

### Blob

Blob是二进制大对象的缩写。
以下是ChatGPT的总结：

- Blob是Git中存储文件内容的基本对象之一。
- 每个Blob对象由文件内容和SHA-1哈希值来标识。
- Blob对象与Git的树对象和提交对象一起构成了Git的三个基本对象类型。

### Tree

Git使用树对象来存储文件夹。每棵树都包含一组子树（子文件夹）或者Blob（文件）。它同样通过哈希值来标识，值得注意的是，
任何一个子文件的修改都会导致根树的哈希值改变，可谓牵一发而动全身。

### Commits

Commits是由Tree和其他一些元数据组成，同样通过哈希值标识。

## Git操作相关概念

### Ref

Git里通过一个有向无环图来指示一组Ref（空指针为起点），这些引用主要是方便人们查阅，因为哈希值太长了。比如HEAD Ref就是指向最近一次的提交。

### 三个区域

- Working Directory：表示当前正在使用的目录；
- Staging Area： 也可以理解为缓存或者索引，里面暂存你的更改；
- Repository：通过Commit Staging Area里的内容，得到Git Directory，也叫树或者数据库。

### Merge与Rebase

中文翻译为合并与变基，字面即可理解，下面适当展开：

- Git Merge会保留原始的Commits，然后会有一个新的Commit指向HEAD，里面可能会包含一些为解决冲突而产生的新代码。
- Git Rebase会复制原始的Commits，然后以旧的HEAD为起点继续向后添加新节点，这样可以保证这个DAG图保持一个直线。

一般情况下，Rebase更加推荐使用，这样你的Commit会“脱颖而出”（"the cream rises to the top"），但也要注意，如果其他人
在基于你的Commit进行开发，那么尽量不要使用Rebase。

## 结语

最后的最后，尽量使用Intellij开发，因为他的Local History真的可以救命，有时候`git reflog`也不一定管用。
