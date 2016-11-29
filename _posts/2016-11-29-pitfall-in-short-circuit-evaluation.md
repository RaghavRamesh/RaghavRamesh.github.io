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

In the above two lines, the second part of the expression is not evaluated as the first operand and the operation already determine the final result.

### When is it useful?
This strategy comes in handy when you're defining the constructor of your class and assigning default values for the data members of the class.

```
function Counter(initialCount) {
  // Intent: Set initialCount if specified, else set null
  this._count = initialCount || null;
}
```

So if initialCount is passed while declaring a new Counter, this value is assigned to the data member `_count`, otherwise, it's set to `null`.

This falls under one of those techniques we pick up by reading code, and not by taking an extra step to read the spec. The downside is that the pitfalls have to be learnt the hard way.

### The pitfall
The problem occurs when we use a `falsy` value as the first operand - if a value can be converted to `false`, it's considered `falsy`.

In JavaScript, `0` and empty string `""`, two commonly assigned values are considered `falsy` among others like `null`, `undefined` and `NaN`.

Let's consider our example above,

* When `initialCount` is say, `5`, `5 || null` evaluates to `true || null`, evaluates to `true`. So `5` is assigned to `this._count`. Great!
* When `initialCount` is `0` though, `0 || null` evaluates to `false || null`, evaluates to `null` !! because `0` is `falsy`. Fail!

The same applies to empty strings `""`. Watch out for this while using the short circuit evaluation.

### References
* [Logical operators](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Logical_Operators), Mozilla Developer Network
* [Short-circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation), Wikipedia
