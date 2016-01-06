# JavaScript Coding Style

This document is intended to provide consistent and helpful coding convention for JavaScript code. The main focus of this document is around the ES2015 standard, but it does address older standards when applicable. It should be usable either in Node/IO.js or in the browser.

The recommended transpiling tool for ES2015 to ES5 is [babel](http://babeljs.io/). The recommended bundler for the browser is [webpack](https://github.com/webpack/webpack), but [browserify](http://browserify.org/) is also acceptable.

The recommended text editor is Sublime Text using [babel-sublime](https://github.com/babel/babel-sublime). Other editors are acceptable, as long as they have proper ES2015 and optionally, JSX support.

Instructions denoted as **must**/**must not** are *mandatory*. Instructions denoted as **should**/**should not** are *preferred*.

Rationale for each instruction is *italic*. Some instructions are under the *pure convention* rationale: it means that when several, practically equivalent (besides bikeshedding) options are available, we pick one and stick to it to enforce consistency in the codebase.

## Evil constructs

- The following constructs **must not** be used, for both performance and critical security reasons.

  - `eval` and similar `new Function`, `setTimeout(string)`, `setInterval(string)`, `setImmediate(string)` (however `setTimeout(function)` etc. is perfectly fine)

  - `with`

  - primitive type wrapper constructors (`new Number`, `new String`, `new Boolean`, `new Array`, `new Object`)

  - tabs (they are evil, don't use them)

*`eval` and similar prevents runtime optimizations and leaks the execution context. See [How evil is eval?](https://javascriptweblog.wordpress.com/2010/04/19/how-evil-is-eval/).*

*`with` present the same kind of issues than `eval`. In addition, it is often ambiguous. See [the MDN with docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/with).*

*Use of tabs should be avoided because (as of this writing in the 21st Century) there still is not a standard for the placement of tabstops.*

*Primitive type wrappers are slower than their primitive counterparts. They also cause subtle bugs with `instanceof`.*

## Strict mode

- Strict mode **must** be used. However, it **should** be inserted at build time, not in the original source.

## Casing

- Regular identifiers **must** be formatted in Camel Case `likeThis`.

- Only functions with constructor semantics, classes, or namespaces **must** start with an Uppercase letter (PascalCase), `LikeThis`.

- Macros (to be processed at build time) **must** be formatted `__LIKE_THIS__`.

- Magic values (eg. numerical constants) **must** be formatted `LIKE_THIS`.

- Private properties and methods **must** be formatted `_likeThis`.

```js
const regularIdentifier;
function Constructor() { ... }
class Class { ... }
const Namespace = {};

if (__MACRO__) { ... }
const SOME_MAGIC_NUMBER = 1337;
const {
    _privateMethod() {
        ...
    }

    publicMethod() {
        ...
    }
}
```

*Mostly pure convention. Magic values should always be assigned to named constants, not used directly in formulas, for easier debugging and maintenance. Enforcing __lexical__ 'privacy' of methods and properties is possible using closures, but it comes with a performance cost and more coding efforts, when in the vast majority of cases you just want to enforce a __conventional__ privacy: users can violate privacy, at their own risk.*


## Objects

- Regular objects **must** be created using the Object syntax literal.

```js
// bad
const obj = new Object();
const obj = Object.create(Object.prototype);

// good
const obj = {};
```

- Object literals keys **must** be camelCase (this doesn't apply to programmatically generated keys).

```js
// bad
const obj = {
    'not-camel-cased': ...
};

// good
const obj = {
    camelCased: ...
};
// acceptable
const myKey = 'something-from-somewhere';

const obj = {
    [myKey]: ...
};
```

- An object declaration **must** have spaces after each `{` and before each `}`.

- The last value in the object declaration **must not** end with a comma.

- In an object declaration, there **must not** be space before each colon but there **must** be one after each colon.

```js
// bad
const a = {
    k1: v1,
    k2: v2,
};

const b = { k1: v1, k2: v2, };
const c = {k1:v1};

// good
const a = {
    k1: v1,
    k2: v2
};

const b = { k1: v1, k2: v2 };
const c = { k1: v2 };
```

*Mostly pure convention.*

## Arrays

- Arrays **must** be created using the Array syntax literal.

```js
// bad
const a = new Array();

// good
const a = [];
```

## Strings

- Static strings **must** be declared using single quotes.

- Dynamic strings **must** be declared using ES2015 template literals (backticks). Dynamic strings **must not** be declared using single- or double-quotes.

- Never use double-quotes.

```js
// bad
const a = "foobar";
const b = 'foo' + a + 'bar';
const c = `foobar`;

// good
const a  = 'foobar';
const b = `foo${a}bar`;
const c = 'foobar';
```

*Mostly pure convention. Backticks are always preferred for dynamic strings as they are easier to read and easier to scan. When working with the DOM, single quotes require less string manipulation to produce valid HTML.*

## var, let, const

- `var` **must not** be used when writing code conforming with the ES2015 standard.
  - `var` **must** be used when writing ES5 or earlier code.
    - When writing ES5 code, a single `var` statement per scope **should** be used.

- `const` **should** be used by default. If you really need to use `let`, its acceptable, but its most often a sign that you should refactor.

- `const` (or `let`) **must** be used exactly once per variable declaration.

```js
// ES2015 Spec Only
// bad
const a = 1, b = 2, c = 3;

// good
const a = 1;
const b = 2;
const c = 3;

// actually best
const [a, b, c] = [1, 2, 3];
```

```js
// ES5 Spec Only
// bad
var a = 1;
var b = 2;
var c = 3;

// good
var a = 1,
    b = 2,
    c = 3;
```

*`var` is to be considered legacy; there are no practical use cases for `var` anymore. All values (rather than variables) should be `const` (single-assignment) by default; which means that unless otherwise specified, a value can not change behind your back. Mutability is therefore opt-in rather than opt-out. Note however that a `const` object can still be mutated; if you need deep immutability, consider using [`immutable-js`](http://facebook.github.io/immutable-js/).*

## Functions

- Regular (named and hoisted) function declarations **should** be used instead of anonymous functions by default.

- `new` **must not** be used with functions which are not ES2015 classes.

```js
// bad
function A() {

}
const a = new A();

// good
class A {

}
const a = new A();
```

- When using anonymous functions, arrow function **should** be used instead of anonymous `function`.

- When using 'anonymous' `function`, a name **must** be given.
  - Anonymous function names **should** be removed when building

```js
const a = [1, 2, 3];
// bad
a.reduce(function (x, y) { return x + y; }, 0);

// better, but still bad
a.reduce(function sum(x, y) { return x + y; }, 0);

// acceptable but convoluted
function sum(x, y) {
    return x + y;
}
a.reduce(sum, 0);


// good
a.reduce((x, y) => x + y, 0);
```

- Parens **must** be used for arrow functions parameters, even when there is only one parameter.

```js
// bad
const triple = x => 3 * x;

// good
const triple = (x) => 3 * x;
```

- Arrow functions **should** be used instead of `Function.prototype.bind` when applicable. `self` / `_this` / `that` trickery **must not** be used.

```js
function method(...params) {
    ...
}

// terribly bad
const self = this;
const boundMethod = function (...params) {
    return method.apply(self, params);
}

// acceptable
const boundMethod = method.bind(this);

// best
const boundMethod = (...params) => method.apply(this, params);
```

*Mostly pure conventions. `Function#bind` is [relatively slow](http://jsperf.com/bind-vs-scope-perf) compared to lexical `thisArg` scoping, but lexical scoping is ugly and harder to read. Arrow functions desugar to lexical scoping and are a good compromise. In some cases the following snippet can be useful:*

```js
const scope = (fn, thisArg) => (...params) => fn.apply(thisArg, ...params);
```

## Function parameters

- A parameter **must not** be named `arguments`.
  - Arguments **should** (almost always) be named.
  - The `arguments` array **should not** be used to to access arguments. (very, very few exceptions)

- Parameters **should** BE explicitly typechecked in a `__DEV__` block. They CAN be documented in addition.

- ES2015 default parameter value **should** be used when applicable.

- Destructuring parameter syntax **should** be used when applicable.

- Explicit variadic expansion **should** be used over ES2015 rest syntax. If you really need true dynamic variadic functions, ES2015 rest syntax **must** be used, not `arguments` inspection.

```js
// bad
function sum() {
    let sum = 0;

    for (let k = 0; k < arguments.length; k++) {
        sum += arguments[k];
    }

    return sum;
}

// good
function sum(a = 0, b = 0, c = 0, d = 0, e = 0, f = 0, g = 0, h = 0, i = 0) {
    return a + b + c + d + e + f + g + h + i;
}

// acceptable
function sum(...rest) {
    return rest.reduce((x, y) => x + y, 0);
}
```

- Object parameters (with default values) **should** be used for options parameters. Boolean parameters **must not** be used.

```js
// bad
function divide(a, b, shouldCheckForZero = false ) {

}

// good
function divide(a, b, { shouldCheckForZero = false } = {}) {

}
```
*ES2015 destructuring assignment is a very powerful construct which considerably simplifies the use of object-as-hashes params. It also enables a kind of named parameters pattern. While named parameters are usually more readable than unnamed parameters, it really shines against the [boolean trap](http://ariya.ofilabs.com/2011/08/hall-of-api-shame-boolean-trap.html) antipattern.*

## Properties

- Dot/subscript notation **must** be used consistently.

- Dot notation **should** be used when possible.

```js
// bad
t.x = 3;
t['y'] = 4;

// good
t['x'] = 3;
t['y'] = 4;

// better
t.x = 3;
t.y = 4;
```

*Mostly pure convention. Dot notation is usually easier to read, but sometimes it's just not possible (for dynamic keys and special characters). In this case, consistency is best.*

## Operators

- Short-hand operators `+=`-like and `++`-like operators **should** be used with care. Consider refactoring to get rid of the underlying `let`.
  - Use when readability doesn't suffer as a result

```js
const a = 1;
const b = 2;
// bad
const c = ++a * b++;
const d += c++ * a;

let n = 0;

// Acceptable in certain situations
n += 1;
n++;

// good
n = n + 1;
```

- Non-strict comparison operators `==` or `!=` **must not** be used. Strict comparison operators `===` or `!==` **must** be used instead.

- Boolean force-casting `!!expr` **should not** be used. `expr` **should** BE used directly, unless you specifically care about leaking values in a function call or a return value.

```js
// bad
if (!!expr) {
    ...
}

// good
if (expr) {
    ...
}

// acceptable to prevent leaking values
someUntrustedFunction(!!expr);
```

*Shorthand operators are marginally harder to read, but its really more of a convention. Boolean force-casting is useless and convoluted, unless you specifically care about the value of the expression leaking (besides its boolean value), but this doesn't matter in `if`-like constructs, only in function calls or return values.*

## Comments

- `/* ... */`-style **must** be used for non-jsdoc multiline comments.

- `/** ... */`-style **must** be used for jsdoc comments.

- Single-line comments `//` **should** go on the previous line to whatever you're commenting.

*Pure convention.*

## Whitespace

- Tab characters **must not** be used. Ever. Tabs are evil. Don't use them.

- Soft-spacing 4-characters **must** be used.

```js
// bad
if (true) {
  return 42;
}

// good
if (true) {
    return 42;
}
```

- 1 space **must** be used before the leading brace `{`.

```js
// bad
if(true){
    ...
}

// good
if (true) {
    ...
}
```

- 1 space **must** be used before the ending brace `}` in single-line object literals or function declaration.

```js
// bad
function answer() { return 42;}
const a = { a: 42};

// good
function answer() { return 42; }
const a = { a: 42 };
```

- Space **must not** be used before the leading bracket `[` or the ending bracket `]` in single-line array literals.

```js
// bad
const a = [ 1, 2 ];

// good
const a = [1, 2];
```

- Space **must** be used before the leading paren `(`.
  - An exception to this is named function declarations

```js
// bad
if(true) {
    ...
}

// good
if (true) {
    ...
}

// good function declarations
function () {

}

function a() {

}
```

- Space **must not** be used between `function` and `*` when defining generator. Space **must** BE used between `*` and the function name when defining named generators.

```js
// bad
function *g() {

}

// good
function* g() {

}
```

- Space **must not** be used between `*` and the generator name when using the shorthand generator method syntax.

```js
// bad
const a = {
    * g() {
        ...
    }
};

// good
const a = {
    *g() {
        ...
    }
};
```

- Spaces **must** be used between infix operators.

```js
// bad
const a = b+c;
const a= b + c;

// good
const a = b + c;
```

- Files **must** end with a single newline character.

- Leading dot and proper indentation **must** be used for method chaining.

```js
// bad
a.then(...).
then(...).
catch(...);

a.then(...)
.then(...)
.catch(...);

// good
a.then(...)
    .then(...)
    .then(...)
    .catch(...);
```

- Two new lines **must** precede and follow all code blocks
  - A few exceptions:
    - If the block is the first item in the parent block, a single preceding new line is acceptable
    - The block is the last item in the parent block, a single trailing newline is acceptable
    - The block is being declared as part of an object

```js
// bad
const a;
if (a === 1) {
    ...
}
return a;

// good
const a;

if (a === 1) {
    ...
}

return a;

function b() {
    if (...) {
        ...
    }

    const c = 5;

    for (...) {
        ...
    }
}

const obj = {
    x: 1,
    y: 2,
    z() {
        ...
    }
};
```

*Pure convention. Also, tabs are evil. Don't use them.*

## Blocks

- Braces **must** BE used for blocks, even single-statement blocks. Only exception is 1-expression arrow functions.

```js
// bad
if (true)
 return 42;
if (true) return 42;
const id = (x) => { return x; };

// good
if (true) { return 42; }

if (true) {
    return 42;
}

const id = (x) => x;

const id = (x) => {
    return x;
};

const id = (x) => functionCall(x);
```

*Always-braces is easier to maintain: one can add or remove statements/instructions without having to maintain the braces. Also easier to scan.*

## Commas

- Trailing commas **must not** be used for multiline literals, and **must not** be used for single-line literals.

```js
// bad
const a = { a: 42, b: 43, };

const a = {
    a: 42,
    b: 42,
};

// good
const a = { a: 42, b: 42 };

const a = {
    a: 42,
    b: 43
};
```

## Semicolons

- Trailing semicolons **must** be used, even when they are not strictly required.

```js
// bad
function id(x) { return x }

// good
function id(x) { return x; }

// actually better
const id = (x) => x;
```

*Easier to maintain; one can add or remove statements without having to maintain the semicolon. Also produces cleaner git diffs. Avoid subtle bugs.*

## if/else construct

- `if`/`else if`/`else` **must** be preceded by a newline.

```js
// bad
if (...) {
    ...
} else if (...) {
    ...
} else {
    ...
}

// good
if (...) {
    ...
}
else if (...) {
    ...
}
else {
    ...
}
```

*Easier to read and refactor.*

## Ternary operator

- Ternary operator (`?:`) **should** be avoided.

- It **must not** be used if:

  - The whole expression cannot fit on one line;

  - The expression involve other ternary operators;

  - The members of the expression are too complex to keep it readable.

```js
// bad
const foo = bar === baz ? aFunctionWithALongNameCalledIfTheFirstExpressionIsTrue()
: aFunctionWithALongNameCalledIfTheFirstExpressionIsFalse();

// bad
const foo = bar === baz
? aFunctionWithALongNameCalledIfTheFirstExpressionIsTrue()
: aFunctionWithALongNameCalledIfTheFirstExpressionIsFalse();

// bad
const foo = bar === baz ?
aFunctionWithALongNameCalledIfTheFirstExpressionIsTrue() :
aFunctionWithALongNameCalledIfTheFirstExpressionIsFalse();

// bad
const foo = fooBar(bar + qux) * qux ? fooBar(barFoo({ bar })[qux]).foo : fooBar(barFoo({ qux })[foo]).baz;

// bad
const foo = qux ? (bar ? qux : baz) : bar;

// acceptable
const foo = bar === baz ? bar + 1 : baz - 1;

// good
const foo = bar ? baz : qux;
```

# switch construct

- `switch` construct **should not** be used unless you are absolutely confident you are optimizing a super hot function which your JS engine can't optimize by itself. `if/else if/else` **should** be used instead.

*When two constructs are equivalent, pick one and stick to it. Also, missing `break` bugs are very tedious.*

# for in, for of

- `for in` and `for of` **should not** be used unless you are absolutely confident you are optimizing a super hot function which your JS engine can't optimize by itself. `forEach/map` **should** be used instead.

*When two constructs are equivalent, pick one and stick to it. Also, missing `hasOwnProperty` check bugs are very tedious. `for` loops are very rarely needed in ES2015 code where `forEach/map` and arrow functions can achieve almost every conceivable looping pattern and are easier to reason about. In edge cases where optimization is actually needed, it can be helpful though.*

## Method short notation

- ES2015 method short notation **should** be used when applicable.

```js
// bad
const a = {
    f: function () {
        ...
    }
}

// good
const a =  {
    f() {
        ...
    }
}
```

*Less clutter and easier to maintain.*

## ES2015 modules

- ES2015 modules **must** be used, `require`-style module **must not** be used. Destructuring assignment **should** be used when applicable.

```js
// bad
const moduleA = require('moduleA');
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// bad
import * as moduleA from 'moduleA';
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// good
import { func1, func2 } from 'moduleA';
```

*More standards-friendly and easier to change the module implementation backend (eg. CommonJS/AMD) at the transpiler level.*

## undefined

- `undefined` **must not** be used, `void 0` **must** be used instead.

```js
// bad
if (x === undefined) {
    ...
}

// good
if (x === void 0) {
    ...
}
```

*This guarantees the value of undefined. since `undefined` is not a reserved word, it can be overridden and is not guaranteed to have an undefined value.*

## Object extension and reshaping

- Object reshaping **should** be avoided by declaring their shape early with `null` values, with the notable exceptions of Objects used as maps (but in this case, ES2015 Map/Set/WeakMap/WeapSet is often more suited).

- `delete` **should not** be used, with the notable exceptions of Objects used as maps (see above). Settings the value to `null` **should** be used to explicitly remove a reference for garbage collection.

- In case reshaping is unavoidable, `Object.assign` **must** be used. In particular, `Object.assign` **must** be used to define prototype properties on ES2015 classes.

- In the particular case of a constructor function, an object **should not** be reshaped after the end of the constructor.

- `__proto__` **should not** be used. `Object.setPrototypeOf` **should** be used instead, but with great care.

```js
// bad
const a = {};
a.x = 3;
delete a.x;

// if reshape unavoidable
const a = {};
Object.assign(a, { x: 3 });

// good
const a = { x: null };
a.x = 3;
a.x = null;


function F() { ... }

// bad
F.prototype.staticA = 43;
F.prototype.staticB = 44;
F.prototype.protoProp = null;
F.prototype.method = function () {
    ...
}

// good
Object.assign(F.prototype, {
    staticA: 43,
    staticB: 44,
    protoProp: null,
    method() {
        ...
    }
});
```

*Object reshaping highly impacts performance on frequently created/used Objects. While negligible on less frequently used Objects (eg. singletons), it is preferable to be consistent and always avoid reshaping Objects. Objects used as hashes (maps) will often be reshaped anyway so this cannot be avoided, but in this case consider using ES2015 Map/Set/WeakMap/WeakSet. Usage of Object.assign instead of multiple property assignment is marginally less efficient, but is easier to maintain and enables using the ES2015 method short notation. Mutating the `__proto__` has huge performance implications and should be used with extreme care.*


## Classes

- Class names **must** be `PascalCased`.

- When defined, `constructor` **should** be the first method of a class definition.

- The order of class methods definitions **should** always be:

  - `constructor`

  - public `get/set` accessors, grouped by name

  - public methods

  - private `get/set` accessors (with a `_` prefix), grouped by name

  - private methods (with a `_` prefix)

  - public static methods

  - private static methods (with a `_` prefix)

- Prototype properties **should** be defined immediately after the class definition and use `Object.assign` and **should not** contain methods (set them in the class definition directly).

- If your transpiler or environment support ES7 class properties, prototype properties of classes **should not** be used (use class properties instead).

```js
class Foo extends Bar {
    // If your transpiler or environment support ES7 class properties:
    bax = null;
    baw = 1337;

    constructor() { // constructor
        ...
    }

    get baz() { // public getter
        ...
    }

    set baz(val) { // public setter
        ...
    }

    doFoo() { // public method
        ...
    }

    get _baz() { // private getter
        ...  
    }

    set _baz(val) { // private setter
        ...
    }

    _doFoo() { // private method
        ...  
    }

    // If your transpiler or environment support ES7 class properties:
    static theFoo = 'theBar';

    static doFoo() { // public static method
        ...
    }

    static _doFoo() { // private static method
        ...  
    }
}

// If your transpiler or environment doesn't support ES7 class properties:
Object.assign(Foo.prototype, { // prototype properties
    bax: null,
    baw: 1337,
});

Object.assign(Foo, { // static properties
    theFoo: 'theBar',
});
```

*Mostly pure conventions. Public API are closer to the beginning of the declaration, making it easier to read. Also produces cleaner diffs since private methods are more susceptible to changes than public methods. The prototype properties must be defined outside of the `class` block due to ES2015 limitations, but it should be very close to the class definition.*

## Proxies

- Proxies **must not** be used at the moment.

*There is no production-ready transpilation for ES2015 Proxies. In addition, they may introduce questionable black magic.*

## Promises

- `Promise#then` **must not** be used with 2 parameters. `Promise#catch` **must** be used instead.

- `Promise#try` **should** be used for `Promise`-returning function, instead of `return Promise.resolve`/`return Promise.reject`.

```js
// bad
p.then(() => {
    ...
}, () => {
    ...
});

function f() {
    if (...) {
        return Promise.resolve(...);
    }
    else {
        return Promise.reject(...);
    }
}

// good
p.then(() => {

})
.catch(() => {

});

function f() {
    return Promise.try(() => {
        if (...) {
            return ...;
        }
        else {
            throw ...;
        }
    });
}
```

*`Promise` is a relatively low-level/interoperaribility spec. The excellent [`bluebird`](https://github.com/petkaantonov/bluebird/blob/master/API.md) implements higher-level primitives that implement the hardest thing to do right with Promises: error propagation. In addition to its excellent performance (outperforming native Promises in most cases), it will most likely help you avoid common pitfalls with Promises.*

## Tail calls

- Tail call optimization **should not** be assumed except for simple recursive functions (self-referencing functions). Tail call optimization **must not** be relied on for complex recursive function (eg. mutually recursive functions).

```js
// bad
function odd(n) {
    if (n === 0) {
        return false;
    }
    if (n === 1) {
        return true;
    }

    return !even(n-1);
}

function even(n) {
    if (n === 0) {
        return true;
    }
    if (n === 1) {
        return false;
    }

    return !odd(n-1);
}

// good
function factorial(n) {
    function factorialLoop(n, acc) {
        if (n === 1) {
            return acc;
        }
        else {
            return factorialLoop(n - 1, n*acc);
        }
    }

    return factorialLoop(n, 1);
}
```

*Tail calls are very hard to transpile efficiently. It is only partially supported by babel.*

## Generators

- Generators **must** contain at least one `yield` statement. If not, consider using a `Promise`-returning function instead.

- Generator short-hand definition **must** be used where applicable.

```js
// bad
const a = {
    g: function*() {
        ...
    }
}

// good
const a = {
    *g() {
        ...
    }
}
```

*Pure convention. Also, `Promise` and `yield` are interoperable, see [`Promise#coroutine`](https://github.com/petkaantonov/bluebird/blob/master/API.md#generators).*

## Alert and Confirm

- `alert` and `confirm` **should not** be used. The exception to this is 508 mode. As of today, the most accessible alternative to our current toast messages is the `alert` or `confirm` box.
