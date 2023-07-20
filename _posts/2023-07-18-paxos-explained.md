---
layout: post
title:  Paxos explained
date: 2023-07-18 18:07:00-0400
description: description of one of the most famous algorithm for reaching consensus in distributed systems in presence of faults
tags: distributed-systems consistency consensus
categories: distributed-systems
giscus_comments: true
related_posts: false
bibliography: 2023-07-18-paxos-explained.bib
toc:
  sidebar: left
---


## Introduction

```markdown
> A distributed system is one in which the failure of a computer you didn't even know existed can render your own computer unusable. - Leslie Lamport
```

Paxos is one of the most famous algorithms for solving consensus issues in a distributed system, in the presence of failures. Consensus in a distributed system is a problem in which a set of entities, communicating with each other, must eventually agree upon a chosen value. This problem has significant relevance in a distributed system due to the challenges of reaching total agreement on operations to execute. This is fundamental in order to guarantee consistency, which can be oversimplified as having the same data on different nodes at some point in time. Reaching consensus in a distributed system is hard because of failures. That's why we start this introduction to Paxos by briefly describing failures.

## Failure Model

Failures are not uncommon in computer networks, where latency is unpredictable and nodes may fail. Imagine you send a message over a computer network. Depending on the traffic over the network, you may find that your packet may be delayed due to network congestion or never reach its destination because nodes along the way have failed. 

To precisely describe Paxos, it's necessary to define the types of failures that Paxos tolerates. A range of failure models has been proposed within the theory of distributed systems to account for various situations where a system may malfunction due to different types of failures.

For simplicity's sake, let's introduce the <bold>fail-stop model</bold>. This model assumes that nodes can crash during communication with other nodes but they will recover from the failure. Once a node has recovered from a failure, it resumes proper functioning. Now consider a situation where a node executes an operation and then fails. What happens after it recovers from the failure? There could be two scenarios:

i) The node retains some memory of what happened before it crashed.

ii) The node doesn't remember anything that occurred prior to the failure.

In Paxos, we assume that a node has some memory of events that took place before a failure. For ease of understanding, you can think of this memory as a log. Whenever a node executes an operation, it records the operation it's about to execute in a **log** file. Upon recovery from a failure, a node can read what it executed prior to the failure to resume its operations.

As you can imagine failures described above make the problem of reaching consesuns hard in a system were failures are not uncommon. Paxos algorithm try to solve the consesus problem with an elegant solution.

For the clarity of the description, in the next sections we refers to processes as a set of entites communicating over a network trying to reach consensus on a value.

## Consensus Problem
Assume a set of processes are communicating over a network, trying to reach consensus. A process may fail or messages could be delayed due to network congestion. How can we properly define consensus given these constraints? In Paxos, there are two fundamental properties named: *safety* and *liveness*. 

Safety is a correctness property that makes assumptions about the algorithm's behaviour. In particular, for Paxos, we make the following <d-cite key="lamport2001paxos">assumption</d-cite>:

- Only a single value is chosen

This property states that we cannot have a situation in which, given a set of nodes `$$n$$`, half of them choose a value `$$v_1$$` while the remaining nodes converge on choosing a value `$$v_2$$` such that `$$v_1 != v_2$$`. This property is fundamental to avoid what is called a split-brain scenario. In a split-brain situation, we may have a network partition that does not allow nodes to talk to each other. In this situation, one half of the partition may choose a value while the other half chooses another one.

