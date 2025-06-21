---
title: "TLA+ wiki"
date: "2015-11-25"
draft: false
# tags: ["tla+"]
---

### Modules
* Integers
* Naturals
* Reals
* [Sequences](https://github.com/tlaplus/tlaplus/blob/master/org.lamport.tla.toolbox/StandardModules/Sequences.tla)
* [TLC](https://github.com/tlaplus/tlaplus/blob/master/org.lamport.tla.toolbox/StandardModules/TLC.tla) -- print
* [Bags](https://github.com/tlaplus/tlaplus/blob/master/org.lamport.tla.toolbox/StandardModules/Bags.tla) are multi-set functions, each element points to an integer as its quntity. Defines operators: (+) (-)
* [FiniteSets](https://github.com/tlaplus/tlaplus/blob/master/org.lamport.tla.toolbox/StandardModules/FiniteSets.tla) defines `IsFiniteSet(S)` and `Cardinality(S)`.
* RealTime -- RTBound RTnow now


### Definition
```
<A>_v == A /\ (v' # v)
```
$$\langle A \rangle_v == A \land (v' \neq v)$$
```
[A]_v == A \/ (v' = v)
```
$$[A]_v == A \lor (v'=v)$$


### Operators

Divides (p, n) == $\exists q \in Int : n = q * p$

DivisorsOf (n) == {$p \in Int$ : Divides(p, n)}

SetMax (S) == CHOOSE $i \in S$ : $\forall j \in S$ : $i \geq j$

GCD (m, n) == SetMax(DivisorsOf(m) $\cap$ DivisorsOf(n))

SetGCD(T) == SetMax( {$d \in Int$ : $\forall t \in T$ : Divides(d, t)} )

max(x, y) == IF x>y THEN x ELSE y

min(x, y) == IF x<y THEN x ELSE y

maxv(x, y) == [$i \in$ DOMAIN x |-> max(x[i], y[i])]

```
Cardinality(set)
SortSeq(s, <)
Len(s)
Head(s)
Tail(s)
Append(s,e)
Seq(s)
SubSeq(s, m, n)
SelectSeq(s, op)
```

successor[$i \in Nat$] == i+1

successor == [$i \in Nat$ |-> i+1]

factorial[$n \in Nat$] == IF n = 0 THEN 1 ELSE n * factorial[n-1]

RECURSIVE FactorialOp(_)

FactorialOp(n) == IF n=0 THEN 1 ELSE n * Factorial(n-1)

RECURSIVE Cardinality(_)

Cardinality(S) == IF S={} THEN 0 ELSE 1+Cardinality(S \ {CHOOSE $x \in S$ : TRUE})

------

Sortings(S) == LET D == 1..Cardinality(S)

IN {$seq \in [D \to S] : $

$\land S \subseteq \{seq[i] : i \in D\}$

$\land \forall i,j \in D : (i<j) \Rightarrow (seq[i].key \leq seq[j].key)$}

------

RECURSIVE SetSum(_)

SetSum(S) == IF S={} THEN 0 ELSE LET s==CHOOSE $x \in S$ : TRUE IN s + SetSum(S \ {s})

------

RECURSIVE SeqSum(_)

SeqSum(s) == IF s=<<>> THEN 0 ELSE Head(s) + SeqSum(Tail(s))

### Network

variable network = [from $\in 1..N$ |-> [to $\in 1..N$ |-> <<>>]];

```
define
{
    send(from, to, msg) == [network EXCEPT ![from][to] = Append(@, msg)]
    bcast(from, msg) == [network EXCEPT ![from] = [to $\in 1..N$ |-> Append(network[from][to], msg)]]
}
```

```
macro rcv()
{
    with (from $\in$ {$j \in 1..N$ : Len(network[j][self]) > 0}) {
        msg := Head(network[from][self]);
        network[from][self] := Tail(@)
    };
}
```