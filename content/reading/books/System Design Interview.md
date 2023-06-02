---
title: System Design Interview, An Insider's Guide
created: 2023-01-08 01:34:23
modified: 2023-03-26 11:19:48
started: 2023-02-13
finished: 2023-04-30
read: 4
total: 16
author: [Alex Xu]
tags: [Book, CS, SystemDesign]
---

## General Architecture

![[../../images/system-design-architecture.png]]

### Suggestions for millions of users and beyond

1. Keep web tier stateless
2. Build redundancy at every tier
3. Cache data as much as you can
4. Support multiple data centers
5. Host static assets in CDN
6. Scale your data tier by sharding
7. Split tiers into individual services
8. Monitor your system and use automation tools

### A framework for system design interviews

1. Understands the problem and establish design scope
2. Propose high-level design and get buy-in
3. Design deep dive
4. Wrap up

**Dos**

- Always ask for clarification. Do not assume your assumption is correct
- Understand the requirements of the problem
- There is neither the right answer nor best answer. A solution designed to solve the problems of a young startup is different from that of an established company with millions of users.
- Let the interviewer know what you are thinking. Communicate with your interviewer.
- Suggest multiple approaches if possible
- Once you agree with your interviewer on the blue print, go into details on each component. Design the most critical components first
- Bounce ideas off the interviewer.

**Donts**

- Don't be unprepared for typical interview questions.
- Don't jump into a solution without clarifying the requirements and assumptions.
- Don't go into too much detail on a single component in the beginning. Give the high-level design first then drills down.
- If you get stuck, don't hesitate to ask for hints.
- Again, communicate. Don't think in silence.
- Don't think your interview is done once you give the design. You are not done until your interviewer says you are done. Ask for feedback early and often.

## References

- [bytebytego](https://blog.bytebytego.com/)
