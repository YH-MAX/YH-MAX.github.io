---
title: "Understanding Hashing"
date: 2026-05-31
draft: false
tags:
  - Algorithms
  - Data Structures
---

# Understanding Hashing

Hashing is one of the most important techniques in computer science.

## Why do we need Hashing?

Many applications require fast search operations:

- Dictionary
- Database Index
- Symbol Table
- File System Index

## What is a Hash Table?

A hash table stores data using a hash function.

Example:

h(k) = k mod m

## Collision

A collision occurs when two keys map to the same slot.

Example:

15 → 5

25 → 5

## Chaining

Store colliding elements in a linked list.

## Open Addressing

Store all elements directly in the hash table.

### Linear Probing

### Quadratic Probing

### Double Hashing

## Conclusion

Hash tables provide average O(1) search, insertion and deletion performance.