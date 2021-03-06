<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [inst][1]
    -   [Parameters][2]
    -   [Examples][3]
-   [handler][4]
    -   [Parameters][5]
    -   [Examples][6]
-   [handlers][7]
    -   [Parameters][8]
    -   [Examples][9]
-   [combineHandlers][10]
    -   [Parameters][11]
-   [execute][12]
    -   [Parameters][13]

## inst

Returns a new effect instance.

### Parameters

-   `name` **[string][14]?** an effect name (optional, default `''`)

### Examples

```javascript
// Creates a new effect instance.
const eff = inst();

// Creates a new effect instance with a name.
const fooEff = inst('foo');

// An effect instance is a function returning an object.
// It calls *effect invocation*.
eff(1); // => {eff: inst%0, values: [1]}
```

Returns **[function][15]** the effect instance

## handler

Returns a handler for the given effect.

It is just a shortcut to `handlers(vh, {[eff]: effh})`.

### Parameters

-   `eff` **[Function][15]** an effect instance
-   `vh` **AsyncGeneratorFunction** a return value handler
-   `effh` **AsyncGeneratorFunction** an effect handler

### Examples

```javascript
const write = inst();

const handleWrite = handler(
  // An effect instance:
  // It is target to handle.
  write,
  // A return value handler:
  // It is called when `main` is finished.
  async function* (v) {
    return v;
  },
  // An effect handler:
  // It is called on each `yield write(...args)` with the continuation from
  // here and the given `args`.
  async function* (k, ...args) {
    console.log(...args);
    return yield* k();
  },
);

const main = async function* () {
  yield write('hello world');
};

execute(handleWrite(main));
// Outputs:
// hello world
```

Returns **AsyncGeeneratorFunction** the handlar for the given effect

## handlers

Returns a handler for the given effects.

### Parameters

-   `vh` **AsyncGeneratorFunction** a return value handler
-   `effhs` **[object][16]** effect handlers

### Examples

```javascript
// Implements `State` monad like effect:

const get = inst();
const put = inst();

const handleState = async function* (init, main) {
  const f = yield* handler(
    async function* (v) {
      return async function* (_) {
        return v;
      };
    },
    {
      async *[get](k) {
        return async function* (s) {
          return yield* k(s)(s);
        };
      },
      async *[put](k, s) {
        return async function* (_) {
          return yield* k()(s);
        },
      },
    },
  )(main);

  return yield* f(init);
};

const main = async function* () {
  yield put(42);
  return yield get();
};

execute(handleState(0, main)).then(console.log);
// Outputs:
// 42
```

Returns **AsyncGeneratorFunction** the handlar for the given effects

## combineHandlers

Combines some handlers to one handler.

### Parameters

-   `hs` **...AsyncGeneratorFunction** effect handlers returned by `handler` or `handlers` function

Returns **AsyncGeneratorFunction** the combined handler

## execute

Runs the given generator and returns its result.

### Parameters

-   `g` **AsyncIterator** a generator

Returns **[Promise][17]&lt;any>** the generator's result

[1]: #inst

[2]: #parameters

[3]: #examples

[4]: #handler

[5]: #parameters-1

[6]: #examples-1

[7]: #handlers

[8]: #parameters-2

[9]: #examples-2

[10]: #combinehandlers

[11]: #parameters-3

[12]: #execute

[13]: #parameters-4

[14]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[15]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function

[16]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object

[17]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise
