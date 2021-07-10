---
title: "Talk: Kubernetes multi-container patterns"
date: 2021-03-31
categories: [talk]
tags: [kubernetes]
---

As a new Software Engineering Community Lead for APAC in JPMorgan, I was excited to organize our first major event for Q1 2021. It was a study group for the Certified Kubernetes Application Developer certification. Having passed the CKAD certification myself, I decided to get more involved - by becoming a co-host of the first cohort of this study group.

During one of the study group sessions, I gave a talk on multi-container patterns in Kubernetes. The following is a written version of this talk.

---

Today I'll be covering three multi-container patterns that are useful to develop reliable distributed systems. One of them is likely to be used by almost every production application as it has to do with logging.

But first, a recap on containers and pods - containers are the building blocks of distributed systems.
And the lowest atomic unit of abstraction in Kubernetes is a pod, which can contain one or more containers.

As an app dev developing on Kubernetes, there will be situations where you will have to decide whether to split your applications into containers in separate pods or have them split into containers in the same pod. In today's session, I will cover situations where we can use multiple containers in the same pod.

## Sidecar Pattern
The first and the most common pattern is the sidecar. In this pattern, there are two containers in a pod: the main container and the sidecar. The sidecar is a helper container to assist the primary container. The responsibility of shipping logs to an external component, like a centralized logging system can be offloaded to this sidecar container. The reason to do this is similar to the reason why side-effects can be offloaded to a separate thread from that of the main thread in a multi-threaded application: which is to let the primary thread (or container) focus on the main logic or purpose of the application.

![Sidecar Pattern](/assets/img/posts/k8s-multi-container-patterns/sidecar.png)

### Use cases
The common use cases for this pattern are logging (as mentioned above), file-syncing (in the case of a Content Management System, the main container will focus on responding to client requests while the sidecar will focus on syncing new changes from the CMS), and file watchers.

### How does it work?
Sidecars are possible because containers on the same pod can share a local disk volume. The same volume is mounted to both containers. The primary container would write the logs into files, which get stored in this shared volume. The sidecar container, running a fluent-bit, fluentd, or any other log aggregation technology, would pick up these log files and ship them to the destination, typically a centralized logging platform like Elasticsearch or Splunk.

### Benefits
By splitting the work between the two containers, we get a leaner, more focused primary container, failure isolation, and independent update cycles.

## Ambassador Pattern
This pattern involves an ambassador container, which acts as a proxy for communicating to and from the primary container. The primary container only needs to connect to this ambassador container while the ambassador controls proxying the connections to different environments/destinations. Ambassadors are possible because containers on the same machine share the same localhost network interface.

![Ambassador Pattern](/assets/img/posts/k8s-multi-container-patterns/ambassador.png)

### Use cases
- Commonly used for communicating with databases or cache systems: the primary container talks to the single ambassador which takes on the responsibility of connecting to different databases based on the environment.
- Client-side load balancing logic: the primary container only talks to the ambassador, which takes on the responsibility of connecting to different servers based on a heuristic or a round-robin pattern.

### How does it work?
Instead of configuring env vars in the primary container to control database connections, in this pattern, the application can be simplified to always connect to localhost, with the responsibility of connecting to the right database given to the ambassador.

### Benefits
- In production, the ambassador can implement logic to work with sharded databases as well, but the app in the primary container only needs to consider a single logical database, accessible over localhost
- Another benefit: during application development you can run a database on your local machine without requiring the ambassador, keeping the development experience simple
- The ambassador may also be used by multiple applications written in different languages since this responsibility is removed from the primary container

## Adapter pattern
Adapters present a standardized interface across multiple pods. This pattern is the opposite of the Ambassador pattern in that the Ambassador pattern presents a simplified view for the primary container whereas the Adapter pattern presents a simplified view of the application to the outside world.

![Adapter Pattern](/assets/img/posts/k8s-multi-container-patterns/adapter.png)

### Use cases
The pattern is commonly used for normalizing application logs, or monitoring data, so they can easily be consumed by a shared aggregation system. The adapter may communicate with the primary container using either a shared volume when dealing with files or over localhost.

### How does it work?
The pattern enables to adapt applications without requiring code changes to the primary container. This may be required when you don’t have access to an application’s source code or if it is a legacy application.

### What makes this possible?
The main container can communicate with the adapter through the shared network interface or the shared local volume.

## Final thoughts
For the CKAD exam, it is important to remember the names of the patterns. And practicing creating and mounting config maps via shared volumes will go a long way in helping you complete problems on this space quickly during the exam.