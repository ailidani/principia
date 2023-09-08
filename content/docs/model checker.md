---
title: "Model Checker in Go"
date: 2021-08-23
draft: true
---

## Temporal logic

More than 2000 years ago, Greek philocersor Aristotle, as the first man ever systematically studied _logic_, has already raised concers about how time (futures in particular) plays a complex role in logical reasoning in his book "On Interpretation". Since then, very little was developed for millennia. Until 1947, a formalization of temporal functions was created by Polish mathematician Jerzy Los. Ever since 1950s, a lot of theoretical works has been developed in this area. Eventually, temporal logic has find its biggest application in computer science as formal verification, where it is used to describe the system requirements and a checker can verify against some model.

There are two major logics in formal verifications, Linear Temporal Logic (LTL) and Computation Tree Logic (CTL).

CTL models are defined as transition system $M = (S, \to, L)$ where $S$ is the total set of states, $\to \subseteq S \times S$ is the finite set of state transitions, and $L$ is the labeling function for states. Under the definition of $M$, a model starts from initial state $s_0 \in S$, and generates all possible states by iteratively applying the well defined state transitions. Such model is the minimal abstraction needed to describe any algorithm, and a _model checker_ can verify any temporal properties on state while exploring the state space, as a result checking the correctness of the algorithm.

Imaging a simple model contains two possible actions, $a_0$ and $a_1$ on any state. Ideally, the two actions will generate a full binary tree of states as shown in Figure 1, thus the name computation tree. But in general, the state graph might be any directed graph that is connected. Some times, an action may have no effect, i.e. the next state is equal to previous state. In this case, we ignore the action to avoid any self-loops.

{{< mermaid >}}
%%{init: {'theme':'dark'}}%%
stateDiagram-v2
s0 --> s1 : a0
s0 --> s2 : a1
s1 --> s3 : a0
s1 --> s4 : a1
s2 --> s5 : a0
s2 --> s6 : a1
{{< /mermaid >}}

## CTL
State Formula $\Phi$ and Path Formula $\phi$
$$\Phi := true | (\neg \Phi) | (\Phi_1 \land \Phi_2) | (\Phi_1 \lor \Phi_2) | (\Phi \implies \Phi) | (\Phi \iff \Phi) | \exists \phi | \forall \phi$$
$$\phi := N\Phi | (\Phi_1 U \Phi_2) | \square \Phi | \diamond \Phi$$

```go
// State Formula
type Formula func(State) bool

func True() Formula {
    return func(State) bool { return true }
}

func False() Formula {
    return func(State) bool { return false }
}

func Not(f Formula) Formula {
    return func(s State) bool {
        return !f(s)
    }
}

func Imply(f, g Formula) Formula {
    return func(s State) bool {
        return !f(s) || g(s)
    }
}
```

```go
// Path Formula
type PathFormula func(...State) bool

func Always(f Formula) PathFormula {
	return func(path ...State) bool {
		for _, s := range path {
			if !f(s) {
				return false
			}
		}
		return true
	}
}

func Eventually(f Formula) PathFormula {
	return func(path ...State) bool {
		for _, s := range path {
			if f(s) {
				return true
			}
		}
		return false
	}
}
```

## CTL*

$$\Phi := true | (\neg \Phi) | (\Phi_1 \land \Phi_2) | (\Phi_1 \lor \Phi_2) | (\Phi \implies \Phi) | (\Phi \iff \Phi) | \exists \phi | \forall \phi$$
$$\phi := \Phi | (\neg\phi) | (\phi_1 \land \phi_2) | (\phi_1 \lor \phi_2) | (\phi \implies \phi) | (\phi \iff \phi) | N\Phi | (\Phi_1 U \Phi_2) | \square \Phi | \diamond \Phi$$

$\pi \models \Phi \iff s_0 \models \Phi$

$s \models \neg \Phi \iff s \not\models \Phi$
$\pi \models \neg \phi \iff \pi \not\models \phi$

```go
func Not(f Formula) Formula {
    return func(s ...State) bool {
        return !f(s...)
    }
}
```


$s \models \Phi_1 \implies \Phi_2 \iff s \not\models \Phi_1 \lor s \models \Phi_2$
$\pi \models \phi_1 \implies \phi_2 \iff \pi \not\models \phi_1 \lor \pi \models \phi_2$

```go
func Imply(f, g Formula) Formula {
    return func(s ...State) bool {
        return !f(s...) || g(s...)
    }
}
```

$\pi \models \phi_1 U \phi_2 \iff \exists n \geq 0 : \pi[n] \models \phi_2 \land \forall 0 \leq k < n : \pi[k] \models \phi_1$

```go
func Until(f, g Formula) Formula {
    return func(path ...State) bool {
        n := -1
        for i, s := range path {
            if g(s) {
                n = i
                break
            }
        }
        if n < 0 {
            return false
        }
        for i := 0; i < n; i++ {
            if !f(path[i]) {
                return false
            }
        }
        return true
    }
}
```

{{< mermaid >}}
%%{init: {'theme':'dark'}}%%
classDiagram

class State {
    <<interface>>
    String() string
}

class Actor {
    ID() string
    Address() string
    GetMembers() []string
    Send(any, string) error
    Multicast(any, ...string) error
    Receive() (*Message, error)
}

class Model {
    init State
    actions map[string]Action
    xactions map[string]XAction
    constraints map[string]StateConstraint
    formulas map[string]Formula
    graph *Graph
    queue

    ID() uint64
}
{{< /mermaid >}}
