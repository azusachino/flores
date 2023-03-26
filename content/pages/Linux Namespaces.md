---
title: Linux Namespace
created: 2022-08-23 00:00:00
modified: 2022-12-05 14:05:27
tags: [CS, Linux]
---

The standing-stone of [[Container]] techniques.

## Namespace Kinds

- Mount (mnt)
- Process ID (pid)
- Network (net)
- Interprocess Communication (ipc)
- UTS
- User ID (user)
- Control Group (cgroup)
- Time

Since [[kernel]] version 5.6, there are 8 kinds of namespaces. Namespace functionality is the same across all kinds: each process is associated with a namespace and can only see or use the resources associated with that namespace, and descendant namespaces where applicable. This way each process (or process group thereof) can have a unique view on the resources. Which resource is isolated depends on the kind of namespace that has been created for a given process group.

## Mount Namespace

Mount namespaces control [[mount points]]. Upon creation the mounts from the current mount namespace are copied to the new namespace, but mount points created afterwards do not propagate between namespaces (using shared subtrees).

## References

- [Linux Namespaces - Wikipedia](https://en.wikipedia.org/wiki/Linux_namespaces)
