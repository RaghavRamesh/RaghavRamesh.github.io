---
title: "Pitfall in Short circuit evaluation"
date: 2016-11-29
categories: [code]
tags: [javascript]
---

Short-circuit evaluation is an evaluation strategy used in many languages, JavaScript included. Recently I encountered a pitfall which costed me an hour of debugging.

### What is it?
Simply put, it's taking advantage of the fact that a boolean expression is only evaluated, as long as its final result is not yet known. The following rules apply:

```
false && (anything); // returns false
true || (anything);  // returns true
```

In the above two lines, the second part of the expression is not evaluated as the first operand and the operation already determine the final result.

### When is it useful?
This strategy comes in handy when you're defining the constructor of your class and assigning default values for the data members of the class.

```
function Hand(numOfCards) {
  // Intent: Set cards in hand if specified, else set default value 2
  this._numberOfCards = numOfCards || 2;
}
```

So if `numberOfCards` is passed while declaring a new `Hand`, this value is assigned to the data member `_numberOfCards`, otherwise, it's set to `2`.

This falls under one of those techniques we pick up by reading code, and not by taking an extra step to read the spec. The downside is that the pitfalls have to be learnt the hard way.

### The pitfall
The problem occurs when we use a `falsy` value as the first operand - if a value can be converted to `false`, it's considered `falsy`.

In JavaScript, `0` and empty string `""`, two commonly assigned values are considered `falsy` among others like `null`, `undefined` and `NaN`.

Let's consider our example above,

* When `numOfCards` is say, `5`, the expression becomes `5 || 2`, which means the first operand and the operation already determine the final result of the expression.  So `5` is assigned to `this._numberOfCards`. Great!
* When `numOfCards` is `0` though, the expression becomes `0 || 2`, which means the second operand needs to be evaluated since the first operand is `falsy`. The expression evaluates to the default value `2`, even though a value for `numOfCards` was provided. Fail!

The same applies to empty strings `""`. Watch out for these while using the short circuit evaluation.

### An alternative
A slightly verbose but a single line alternative for the above pitfall is to use the ternary operator.

```
this._numberOfCards = (numberOfCards !== undefined && numberOfCards !== null)
  ? numberOfCards
  : 2;
```

### References
* [Logical operators](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Logical_Operators), Mozilla Developer Network
* [Short-circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation), Wikipedia
