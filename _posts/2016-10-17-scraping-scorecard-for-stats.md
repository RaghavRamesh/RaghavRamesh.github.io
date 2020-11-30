---
title: "Scraping scorecard for partnership stats"
date: 2016-10-17
categories: [side-project]
tags: [javascript, nodejs, cricket]
---

What better way to combine my passions - cricket and software engineering than to build a software side project that solves a cricketing pain point. I play for a couple of cricket clubs in Singapore - Challengers United CC, Redbacks CC and my university (NUS) cricket team across a few tournaments. One of the tournaments has a website to host their match scorecards which we use to collect our team stats that we review on a yearly basis to measure our team performance.

Unfortunately, the website doesn't have a key performance metric - [Partnerships](https://en.wikipedia.org/wiki/Partnership_(cricket)) but all the necessary data to derive this stat was available in the scorecard itself. So I built a tool -
[Scorecard-Scraper](https://github.com/RaghavRamesh/Scorecard-Scraper). It is a NodeJS script that scraps for stats from a scorecard and derives the partnership stats for a particular cricket match.
