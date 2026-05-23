# ⚡ JS Last-Minute Prep

> **For the coder who has 30 minutes and zero chill.** Everything you need, nothing you don't.

---

## Table of Contents
1. [Data Types](#1-data-types)
2. [var vs let vs const](#2-var-vs-let-vs-const)
3. [== vs ===](#3--vs-)
4. [Truthy & Falsy](#4-truthy--falsy)
5. [Functions](#5-functions)
6. [Hoisting](#6-hoisting)
7. [Closures](#7-closures)
8. [this Keyword](#8-this-keyword)
9. [Promises & Async/Await](#9-promises--asyncawait)
10. [Array Methods](#10-array-methods)
11. [Object Essentials](#11-object-essentials)
12. [Spread & Rest](#12-spread--rest)
13. [Destructuring](#13-destructuring)
14. [Prototypes & Classes](#14-prototypes--classes)
15. [Event Loop](#15-event-loop)
16. [Common Gotchas](#16-common-gotchas)

---

## 1. Data Types

**Primitives** (stored by value):
```
string, number, bigint, boolean, undefined, null, symbol
```

**Non-Primitives** (stored by reference):
```
object, array, function
```

```js
typeof "hello"      // "string"
typeof 42           // "number"
typeof true         // "boolean"
typeof undefined    // "undefined"
typeof null         // "object"  ← famous bug, just memorize it
typeof {}           // "object"
typeof []           // "object"  ← use Array.isArray() instead
typeof function(){} // "function"
```

---

## 2. var vs let vs const

| | `var` | `let` | `const` |
|---|---|---|---|
| Scope | Function | Block | Block |
| Hoisted | ✅ (as `undefined`) | ✅ (TDZ — unusable) | ✅ (TDZ — unusable) |
| Re-declare | ✅ | ❌ | ❌ |
| Re-assign | ✅ | ✅ | ❌ |

> **TDZ** = Temporal Dead Zone. The variable exists but throws a `ReferenceError` if accessed before declaration.

```js
console.log(a); // undefined  (var is hoisted)
var a = 5;

console.log(b); // ReferenceError  (let TDZ)
let b = 5;
```

> **Rule of thumb:** Always use `const`. Use `let` only when you need to reassign. Forget `var`.

---

## 3. == vs ===

| Operator | Name | Type Coercion |
|---|---|---|
| `==` | Loose equality | ✅ Converts types first |
| `===` | Strict equality | ❌ Checks type AND value |

```js
0 == "0"    // true  ← coerces string to number
0 === "0"   // false ← different types
null == undefined   // true
null === undefined  // false
```

> **Always use `===`** unless you specifically need loose comparison.

---

## 4. Truthy & Falsy

**Falsy values** (only 6):
```js
false, 0, "", null, undefined, NaN
```

**Everything else is truthy**, including:
```js
"0"   // truthy (non-empty string)
[]    // truthy (empty array)
{}    // truthy (empty object)
```

---

## 5. Functions

**4 ways to define a function:**

```js
// 1. Function Declaration — hoisted ✅
function greet(name) { return `Hi ${name}`; }

// 2. Function Expression — NOT hoisted ❌
const greet = function(name) { return `Hi ${name}`; };

// 3. Arrow Function — no own `this`, no `arguments`
const greet = (name) => `Hi ${name}`;

// 4. IIFE — runs immediately
(function() { console.log("runs now"); })();
```

**Arrow vs Regular — key differences:**
```js
// Arrow functions inherit `this` from surrounding scope
const obj = {
  name: "JS",
  regular: function() { return this.name; }, // "JS" ✅
  arrow: () => this.name,                    // undefined ❌
};
```

---

## 6. Hoisting

JavaScript moves **declarations** (not assignments) to the top of their scope.

```js
// What you write:
console.log(x);  // undefined
var x = 5;

// What JS sees:
var x;
console.log(x);  // undefined
x = 5;
```

```js
// Function declarations are fully hoisted:
sayHi(); // "Hi!" ✅
function sayHi() { console.log("Hi!"); }

// Function expressions are NOT:
sayBye(); // TypeError ❌
var sayBye = function() { console.log("Bye!"); };
```

---

## 7. Closures

A function that **remembers the variables from its outer scope** even after the outer function has returned.

```js
function counter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const increment = counter();
increment(); // 1
increment(); // 2
increment(); // 3
// `count` is alive because of the closure
```

**Classic interview trap:**
```js
// ❌ Broken — var is function-scoped, all share the same `i`
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 3, 3, 3
}

// ✅ Fixed with let (block-scoped, new `i` per iteration)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0, 1, 2
}
```

---

## 8. `this` Keyword

`this` depends on **how** the function is called, not where it's defined.

```js
// 1. Global context
console.log(this); // window (browser) / global (Node)

// 2. Object method
const obj = { name: "JS", getName() { return this.name; } };
obj.getName(); // "JS"

// 3. Regular function (non-strict mode)
function show() { console.log(this); } // window

// 4. Arrow function — borrows `this` from parent scope
const arrow = () => console.log(this); // window

// 5. call / apply / bind — manually set `this`
function greet() { return `Hi ${this.name}`; }
greet.call({ name: "Alice" });    // "Hi Alice"
greet.apply({ name: "Alice" });   // "Hi Alice"
const bound = greet.bind({ name: "Alice" });
bound(); // "Hi Alice"
```

**call vs apply vs bind:**
| Method | Calls immediately | Arguments |
|---|---|---|
| `call` | ✅ | comma-separated |
| `apply` | ✅ | as an array |
| `bind` | ❌ (returns new fn) | comma-separated |

---

## 9. Promises & Async/Await

**Promise states:** `pending` → `fulfilled` or `rejected`

```js
// Creating a Promise
const p = new Promise((resolve, reject) => {
  setTimeout(() => resolve("done!"), 1000);
});

p.then(result => console.log(result))  // "done!"
 .catch(err => console.log(err));
```

```js
// Async/Await — cleaner syntax for the same thing
async function fetchData() {
  try {
    const result = await somePromise();
    console.log(result);
  } catch (err) {
    console.log(err);
  }
}
```

**Useful Promise methods:**
```js
Promise.all([p1, p2, p3])     // waits for ALL — fails if any fails
Promise.allSettled([p1, p2])  // waits for ALL — never fails
Promise.race([p1, p2])        // resolves/rejects with the FIRST to settle
Promise.any([p1, p2])         // resolves with the FIRST success
```

---

## 10. Array Methods

| Method | What it does | Returns |
|---|---|---|
| `map` | Transform each element | New array (same length) |
| `filter` | Keep elements that pass a test | New array (shorter/same) |
| `reduce` | Boil array down to a single value | Single value |
| `find` | First element that passes a test | Element or `undefined` |
| `findIndex` | Index of first match | Number or `-1` |
| `some` | Does ANY element pass? | Boolean |
| `every` | Do ALL elements pass? | Boolean |
| `forEach` | Loop (no return value) | `undefined` |
| `flat` | Flatten nested arrays | New array |
| `includes` | Does element exist? | Boolean |

```js
const nums = [1, 2, 3, 4, 5];

nums.map(n => n * 2);          // [2, 4, 6, 8, 10]
nums.filter(n => n % 2 === 0); // [2, 4]
nums.reduce((acc, n) => acc + n, 0); // 15
nums.find(n => n > 3);         // 4
nums.some(n => n > 4);         // true
nums.every(n => n > 0);        // true
```

---

## 11. Object Essentials

```js
const person = { name: "Alice", age: 25 };

// Access
person.name        // dot notation
person["name"]     // bracket notation (use for dynamic keys)

// Useful methods
Object.keys(person)    // ["name", "age"]
Object.values(person)  // ["Alice", 25]
Object.entries(person) // [["name","Alice"], ["age",25]]
Object.assign({}, person, { age: 26 }) // shallow copy + override
```

**Shallow vs Deep copy:**
```js
// Shallow copy (nested objects still shared)
const copy = { ...person };
const copy2 = Object.assign({}, person);

// Deep copy (fully independent)
const deep = JSON.parse(JSON.stringify(person)); // simple but loses functions/undefined
const deep2 = structuredClone(person);          // modern, preferred
```

---

## 12. Spread & Rest

```js
// Spread (...) — expand
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

const obj1 = { a: 1 };
const obj2 = { ...obj1, b: 2 }; // { a: 1, b: 2 }

// Rest (...) — collect remaining into array
function sum(...nums) {
  return nums.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4); // 10
```

> **Spread** expands. **Rest** collects. Same syntax, opposite purpose.

---

## 13. Destructuring

```js
// Array destructuring
const [a, b, c] = [1, 2, 3];
const [first, , third] = [1, 2, 3]; // skip with comma
const [x = 10] = [];                // default value → x = 10

// Object destructuring
const { name, age } = { name: "Alice", age: 25 };
const { name: fullName } = { name: "Alice" }; // rename → fullName = "Alice"
const { name = "Bob" } = {};                   // default → name = "Bob"

// In function params
function greet({ name, age }) {
  return `${name} is ${age}`;
}
```

---

## 14. Prototypes & Classes

Every object has a `__proto__` pointing to its prototype. This chain is how inheritance works.

```js
// ES6 Class (syntactic sugar over prototypes)
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound.`;
  }
}

class Dog extends Animal {
  speak() {
    return `${this.name} barks.`;
  }
}

const d = new Dog("Rex");
d.speak();              // "Rex barks."
d instanceof Dog;       // true
d instanceof Animal;    // true
```

---

## 15. Event Loop

JavaScript is **single-threaded** but handles async with the event loop.

**Execution order:**
1. **Call Stack** — runs synchronous code first
2. **Microtask Queue** — Promises (`.then`, `async/await`) — runs after each task
3. **Macrotask Queue** — `setTimeout`, `setInterval`, I/O — runs after microtasks

```js
console.log("1");                          // sync
setTimeout(() => console.log("2"), 0);    // macrotask
Promise.resolve().then(() => console.log("3")); // microtask
console.log("4");                          // sync

// Output: 1, 4, 3, 2
```

> Microtasks **always** run before macrotasks, even if the macrotask has a `0ms` delay.

---

## 16. Common Gotchas

```js
// NaN is not equal to itself
NaN === NaN  // false → use Number.isNaN(value)

// typeof null is "object"
typeof null  // "object" ← just memorize this

// Floating point
0.1 + 0.2 === 0.3  // false → use Math.abs(a - b) < Number.EPSILON

// Array equality
[] === []   // false ← different references
{} === {}   // false

// String to number tricks
+"42"       // 42
+""         // 0
+null       // 0
+undefined  // NaN

// Optional chaining & nullish coalescing
user?.address?.city       // undefined instead of TypeError
value ?? "default"        // "default" only if value is null/undefined
                          // (unlike ||, which also triggers on 0, "", false)
```

---

## Quick Reference Cheatsheet

```
Primitives:          string, number, bigint, boolean, undefined, null, symbol
Falsy values:        false, 0, "", null, undefined, NaN
Scope:               var → function | let/const → block
Hoisting:            var → undefined | function decl → full | let/const → TDZ
this:                depends on call site, not definition
Arrow fns:           no own this, no arguments, can't be used as constructor
Promise order:       sync → microtasks (Promise) → macrotasks (setTimeout)
== vs ===:           always use ===
```

---

> 🚀 **You've got this. Go crush it.**
# last-minute-javascript
