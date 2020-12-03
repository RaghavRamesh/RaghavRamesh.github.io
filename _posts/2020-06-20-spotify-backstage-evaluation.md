---
title: "Spotify Backstage Evaluation"
date: 2020-06-20
categories: [architecture]
tags: [spotify, backstage, architecture, review, product]
---

### Disclaimers
* This evaluation was conducted in mid-2020 so there might be changes to the Backstage product since then, so keep this in mind while reading this evaluation.
* For regulatory reasons, this is a stripped down version of the original evaluation I published within JPMorgan Chase & Co.. So it will not contain the introduction which contains the context of this evaluation and the conclusion which contains what steps we took based on this evaluation. Nevertheless, I believe the rest of the content is still sufficiently meaningful, so I've extracted those bits for this post. Also, the content published below does not reflect the views or opinions of JPMorgan Chase & Co. in any way. They are purely my own.

## Product Evaluation
### Overview
[Backstage](https://backstage.io/) is an open platform for building developer portals. It's based on the developer portal Spotify has been using internally for over four years. Backstage can be as simple as a services catalog or as powerful as the UX layer for your entire tech infrastructure.
#### Features
* Software/Service catalog: Create and manage all of your organization's software and microservices in one place.
  * Software/Service detail (my guess for this feature's name): A unified method for managing microservices offers both visibility and control.
* Software component bootstrapper: Preset templates allow engineers to quickly create microservices in a standardized way (coming soon)
* Docs-like-code: Centralized, full-featured technical documentation with integrated tooling that makes it easy for developers to set up, publish, and maintain alongside their code (coming soon) (one other problem our firm faces which Backstage includes as a feature of the overall developer portal)

#### Benefits
* For engineering managers, it allows you to maintain standards and best practices across the organization and can help you manage your whole tech ecosystem, from migrations to test certification.
* For end-users (developers), it makes it fast and simple to build software components in a standardized way, and it provides a central place to manage all projects and documentation.
* For platform engineers, it enables extensibility and scalability by letting you easily integrate new tools and services (via plugins), as well as extending the functionality of existing ones.
* For everyone, it's a single, consistent experience that ties all your infrastructure tooling, resources, standards, owners, contributors, and administrators together in one place. More details can be found [here](https://github.com/spotify/backstage#what-is-backstage).
* _[Source](https://github.com/spotify/backstage/)_

#### Backstage Adopters
* [Adopters](https://github.com/spotify/backstage/blob/master/ADOPTERS.md)

### Software Catalog and Software Detail
* "The service catalog - or software catalog - is a centralized system that keeps track of ownership and metadata about all software in your ecosystem. The catalog is built around the concept of metadata yaml files (syntax very similar to k8s resource manifests) stored together with the code, which is then harvested and visualized in Backstage." - _[Source](https://backstage.io/blog/2020/06/22/backstage-service-catalog-alpha)_
* It's currently not easy (not possible?) to find out who owns what software within the firm. It's also not easy to quickly identify the software that I own. A control plane that indicates parts of the software that requires attention like missing AppFit requirements, build failures, monitoring alerts, will be quite helpful.
* Software Detail is a detailed view of each software/service/component (could be titled something else by Spotify Backstage as it is yet to be released). It gives an overview of the component and the different infrastructure/tools your component integrates with - e.g., CI/CD, security scan tool, code quality tool, code review tool, monitoring, etc.

### Workflows
* Workflows / Software component bootstrapper: Preset templates allow engineers to quickly create microservices and other software in a standardized way. AWS Amplify is a good working example.
* Currently, Moneta Kickstart does a great job of providing starters for Java microservice and full-stack projects. This Spotify Backstage feature takes it much further by adding support to more languages and most importantly, integrating with other infrastructures like pre-baked CI/CD pipeline, code scanning, and a suite of other necessary integrations that a team that develops the software will have to implement manually.
* Yet to be open-sourced/implemented. It can be evaluated once it's released.

### Docs-like-code
* Centralized, full-featured technical documentation with integrated tooling that makes it easy for developers to set up, publish, and maintain alongside their code.
* It's useful for our firm as it improves the visibility of software within the firm and encourages reuse which will improve developer productivity. Yet to be open-sourced/implemented. It can be evaluated once it's released.

## Architecture Evaluation
### Single Page Application + Plugins
* Backstage is a single-page application composed of a set of plugins.
* Plugin architecture in monorepo scales well - easy to independently work on, own plugins as a team, and deploy them
* Next, the core and the plugins live in the same mono repo. Backstage uses lerna that provides the necessary toolkit for working with monorepos - like independent bundling as well as when the plugin is ready to deploy, it will bundle it up with the rest of the plugins and the core and produce a single or a few artifacts that make up the entire front-end
* Modern, popular, highly regarded tech stack - React, yarn + Lerna for monorepo workspaces, Jest for tests, TypeScript for type safety, Prettier for code formatting, ESLint for linting, Rollup for build tool (this one I'd say is under assessment by the community - webpack is the default build tool)
* Out-of-the-box the repo is already large enough to feel slow during development. This is due to the tendency of front-end projects to already have many node packages in a project - a monorepo with multiple "mini" apps makes it worse. However, this can be alleviated by selectively building the plugin the team is responsible for/the team is currently working on.

#### Concerns
* Apart from the plugin architecture itself, one salient feature of Backstage is the reusable UX patterns exposed in the form of reusable components. However, if we were to implement it for our firm, we may have to re-implement them using our (JPMC's) design systems which has a big opportunity cost.
* In general, similar to microservices, sharing code between plugins is an anti-pattern. Currently, Backstage encourages reusing UI components.
  * We should be careful to ensure that the shared components contain only UI logic and no business or domain logic. When domain logic is put into a shared library it creates a high degree of coupling across applications and increases the difficulty of change. So, for example, we should usually not try to share a *ProductTable, which would contain all sorts of assumptions about what exactly a "product" is and how it should behave. Such domain modeling and business logic belong in the application code of the micro frontends, rather than in a shared library.
* Other downsides of micro frontends, in general, can be found [here](https://martinfowler.com/articles/micro-frontends.html#Downsides).

### GraphQL-based API Gateway
* Backstage implements an API Gateway using GraphQL - the microservice pattern where a separate service is built to sit in front of your other back-end services. This service acts as a back-end for front-ends, where it can proxy and unify access to a variety of back-end services. This pattern can allow for varying use-cases, but typically this is done to unify client network access, centralized authentication, and combine data from multiple services' data stores.
* [Netflix](https://thenewstack.io/why-netflix-rolled-its-own-node-js-functions-as-a-service-runtime), [Soundcloud](https://samnewman.io/patterns/architectural/bff/), GitHub faced similar issues where different clients had different needs which gave birth to the "Back-ends for Front-ends" pattern. Around this time Facebook invented GraphQL to tackle this problem.
* This technique is in the Trial stage (one stage before Adopt, a.k.a., worth pursuing) in the [ThoughtWorks Radar](https://www.thoughtworks.com/radar/techniques/bff-backend-for-frontends).
* Does having a single GraphQL server scale well? Yes, it's the recommended approach. Using [Apollo Federation](https://www.apollographql.com/docs/federation/), (expose one graph for all of our organization's data without experiencing the pitfalls of a monolith) each plugin provider will implement only the part of the data graph that they should be responsible for. All of Backstage's plugins' required data can be implemented as a single large graph and this will allow the front-end to query from a single server for whatever it needs without worrying about where the data is coming from.
* While the architecture recommends the above, it also allows the front-end to invoke REST APIs. There is a proxy through which the front-end can obtain its necessary data whether GraphQL or otherwise.
* Scaling: The federation/microservices architecture allows the back-end components to be independently built, deployed, tested, etc.
* Backend and Proxy in the above diagram are not yet open-sourced (As of 29th June 2020). So we don't know if their implementation will use Apollo Federation or any other pattern like schema stitching.

#### Concerns
* There's a tendency for API Gateways to turn overambitious. So there's a need to closely guard against this especially when the single large schema will be updated by many contributors. [More details here](https://www.thoughtworks.com/radar/platforms/overambitious-api-gateways).
* Github, Facebook, Shopify, etc., have spoken about receiving plenty of PRs in a day (more than 20). This could lead to a lot of work for the schema maintainers and runs the risk of accidentally accepting poorly designed changes
* Having an API gateway implies there isn't complete decentralization and will involve some amount of orchestration.
* Performance degradation is a concern due to the multitude of scenarios that the API Gateway will handle and can impact the speed and reliability of your application. Also, having an additional layer adds to the latency.

### GraphQL benefits in general
* No under or over-fetching of data: the front-end gets exactly what it asks for no-more, no-less without needing to call multiple endpoints and dealing with unnecessary data.
* Declarative schema - easy to grok the response shape
* Type system which has proved essential to work with agreed response shapes
* Encourages domain-driven-design by modeling an interface to the domain, as opposed to just being a thin layer in front of the service
* Version-less => backward compatibility
* Centralized Authentication. Backstage uses Passport which integrates with [several auth models](https://backstage.io/blog/2020/07/01/how-to-enable-authentication-in-backstage-using-passport).

#### Concerns
* GraphQL is new (released in 2015). _Source: [The Case Against GraphQL - Robert Zhu](https://www.youtube.com/watch?v=djKPtyXhaNE)_
* Cost of learning and getting buy-ins
* Needs time to mature - JS community is mature, but community support is not yet great for other languages
* Control - in GraphQL, unlike REST, the server no longer has full control so there is a possibility of a rogue client making non-performant queries (can be avoided using persisted queries or depth limiting)
* Centralization
  * Naming congestion due to a single large graph/schema
  * vs. Federation. Centralization (having one giant schema for the entire GT Portal) against a very large codebase may not be practically possible. In Facebook, the largest GraphQL schema had over 10000 types, which resulted in weird type names like NewsFeedDepth4
* Graphs vs Resources - many features in REST not available in GraphQL
* ORMs are optimized for REST currently which means they can avoid issues like the N+1 issue • Like other proxies, it presents another point of failure as there is an additional another layer

## Evaluation FAQs
### Why Backstage? Why can't I build my developer portal?
If it kicks off, in my view, the true value of Spotify Backstage is from the potentially rich source of plugins built by the community. We can choose to be an early adopter and use the power of open-source to learn along with this community outside the firm and if possible contribute back. At this point, I view it as a starter kit we can use to get a head start.
### Why GraphQL for API Gateway, why not REST?
"In short, it's a ready-made ecosystem and a perfect fit for this functionality. If you were to build an API Gateway over REST from scratch, you would have to establish new shapes and conventions for a new interface. GraphQL provides the tools to quickly build these with typing, mocking, documentation, and playground functionality for free. Apollo Server and graphql-tools make resolving this data often as simple as implementing a single function to fetch and optionally reshape the responses. We've found these tools to provide an excellent developer experience, especially with the huge improvement in API discoverability and documentation with GraphQL Playground out of the box." - https://engineering.kapost.com/2019/03/implementing-an-api-gateway-withgraphql-resolution-strategies/
