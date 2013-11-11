# Promises/A+ Constructor Spec
## Terminology

Some additional terms are introduced for talking about promises:

1. "Settled" means the promise's state is either fulfilled or rejected.

### Promise Fates

A promise's *fate* describes what can happen to the promise in the future. It is
a distinct concept from its state, although they are related.

A promise can have one of two fates:

1. "Unresolved" means that a promise's fate has not yet been determined: it is still
   pending, and the promise resolution procedure has never been called to resolve the
   promise.
2. "Resolved" means that a promise's fate has been determined: it is either settled, or
   pending because it has adopted the state of a pending thenable via the promise
   resolution procedure.

Note that a promise can only move from unresolved to resolved, never the other way
around.

## The Promise Constructor
An implementation of this specification supplies a function responsible for
constructing new promises. The **promise constructor** takes as its sole argument
a user-supplied **resolver function** which is given the means to resolve the
constructed promise.

Here we refer to this promise constructor by the name `Promise` for concreteness,
although the name is implementation-specific.

```javascript
var promise = new Promise(resolver);
```

1. Can be called without `new`, with the same results.
2. `Object.getPrototypeOf(promise) === Promise.prototype` must be true.
3. `promise.constructor === Promise.prototype.constructor === Promise` must be true.
4. `Promise.length === 1` must be true.
5. If `resolver` is not a function, the implementation must throw a `TypeError`.
6. If `resolver` is a function:
    1. It must be called with the promise's resolver arguments.
    2. It must be called immediately, before `Promise` returns.

## The Resolver Arguments

When `resolver` is called, it is given the constructed promise's **resolver
arguments**, which have the ability to resolve the promise.


```javascript
var promise = new Promise(function(resolve, reject) {
  // …
});
```

### Calling `resolve(x)`

1. If `promise` is resolved, nothing happens (in particular, no exception may be
   thrown).
2. Otherwise, run the Promise Resolution Procedure `[[Resolve]](promise, x)`

### Calling `reject(reason)`

Calling `reject(reason)` resolves the promise to a rejection reason (i.e. rejects the
promise)

1. If `promise` is resolved, nothing happens (in particular, no exception may be
   thrown).
2. Otherwise, reject `promise` with `reason` as its rejection reason.

## Resolver Throwing
If `resolver` throws an exception, call it `e`.

1. If `promise` is resolved, nothing happens (in particular, no exception may be
   thrown).
2. Otherwise, reject `promise` with `e` as its rejection reason.
