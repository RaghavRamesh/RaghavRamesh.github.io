---
title: "Notes on Grafana ObservabilityCON 2020"
date: 2020-11-29
categories: [notes]
tags: [review, conference]
---

There could not have been a more appropriate conference than Grafana's [ObservabilityCON 2020](https://grafana.com/about/events/observabilitycon/) given that I work on JPMorgan Chase & Co.'s private cloud Observability and given that we use Grafana extensively for visualizing our telemetry information. I've documented the highlights, the things I liked and the action items I'd take from attending this virtual conference.

## Highlights
- Grafana Labs released
  - [Tempo for Tracing](https://grafana.com/oss/tempo/).
  - [Loki 2.0](https://grafana.com/blog/2020/10/28/loki-2.0-released-transform-logs-as-youre-querying-them-and-set-up-alerts-within-loki/)
  - [Backfilling in Prometheus](https://grafana.com/blog/2020/09/02/how-were-improving-backfill-methods-to-get-older-data-into-prometheus/)
- "All you need is logs. With the new and improved LogQL, you can convert logs to metrics and create alerts via Grafana/cortextool which integrates with alertmanager" - while this was used to demo the newcapabilities of LogQL, I don't believe this is their recommendation for collecting metrics
- Grafana's correlation capabilities especially using the "Explore" tab
- You don't need Loki to make use of correlation. You can visualize ES logs in Grafana and already make use of correlation
- [ConProf](https://www.google.com/search?client=firefox-b-d&q=conprof+prometheus) - Prometheus for profiling - something for the PerfSec chapter (an internal cross-functional team that focuses on R&D works) to explore
- [Legend](https://github.com/grofers/legend) - a tool to automate dashboard creation - something for the PerfSec chapter to explore
- Consider Service mesh - it's worth the overhead (sidecars and other operations overhead)
- Advice for culture around Observability:
  - Conduct retros for incidents
  - Make creating dashboards/adding monitoring a part of the story to ensure Observability doesn't take a back seat
  - Spread the knowledge of Prom QL and log querying. That can be a bottleneck for developers
  - Contextualize dashboards. Any new dev/SRE looking at a dashboard should be able to understand what he/she is looking at

## My experience and what I liked
- Ability to ask questions to an audience of Observability domain experts. Qns I asked:
  - _Can someone point me to why Events are not considered one of the pillars of Observability unlike Metrics, Traces, and Logs?_
    - _From a data source perspective, I'd probably group events and logs together. Use log queries to extract event info from logs. I think you might find the Observability with logs and Grafana session to be interesting. It's on Wednesday at 16:00 UTC_
  - _Tips on improving Grafana dashboard performance? Like limiting data points/refresh rate_
    - _We have lazy loading which you can move some of those expensive queries/panels down below or in its own row(s). https://grafana.com/blog/2019/07/08/a-closer-look-at-lazy-loading-grafana-dashboards_
    - _Refresh rate certainly also helps along with the time range set by default. i.e. past 5-minute range with a low refresh should be fine when troubleshooting something live_
  - _LastPass observability architecture with Splunk and Service Mesh: Is service mesh worth the resource overhead in your experience?_
    - _Uh... yes, definitely worth the overhead. Not much overhead actually. Get extended metrics of the whole infra. You get a better overview of your system. You get access to some namespaces monitoring. Better operational experience._
  - _Always-enabled monitoring talk: Given monitoring tends to be an after-thought, do you have any tips to drive a change in this mindset within the team?_
    - _It's a cultural question._
    - _Have operational reviews. on-call reviews. get together, discuss incidents, discuss alerts you received, putting out rcas. this really helped._
    - _Have dashboards that show the performance of services. Try to maintain a level of transparency within the organization, this really helped. try building tools to avoid repeated work. Also, keep evolving dashboards._
- 1-to-1 session with Loki maintainers - Owen and Eldin (context: Our team was evaluating Grafana Loki to store Events). Questions I asked:
  - _Choosing labels seems to be a non-reversible action. Any advice on how to choose labels?_
    - _Use labels to specify "where the data is coming from" so env, resource provider, etc. are good. tenantid is not._
  - _No management API - We've found the need for copying over data one cluster goes out of sync_
    - _As part of Loki 2.0. you can actually use the same storage for index and chunks. So one less copying over to do. Our initial suspicion was true, we can indeed use the underlying storage's copying functionality. But he acknowledged that we are not the first people to ask this question_
  - _If we use multi-tenantcy for segregating events of different producers, can we still visualize events across service providers in a single Grafana panel?_
    - _Yes, we can use "mixed" to visualize data from multiple data sources in the same panel_
  - _Timestamp ordering led to rejecting events..._
    - _soon we don't have to worry about timestamp ordering, it's going away. they were using that and rejecting events because it was based on cortex which is strictly time order specific. Anyway given that we will use the kconnect location as a label it shouldn't matter_
  - High-availability Loki deployment recommendation
    - _Running in microservice mode with the "ring" module for coordination (backed by etcd, consul, or memberlist) along with a configurable replication factor (we use 3) Then you can scale out various components into fleets of i.e., distributors, ingesters, queriers, etc._
- Recommended talks:
  - [Always-enabled Monitoring with Loki, Prometheus, and Grafana by Vaibhav Krishna](https://grafana.com/go/observabilitycon/always-enabled-monitoring-with-loki-prometheus-and-grafana/)
  - [Observability with logs and Grafana](https://grafana.com/go/observabilitycon/observability-with-logs-grafana/) - especially the alerting bit because we are currently working on a similar product.
- I came out feeling inspired to explore new tools, technologies - can't describe in words
- Even though it's recorded I may never have actually sat and watched the talks on a later date with so many things competing for attention I missed the physical conf attending aspect. Although I think I felt more empowered to ask questions because of the easy access to experts (the best person could answer as opposed to asking someone in-person who may not be an expert) and visibility for my questions
- I also discovered that the plural of Prometheus is Prometheis :D

## Action Items
- PerfSec chapter ideas listed above
  - Evaluate Tempo
  - Evaluate ConProf
  - Evaluate github.com/grofers/legend
- Take another look at Loki for Events given some of the drawbacks are addressed
- Conduct incident retros
