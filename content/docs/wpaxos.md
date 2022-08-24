---
title: "WPaxos"
date: "2017-08-17"
image: "wpaxos.png"
type: "blog"
draft: true
tags: ["transaction"]
---

## Proposal for WPaxos 2.0

Tasks:

- [ ] Vertical Paxos with moving objects in Master node
- [ ] Change default flexible quorums
- [ ] Dynamic Reconfiguration
- [ ] Transaction

### Flexible Quorums

To improve availability, we changed the default flexible quorum from majority nodes in a zone to majority nodes in one zone and one extra node from any other zone.

### Linearizable Log

```
 Zone1  Zone2   Zone3
 ----- ------- ------
|xxx  |xxxxxx |      |  e
 ----- ------- ------
|xxxxx|       |xx    |  e
 ----- ------- ------
|     |       |      |  e + 1
 ----- ------- ------
```
Each zone maintains a grid log, and starts a new row in the log in two scenarios, (1) when received a remote request from other zone, or (2) when epoch/ballot increased (i.e. configuration changed). As long as every leader applies the the grid log from left to right, top to bottom, global order of the execution is maintained.

In grid log, we are no long maintaining the log per object, but log per zone. And we should ensure there is only one leader in each zone.

### Dynamic Reconfiguration

### Distributed Transactions

What we had the idea in the paper is collect all objects in phase one, then commit the transaction in phase two. This requires extra transaction log to track positions in each object’s log. The down side is, when conflict rate is high between transactions, all concurrent transactions could be blocked indefinitely.

In grid log, stealing objects causes very affected zone to start a new row.
