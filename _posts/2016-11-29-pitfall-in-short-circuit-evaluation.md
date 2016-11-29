---
title: "Pitfall in Short circuit evaluation"
date: 2016-11-29
categories: [javascript]
tags: [javascript]
---

Short-circuit evaluation is an evaluation strategy used in many languages, JavaScript included. Recently I encountered a pitfall which costed me an hour of debugging.

### What is it?
Simply put it's taking advantage of the fact that a boolean expression is only evaluated as long as its final result is not yet known. The following rules apply:

```
false && (anything); // returns false
true || (anything);  // returns true
```

### When is it useful?
This strategy comes in handy when you're defining the constructor of your class and assigning default values for the data members of the class.

```
function Counter(initialCount) {
  // Intent: Set initialCount if specified, else set null
  this._count = initialCount || null;
}
```

This falls under one of those techniques you pick up by reading code and not from spec. The downside to doing that is when you encounter pitfalls - they have to be learnt the hard way.

### The pitfall
The problem occurs when you're trying to assign `falsy` values - if a value can be converted to `false`, it's considered `falsy`.

In JavaScript, `0` and empty string `""`, two commonly assigned values are considered `falsy` among others like `null`, `undefined` and `NaN`.

Let's consider our example above,

* When initialCount is assigned say, `5`, `5 || null` becomes `true || null` becomes `true`. So `5` is assigned to `this._count`. Great!
* When initialCount = `0` though, `0 || null` becomes `false || null` becomes `null` !! because `0` is `falsy`. Fail!

The same applies to empty strings `""`. So, is it really worth using short-cut evaluation?

### References
* [Logical operators](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Logical_Operators), Mozilla Developer Network
* [Short-circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation), Wikipedia
