# Qualified Imports ECMAScript Proposal

**Author**: Michał Miszczyszyn (@mmiszy)

## Summary

Introduce new syntax which allows for creating an object from imports:

```js
import { a, b, fn } as Package from './package.js';
```

## Motivation

When using ECMAScript modules, it's often desired to import only certain named exports like so:

```js
import { a, b, fn } from "./package.js";
```

This is currently part of the ECMAScript specification and is widely supported. However, some problems arise when using multiple imports in the same file.

### Vague names

It's often the case that small utility functions with vague names are being exported from modules, and they only make sense when put in certain context. For instance:

```js
import { fmap } from "./functor.js";
```

```js
import { fmap } from "./applicative.js";
```

```js
import { fmap } from "./monad.js";
```

Out of context, `fmap` could be either of those implementations.

### Name conflicts

Importing two (or more) named exports from different packages results in a conflict of declarations:

```js
import { a } from "./one.js";
import { a } from "./two.js"; // Error!
```

This can be currently solved by renaming the imports:

```js
import { a as aOne } from "./one.js";
import { a as aTwo } from "./two.js";
```

Yet, it quickly becomes cumbersome when we have multiple imports such as when using utilities libraries:

```js
import {
  map as lodashMap,
  reduce as lodashReduce,
  find as lodashFind,
  filter as lodashFilter,
  // … etc.
} from "./lodash.js";
import {
  map as bluebirdMap,
  reduce as bluebirdReduce,
  find as bluebirdFind,
  filter as bluebirdFilter,
  // … etc.
} from "./bluebird.js";
```

## Proposed Solution

Qualified Imports: a new syntax that allows importing multiple named exports and grouping them into a namespace. Compare:

### Vague names solution

```js
import { fmap } as Functor from "./functor.js";

Functor.fmap(/* … */);
```

```js
import { fmap } as Applicative from "./applicative.js";

Applicative.fmap(/* … */);
```

```js
import { fmap } as Monad from "./monad.js";

Monad.fmap(/* … */);
```

### Name conflicts solution

```js
import {
  map,
  reduce,
  find,
  filter,
  // … etc.
} as Lodash from "./lodash.js";
import {
  map,
  reduce,
  find,
  filter,
  // … etc.
} as Bluebird from "./bluebird.js";

Lodash.find(/* … */);
Bluebird.map(/* … */);
```

## FAQ
### Why not just use `import * as X from './x.js'` ?
Even though this might work for certain scenarios, it makes it more difficult to reason about the modules being used. This is a problem not only for the developers due to the lack of readability, but also for the tooling which might not be able to correctly determine whether particular exports are actually used or not.

Moreover, there's an overhead related to parsing and gathering all of the exports in a single namespace – compared to just a few we might want to use.

### You could use dynamic import with destructuring like `const { a, b } = await import('./module.js');
Yes. However, there are a few major differences to what is drafted in this proposal. (#todo what are they?)

Moreover, using destructuring with a dynamic import makes the intentions less clear.

## Previous work

- haskell
- ocaml

#todo: add links and compare
