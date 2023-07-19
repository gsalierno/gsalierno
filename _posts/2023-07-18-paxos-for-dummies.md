---
layout: post
title:  Paxos for dummies
date: 2023-07-18 18:07:00-0400
description: description of one of the most famous algorithm for reaching consensus in distributed systems in presence of faults
tags: distributed-systems consistency consensus
categories: distributed-systems
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

## Introduction
```markdown
> A distributed system is one in which the failure of a computer you didn't even know existed can render your own computer unusable - Leslie Lamport
```

Paxos is one of the most renowned algorithms for resolving consensus issues in a distributed system, particularly in the presence of failures. Failures are not uncommon in computer networks, where latency is unpredictable and nodes may fail. To precisely describe Paxos, it's necessary to define the types of failures that Paxos can tolerate. A range of failure models has been proposed within the theory of distributed systems to account for various situations where a system may malfunction due to different types of failures.

For simplicity's sake, let's introduce the <bold>fail-stop model</bold>. This model assumes that nodes can crash during communication with other nodes and then restart. Once a node has recovered from a failure, it resumes proper function. Now consider a situation where a node executes an operation and then fails. What happens after it recovers from the failure? There could be two scenarios: i) the node retains some memory of what happened before it crashed, or ii) the node doesn't recall anything that occurred prior to the failure. In Paxos, we assume that a node has some memory of events that took place before a failure. For ease of understanding, you can think of this memory as a log. Whenever a node executes an operation, it records the operation it's about to execute in a <italic>log</italic> file. Upon recovery from a failure, a node can read what it executed prior to the failure to resume its operations.