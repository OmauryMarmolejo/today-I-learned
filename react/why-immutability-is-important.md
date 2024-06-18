# Why immutability is important

Immutability is a core principle in functional programming, and has lots of benefits. This article explains why immutability is important, and how it can help you write better code.

There are basically two ways to change data: You can either mutate it, or you can create a new copy of the data with the changes you want. Mutating data means changing the data in place, while immutability means creating a new copy of the data with the changes you want.

Immutability has several benefits:

1. **Predictability**: When data is immutable, you can be sure that it won't change unexpectedly. This makes it easier to reason about your code, and reduces the chance of bugs.
2. **Concurrency**: Immutable data can be shared across threads without the need for locks, because you know it won't change.
3. **Undo/Redo**: With immutable data, you can easily implement undo/redo functionality, because you can keep a history of all the changes.
4. **Performance**: Immutability can be more performant in some cases, because you can avoid unnecessary copying of data.

In JavaScript, objects and arrays are mutable by default. This means that if you change an object or array, all references to that object or array will see the change. This can lead to unexpected behavior, especially in large codebases.

## Example

Here's an example to illustrate the benefits of immutability. Suppose you have an array of numbers, and you want to add a new number to the array. Here's how you might do it with mutable code:

```javascript
let numbers = [1, 2, 3];
numbers.push(4);
```

This code mutates the `numbers` array in place by adding a new number to it. If you have other code that relies on the `numbers` array, it might break unexpectedly if you change it like this.

Here's how you could do the same thing with immutable code:

```javascript
let numbers = [1, 2, 3];
let newNumbers = [...numbers, 4];
```

This code creates a new array `newNumbers` that contains all the numbers from the `numbers` array, plus the new number `4`. The `numbers` array remains unchanged, so any other code that relies on it will continue to work as expected.
