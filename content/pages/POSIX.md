---
title: Portable Operating System Interface
created: 2023-01-11 16:54:56
modified: 2023-01-11 16:54:56
aliases: [Portable Operating System Interface]
tags: [CS, Linux]
---

It is a set of standards defined by the Institute of Electrical and Electronics Engineers (IEEE) that specify how operating systems should provide certain basic functionality, such as file and directory access, process management, and inter-process communication.

The goal of POSIX is to provide a consistent and portable interface for developers, so that software written for one POSIX-compliant operating system can be easily ported to another. This helps to increase the portability and maintainability of software, and to reduce the need for developers to learn multiple different APIs for different operating systems.

## Standard Interfaces

- The base definitions, which provide standard types, macros, and functions for use in application programming
- The system interface, which defines the system calls and library functions used to interact with the kernel and perform basic operating system operations
- The shell and utilities, which defines a set of standard command-line utilities and a shell (command line interpreter) interface
- The Realtime extension, which defines an extension to the system interface for developing real-time applications

## POSIX-compliant

- Linux
- MacOS
- Unix-based systems

## References

- [POSIX - wikipedia](https://en.wikipedia.org/wiki/POSIX)
