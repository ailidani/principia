---
title: "What Paxos sounds like"
date: "2016-09-05"
image: "sonification.jpg"
type: "blog"
draft: false
tags: ["paxos"]
---

> Sonification is the use of non-speech audio to convey information or perceptualize data. Auditory perception has advantages in temporal, spatial, amplitude, and frequency resolution that open possibilities as an alternative or complement to visualization techniques.  -- [Wikipedia](https://en.wikipedia.org/wiki/Sonification)

Following simple rules, to assign different audio frequencies to meaningful events or state changes in a computer program, we were able to clearly hear the "characteristic" and pattern that distinguish them, like the sorting algorithms in [this video](https://www.youtube.com/watch?v=kPRA0W1kECg).


What about a distributed algorithm? When multiple processes joined to play the sound, will it become an orchestra? In [this fun idea](http://muratbuffalo.blogspot.com/2016/09/sonification-for-monitoring-and.html), we tried to make Paxos implementation from [this paper](http://www.cs.cornell.edu/home/rvr/Paxos/) perform.

With 5 Acceptors, 2 Replicas and 2 Leaders, each process will play a different MIDI instruments, and different event in the Paxos system is assigned with different note. To make it sounds better, we delay every event for 150 milliseconds, so that each note is played 150ms.

<audio controls>
<source type="audio/mpeg" src="paxos_150ms_delay.mp3">
Your browser does not support the audio element.
</audio>

Since there is no deterministic execution in any distributed systems (uncertain arrival of messages), the sound in realtime is different every time we run the program, but the pattern remains. Our experimental program can be downloaded [here](https://github.com/ailidani/sonification), which can be run in terminal in following commands:

```
java -jar paxos-sonification.jar
```
