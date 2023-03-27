---
title: TLPI
created: 2022-12-13 12:10:34
modified: 2023-03-27 10:29:34
aliases: [The Linux Programming Interface]
tags: [CS, Linux]
---

[[pages/Linux]] is a preemptive multitasking operating system.

## The history of UNIX

UNIX(AT&T) -> BSD(Berkeley), System V(AT&T)

## Acronym

- GNU (GNU's not Linux)
- FSF (Free Software Foundation)
- ANSI (American National Standards Institute)
- POSIX (Portable Operating System Interface)
- IEEE (The Institute of Electrical and Electronics Engineers)

## Tasks performed by the kernel

- Process scheduling
- Memory management (Virtual Memory Management)
  - Processes are isolated from one other and from the kernel (user space, kernel space **the linux memory model**), one process can't read or modify the memory of another process or the kernel (memory safety)
- Provision of a file system (the mighty overlay2)
- Creation and termination of processes
  - Load a new program into memory
  - Provide the program with the resources
- Access to devices (mice, monitors, keyboards, disk and tape drives and so on)
- Networking
- Provision of a system call API

## References

- [TLPI](https://man7.org/tlpi/index.html)
