---
title: "WanKeeper: Efficient Distributed Coordination at WAN-scale"
date: "2017-06-17"
draft: true
tags: ["wan", "consensus"]
---


In recent years, there have been more systems to distribute data at global scale, especially so given the rise of the [NewSQL](https://en.wikipedia.org/wiki/NewSQL) systems like [Google Spanner](https://cloud.google.com/spanner/) [^spanner], [CockroachDB](https://www.cockroachlabs.com/) and [Microsoft Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/). While providing the same scalable performance of NoSQL systems, they still maintain strong consistency guarantees by relying upon some forms of wide area network (WAN) coordination service. Among them, [ZooKeeper](http://zookeeper.apache.org/) [^zookeeper] is the most popular coordination service used in many projects in Facebook and Apache.

<!--![zk table](zk_table.png)-->
<div style="text-align:center">
<img src="zk_table.png" alt="centra" style="width: 600px;"/>
</div>


ZooKeeper implements the Zab [^zab] protocol that, similar to Multi-Paxos [^paxos] and Raft [^raft], relies on the stable leader $\lambda$ to serialize the total order of events. Every replica in the system only communicates with $\lambda$ unless there's leader failure. We call this type of systems **centralized** coordination. A centralized coordination cannot be used in WAN because its performance limitations, specifically, it fails to scale with respect to increasing distance from a replica to stable leader. Each operation will suffer from WAN latency.

<!--![centra](centralized.jpg)-->
<!--![decentra](leaderless.jpg)-->
<div style="text-align:center">
<img src="centralized.jpg" alt="centra" style="width: 250px;"/>
<img src="leaderless.jpg" alt="decentra" style="width: 250px;"/>
</div>

Other protocols, like Generalized Paxos [^gpaxos] and EPaxos [^epaxos], opportunistically allow **decentralized** coordination by depending on a fast quorum to detect any conflicts before the value is committed. The fast quorum size is much larger than a simple quorum, therefore, it fails to scale with respect to increasing number of nodes in a globally deployed system.

One simple solution to scale ZooKeeper to multiple sites is running separate ZooKeeper cluster in each site, then maintain consistency between clusters using external method like 2 phase commit (2PC) or *sync* operations. Such solution has two major drawbacks: (1) data in each cluster is statically partitioned, therefore performance is limited by the operations targeting nonlocal data; and (2) any inter-site coordination (e.g. 2PC) is complicated and does not scale with more sites.

WanKeeper provides a novel hybrid framework that extends centralized coordination by using hierarchical composition and token migration ideas. WanKeeper architecture is simple by design. We designate one cluster as the higher level (L2) cluster, preferably in the geographical center of the global system. L2-cluster's task is to serialize conflicting requests from any cluster and serve tokens to the suitable cluster while ensuring freedom from deadlocks and starvation. A token for each object in the system represents the exclusive ownership of that object. If tokens for all the objects needed for an operation are present at the cluster, the operation is executed at that cluster without involving communication to L2 for coordination. If an operation involves items for which tokens are not present in this cluster, the leader forwards the operation-request to L2.

We argue that the hierarchical architecture is more scalable because it reduces the communication complexity and exploits the locality exhibited in the workload.

<div style="text-align:center">
<img src="wankeeper.png" alt="wankeeper" style="width: 600px;"/>
</div>


[^spanner]: Corbett, James C., et al. "Spanner: Google’s globally distributed database." *ACM Transactions on Computer Systems (TOCS)* 31.3 (2013): 8.

[^zookeeper]: Hunt, Patrick, et al. "ZooKeeper: Wait-free Coordination for Internet-scale Systems." *USENIX annual technical conference.* Vol. 8. 2010.

[^zab]: Junqueira, Flavio P., Benjamin C. Reed, and Marco Serafini. "Zab: High-performance broadcast for primary-backup systems." *Dependable Systems & Networks (DSN), 2011 IEEE/IFIP 41st International Conference on.* IEEE, 2011.

[^paxos]: Van Renesse, Robbert, and Deniz Altinbuken. "Paxos made moderately complex." *ACM Computing Surveys (CSUR)* 47.3 (2015): 42.

[^raft]: Ongaro, Diego, and John K. Ousterhout. "In Search of an Understandable Consensus Algorithm." *USENIX Annual Technical Conference.* 2014.

[^gpaxos]: Lamport, Leslie. "Generalized consensus and Paxos." *Technical Report MSR-TR-2005-33,* Microsoft Research, 2005.

[^epaxos]: Moraru, Iulian, David G. Andersen, and Michael Kaminsky. "There is more consensus in egalitarian parliaments." *Proceedings of the Twenty-Fourth ACM Symposium on Operating Systems Principles.* ACM, 2013.
