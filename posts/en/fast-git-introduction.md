---
title: "Fast Git Introduction"
title-en: "Fast Git Introduction"
date: "2023-07-21"
summary: "A Fast Intro to Git Internals"
draft: false
author: "kratos"
tags: ["Basic Computer Science"]
---

## Basic Concepts

Most Git-related articles tell you what Git commands exist and how using them can accelerate your development process, but few explore Git's internal principles.

First, consider three basic concepts of Git repositories: Blob, Tree, and Commits.

### Blob

Blob is short for Binary Large Object.
Here's ChatGPT's summary:

- Blob is one of the basic objects in Git for storing file content.
- Each Blob object is identified by file content and SHA-1 hash value.
- Blob objects, together with Git's tree objects and commit objects, form Git's three basic object types.

### Tree

Git uses tree objects to store folders. Each tree contains a set of subtrees (subfolders) or Blobs (files). It's also identified by hash values. Notably, any modification to a child file will cause the root tree's hash value to change - truly affecting the whole from a single part.

### Commits

Commits consist of Trees and other metadata, also identified by hash values.

## Git Operation Related Concepts

### Ref

Git uses a directed acyclic graph to indicate a set of Refs (with null pointers as starting points). These references are mainly for human convenience, as hash values are too long. For example, the HEAD Ref points to the most recent commit.

### Three Areas

- Working Directory: Represents the directory currently in use;
- Staging Area: Can also be understood as cache or index, temporarily storing your changes;
- Repository: By committing content from the Staging Area, you get the Git Directory, also called tree or database.

### Merge vs Rebase

Chinese translations are 合并 and 变基, literally understandable. Let me expand appropriately:

- Git Merge preserves original Commits, then creates a new Commit pointing to HEAD, which may contain new code generated to resolve conflicts.
- Git Rebase copies original Commits, then continues adding new nodes forward from the old HEAD as the starting point, ensuring the DAG graph maintains a straight line.

Generally, Rebase is more recommended as it makes your Commits "stand out" ("the cream rises to the top"), but be careful - if others are developing based on your Commits, try to avoid using Rebase.

## Conclusion

Finally, try to use IntelliJ for development because its Local History can really be a lifesaver. Sometimes even `git reflog` might not work.