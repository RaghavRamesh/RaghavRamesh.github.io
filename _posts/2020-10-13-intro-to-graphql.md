---
title: "Talk: Intro to GraphQL"
date: 2020-10-13
categories: [talk]
tags: [graphql]
---


I recently gave an intro talk on GraphQL after performing a spike for one of our products at JPMorgan. The
following's the script I used. I've stripped out some parts for regulatory reasons. For the same reason,
I also can't obtain the Powerpoint presentation file. But the generic bits are still useful for those new to GraphQL.

---

Today I’m going to be talking about GraphQL, how it’s different from REST, what are some of its benefits and limitations. This is more of an intro talk, I won’t go too much in deep because the content is based on a 1-week spike on _Product name_ API. But I believe it will be useful for those who are new to GraphQL.

## What GraphQL is not?
- It’s not a database (like a Graph db).
- It’s not a library or a framework by itself (you can’t install GraphQL).

## What is GraphQL?
- Query language for your API - allows you to query deeply nested associations
- Specification for servers to execute the query

## Why was it created?
- In 2012, Facebook started the project to overcome data fetching issues in their native mobile platform.
- Built with the goal of being a more flexible and efficient alternative to REST.

## Why GraphQL?
- Provides clients the power to ask for exactly what they need and nothing more.
- GraphQL APIs get all the data your app needs in a single request
- Language agnostic - plenty of client and server libraries are available. There are implementations of GraphQL like Apollo, GraphQL Java

![getting started](/assets/img/posts/intro-to-graphql/why-graphql.png)

## Comparing to REST
Let's take an example of a shopping cart and the data needed to populate the following UI
- ![cart ui](/assets/img/posts/intro-to-graphql/cart-ui.png)

What kind of data do we need? And how would you build a REST API for this?
- Cart resource - list of products, cart value
- Product resource - title, description, price, product image URL, bunch of other things
- If you’re truly REST, you’ll have hyperlinks to other resources which will make your API discoverable

RESTful queries

```
GET /carts/<id>
GET /products/<id> // x3
GET /product_images/<id> // x3
```
- That’s 7 round trips!

Can we make it a single request?
- I can use a query param, expand my associations
  - `/carts/<id>?expand=products`
  - Problem with this approach: stuck with the entire product resource even though you only need title, desc, price
- Even more clever, specify which fields you want
  - `/carts/<id>?fields=products(name, description, price)`
  - Problem with this approach: that’s only 1 level deep, what if you had to go a level deeper

The GraphQL query equivalent is as follows
- ![cart query](/assets/img/posts/intro-to-graphql/cart-schema.png)

The following is how the schema corresponding to the objects in this UI would look like:
- ![cart schema](/assets/img/posts/intro-to-graphql/demo-schema.png)

References: [GraphQL Schema Design by Giroux](https://www.youtube.com/watch?v=pJamhW2xPYw)

## Core concepts
- Scalar Types
	- Boolean
	- Number
	- String
	- ID
	- Query, Mutation
- Custom Types
- Enums


## Demo
- Domain: _stripped out_
- Entry point: _stripped out_
- Graph - edges, nodes: _stripped out_
- Types: _stripped out_
- Playground
	- It’s a tool built by the GraphQL community which allows you to not only execute queries against the API, it also provides a documentation of the schema
	- As a developer it’s useful to provide pre-configured queries with placeholders or query variables that you can ask your customers to replace and then just press the play button
	- Let’s explore the Rule Type
	- Entry point
	- Explain non-nullable
- Code: _stripped out_
- Authn/Authz: _stripped out_

## Gotchas
- InputUnion type is missing: [Github Issue](https://github.com/graphql/graphql-spec/issues/488)
[Output object type Union is present](https://www.apollographql.com/docs/apollo-server/schema/unions-interfaces/) but Input Union type is still in RFC. So until it's available, all checks must be performed server-side.
E.g., Applies in the case where a user should specify only either type A or type B but not both while performing a mutation query. While it's possible to verify this while querying due to the presence of Union type it's not possible to enforce this in mutations.
- Dynamic key value pairs should be avoided so that we get the benefit of static type checking enforced by GraphQL. For e.g., in the case of labels, we can use a Label type which has a `key` and `value` as keys. And it will look like the following: [https://stackoverflow.com/a/46563788](https://stackoverflow.com/a/46563788)
- GraphQL Playground, unlike GraphiQL doesn't allow queries to be populated with URL query params. The next best alternative is to set default queries via tabs.
  - [Reference](https://spectrum.chat/apollo/apollo-tooling/allowing-playground-to-receive-querystring-params--f9bcf02b-cafd-4b54-9b9aa6e166241eee)
  - Playground is the more recent and strategic IDE solution promoted by Apollo, and generally has more features than GraphiQL (the above is an exception).
  - Also, Playground and GraphiQL [are joining forces](https://foundation.graphql.org/news/2020/04/03/web-based-graphql-ides-for-the-win-how-why-playground-graphiql-are-joining-forces/)
  - They seem to be working on [GraphQL Explorer](https://www.apollographql.com/blog/introducing-the-apollo-explorer/) which appears to be waay more comprehensive than Playground and GraphiQL but seems to require an Apollo account.
- Playground in production
  - Enabling introspection in production is not considered a best practice for security reasons as it exposes the schema and allows attackers to execute malicious queries. [Reference](https://spectrum.chat/apollo/apollo-server/graphql-playground-disabled-as-a-production-best-practice-but-why-93d18b63-7dfc-44d3-85a2D967954d1efa)

## Limitations
- GraphQL is new (released in 2015). _Source: [The Case Against GraphQL - Robert Zhu](https://www.youtube.com/watch?v=djKPtyXhaNE)_
- Cost of learning and getting buy-ins
- Needs time to mature - JS community is mature, but community support is not yet great for other languages
- Control - in GraphQL, unlike REST, the server no longer has full control so there is a possibility of a rogue client making non-performant queries (can be avoided using persisted queries or depth limiting)
- Centralization
  - Naming congestion due to a single large graph/schema
  - vs. Federation. Centralization (having one giant schema for the entire GT Portal) against a very large codebase may not be practically possible. In Facebook, the largest GraphQL schema had over 10000 types, which resulted in weird type names like NewsFeedDepth4
- Graphs vs Resources - many features in REST not available in GraphQL
- ORMs are optimized for REST currently which means they can avoid issues like the N+1 issue • Like other proxies, it presents another point of failure as there is an additional another layer

## Tooling (for learning)
- I completed the [GraphQL: The Big Picture Pluralsight course](https://app.pluralsight.com/library/courses/graphql-big-picture/table-of-contents)
  by Adhithi Ravichandran. You can check out [my notes from that course](https://raghavramesh.github.io/mooc-notes/Backend/GraphQL/GraphQL.html) to get an overview.
- Many public playgrounds to get a feel for querying
  - [Github API Explorer](https://docs.github.com/en/free-pro-team@latest/graphql/overview/explorer)
  - [Star Wars API](https://github.com/graphql/swapi-graphql)

## Final thoughts
- Cool documentary by HoneyPot on [YouTube](https://www.youtube.com/watch?v=783ccP__No8&vl=en)
- Thank you and happy querying!
