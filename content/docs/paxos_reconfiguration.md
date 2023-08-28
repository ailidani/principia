---
title: "Paxos Reconfiguration"
date: "2017-05-11"
draft: true
tags: ["Paxos", "consensus"]
---

### Introduction

There have been several approaches to dynamic reconfiguration of consensus algorithms.
Dynamic Paxos (Multi-Paxos that supports reconfig) limits the number of concurrent proposals as a pipeline length parameter $\alpha > 0$, therefore a configuration change chosen at instance $i$ does not take effect until instance $i + \alpha$. Raft protocol supports unbounded pipeline by limiting reconfiguration operations to be only add or remove one node at a time. Vertical Paxos and Egalitarian Paxos employ a separate oracle to manage the configuration [^epaxos]. Zab added a limited-pipeline approach much like Dynamic Paxos [^zab].

### Flexible Paxos Reconfiguration

Flexible Paxos generalized Paxos quorums from

Any two quorums intersect
$$q_1, q_2 \in Q \implies q_1 \cap q_2 \neq \emptyset$$

to

Phase I and Phase II quorums intersect
$$q_1 \in Q^{I}, q_2 \in Q^{II} \implies q_1 \cap q_2 \neq \emptyset$$

Turner [^upaxos] describes the unbounded reconfiguration algorithm for Flexible Paxos.



[^upaxos]: Turner, David C. "Unbounded Pipelining in Dynamically Reconfigurable Paxos Clusters."

[^epaxos]: Moraru, Iulian, David G. Andersen, and Michael Kaminsky. "There is more consensus in egalitarian parliaments." *Proceedings of the Twenty-Fourth ACM Symposium on Operating Systems Principles.* ACM, 2013.

[^zab]: Junqueira, Flavio P., Benjamin C. Reed, and Marco Serafini. "Zab: High-performance broadcast for primary-backup systems." *Dependable Systems & Networks (DSN), 2011 IEEE/IFIP 41st International Conference on.* IEEE, 2011.
