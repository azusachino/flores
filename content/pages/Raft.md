---
title: Raft
created: 2022-11-16 19:07:21
modified: 2022-12-05 14:05:27
tags: [CS, Consensus]
---

**Raft** is a [[consensus]] algorithm designed as an alternative to the [[Paxos]] family of algorithms. It was meant to be more understandable than Paxos by means of separation of logic, but it is also formally proven safe and offers some additional features. Raft offers a generic way to distribute a state machine across a cluster of computing systems, ensuring that each node in the cluster agrees upon the same series of state transitions.

## References

- [The Raft Consensus Algorithm](https://raft.github.io/)
- [Raft Consensus Algorithm](https://www.geeksforgeeks.org/raft-consensus-algorithm/)
- [Raft (algorithm)](https://en.wikipedia.org/wiki/Raft_(algorithm))
