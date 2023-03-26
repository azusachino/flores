---
title: Postgres
created: 2022-12-13 18:17:42
modified: 2023-03-26 11:19:47
aliases: [PGSQL]
tags: [CS, DB]
---

## Basic Structure

![[../images/pg-database-structure.png]]

![[../images/pg-storage-structure.png]]

表和索引的数据文件默认为1GB，当超过该阈值时系统会创建新的数据文件，并加上`.1`开始的后缀名，也就是所谓的数据段。

## References

- [深入浅出PostgreSQL - 微信读书](https://weread.qq.com/web/reader/86a3216071d2825c86a37e0kc20321001cc20ad4d76f5ae)
