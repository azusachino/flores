---
title: High Performance MySQL
created: 2023-01-08 01:34:23
modified: 2023-03-27 10:36:03
aliases: [高性能MySQL]
started: 2023-02-13
finished: 2023-03-31
read: 2
total: 13
author: [Silvia Botros, Jeremy Tinley]
tags: [Book, CS, DB, MySQL]
---

## Contents

- MySQL Architecture
- Monitoring in a Reliability Engineering World
- Performance Schema
- Operating System and Hardware Optimization
- Optimizing Server Settings
- Schema Design and Management
- Indexing for High Performance
- Query Performance Optimization
- Replication
- Backup and Recovery
- Scaling MySQL
- MySQL in the Cloud
- Compliance with MySQL

## Architecture

- topmost layer: connection handling, authentication, security
- second layer: query parsing, analysis, optimization and all built-in functions
- third layer: storage engines (storing & retrieving data)

![[../../images/mysql-architecture.png]]

### Main Topics

- Logical Architecture
- Concurrency Control
  - Read/Write Lock
  - Lock Granularity (Table/Row Lock)
- Transaction
  - Isolation Level
  - ACID
  - WAL
  - Redo/Bin/Undo Log
- Multiversion Control
- Replication
- Datafile structure

A sequence diagram of handling multiple versions of a row across tractions:

![[../../images/mysql-transaction-sequence.png]]

When a different session reads a cluster key index record, InnoDB compares the record’s transaction ID versus the read view of that session. If the record in its current state should not be visible (the transaction that altered it has not yet committed), the undo log record is followed and applied until the session reaches a transaction ID that is eligible to be visible.

**Datefiles Structure**

In version 8.0, MySQL redesigned table metadata into a data dictionary that is included with a table’s `.ibd` file. This makes information on the table structure support transactions and atomic data definition changes. Instead of relying only on information_schema for retrieving table definition and metadata during operations, we are introduced to the dictionary object cache, which is a least recently used (LRU)-based in-memory cache of partition definitions, table definitions, stored program definitions, charset, and collation information.

## Monitoring in a Reliability Engineering world

- SLI
- SLO
- SLA

## Performance Schema

Performance Schema provides low-level metrics on operations running inside MySQL server.
