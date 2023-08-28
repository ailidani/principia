---
title: "Distributed Transactions"
date: "2017-08-13"
draft: true
tags: ["transaction"]
---



## __Occult__ (Observable causal consistency using lossy timestamps)

Observable causal consistency definition:
```
Alice write(a=1) -> |Shard A| --x--> |Shard A'|
Bob   read(a)=1  <- |Shard A|
      write(b=2) -> |Shard B| -----> |Shard B'| -> read(b) waits for read(a)
```
All existing causally consistent systems are susceptible to slowdown cascades.

_Solution_: timestamp like vector clocks
Write path: Client library attaches its causal timestamp to every write and sends it to the master of the corresponding shard. The master increments the relevant time stamp, updates the received causal timestamp, and stores it with new value. Then asynchronously replicates the writes to its slaves before returning to client library.

Read path: Read from any slave shard with higher timestamp, or retry read from master. Update local timestamp accordingly.

Transaction isolations:

 - Snapshot Isolation
 - Parallel Snapshot Isolation (PSI)
 - Per-Client Snapshot Isolation (PC-PSI) -- Occult

 - Read phase: client $c$ reads from local server for every object $o$ in transaction $T$ and store it as successful reads if it's not stale compare to local shard timestamp. Meanwhile, buffer all writes.
 - Validation phase:
 - Commit phase:


## __Consus__

Consus implements transactions with three phase commit protocol and writes the value into a Generalized Paxos system.

Assuming data is fully replicated between data centers, the transaction initiator $dc_1$ executes transaction $t$ and broadcast $t$ with its result state $s$ to all other data centers. Other data centers then re-executes $t$ and compare their result state to $s$.

The commit protocol in consus is too optimistic, would result in high abort rate under heavy write load. There is no evaluation results in the paper.


## __SLCP__ (Simple Leaderless Consistency Protocol)

```
Client       Servers
   |         |  |  |
   X-------->|->|->|  Write Request (sequence, id)
   |<--------X--X  |  Response
   |         |  |  |
   |         |  |  |
   X-------->|->|->|  Read Request (with retry)
   |<--------X--X  |
```

This protocol essentially implement an _atomic storage_ system that provides strong consistency that observable to clients. But it compares the performance with Multi-Paxos and Raft, which are consensus protocols that solves a harder problem.

The SLCP protocol basically uses a majority write and read quorums. Compare to the majority replication algorithm, SLCP does not require two phases for write operation, and reads are not guaranteed to complement in certain number of rounds.