---
layout: post
title: "Raft Consensus Algorithm"
date: 2014-10-11 10:19:53
categories: study-notes algorithms
published: true
---

## Overview

A consensus algorithm is a method to allow a distributed system to agree on a data value,
even when some members of the system are unavailable. Unlike existing algorithms, Raft was
designed with the primary goal of being understandable, both for students and implementers.

Raft is a leader-based algorithm, meaning all changes to the system are initiated by the leader,
which is elected by the members of the system. Each member maintains an election timer, which is
always counting down. Periodically, the leader sends a heartbeat to the rest of the members,
known as followers, which resets their election timers. If at any point, a follower's election
timer expires, the follower initiates a new election, naming itself as a candidate and voting for
itself. Each member casts a vote (electing the candidate whose election request was received first)
and whichever candidate receives the majority of the votes is elected new leader.

When a client needs to change a data value, the change request is sent to the leader (if the client
contacts a follower, it is redirected to the leader). The leader adds the change to its log and sends
it to each of its followers. When more than half of the followers have added the change to their logs,
the change is considered committed. It should be noted that a candidate may not be elected leader unless
its log contains all committed entries, guaranteeing consistency across the system.

## Applications

Raft is used in a variety of distributed systems, including [CoreOS's etcd](https://github.com/coreos/etcd)
and [Hashicorp's Consul](http://www.consul.io/).
