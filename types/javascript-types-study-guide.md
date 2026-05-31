# JavaScript Types

## Overview

In JavaScript, **values have types**, not variables.

```js
var v;
v = "1"; // string value
v = 2;   // number value
v = true; // boolean value
```

The variable `v` can hold different kinds of values. The type belongs to the value stored inside it.

Common exam idea: **"Everything in JavaScript is an object" is false.** Values like `false`, `42`, `"hi"`, `null`, and `undefined` are not objects, even if some can behave like objects for a moment.

Note: In output blocks, quotes around values like `"number"` mean the result is a string. Some consoles print strings without visible quotes.

## Primitive Types

Primitive values are simple values. They are not objects.

Think of a primitive value like a single small label: `"Ali"`, `25`, or `true`. The value itself is stored and compared directly.

Main primitive types from the notes:

- `undefined`
- `null`
- `boolean`
- `string`
- `symbol`
- `number`

Example:

```js
var a = 10;
var b = a;

b = 20;

console.log(a);
console.log(b);
```

Output:

```text
10
20
```

Why?

`a` and `b` hold primitive number values. Changing `b` does not change `a`.

## Non-Primitive Types

Non-primitive values are objects. They can hold collections of data or behavior.

Objects include:

- plain objects: `{}`
- arrays: `[1, 2, 3]`
- functions: `function() {}`

Arrays and functions are special kinds of objects.

Example:

```js
var user1 = { name: "Ali" };
var user2 = user1;

user2.name = "Sara";

console.log(user1.name);
console.log(user2.name);
```

Output:

```text
"Sara"
"Sara"
```

Why?

`user1` and `user2` point to the same object. Changing the object through `user2` is also visible through `user1`.

Tricky comparison:

```js
console.log({} === {});
console.log([] === []);
```

Output:

```text
false
false
```

Why?

Each `{}` or `[]` creates a new object. Two different objects are not strictly equal, even if they look empty and identical.

### Examples

```js
typeof "hello";
typeof 42;
typeof true;
typeof Symbol();
typeof {};
typeof [1, 2, 3];
typeof function() {};
```

Output:

```text
"string"
"number"
"boolean"
"symbol"
"object"
"object"
"function"
```

Why?

- Strings, numbers, booleans, and symbols are primitive values.
- Arrays are objects, so `typeof [1, 2, 3]` gives `"object"`.
- Functions are callable objects, but `typeof` has a special result for them: `"function"`.

## The `typeof` Operator

`typeof` always returns a string.

Original lecture example:

```js
var v;
typeof v;

v = "1";
typeof v;

v = 2;
typeof v;

v = true;
typeof v;

v = {};
typeof v;

v = Symbol();
typeof v;
```

Output:

```text
"undefined"
"string"
"number"
"boolean"
"object"
"symbol"
```

Why?

`typeof` checks the type of the current value inside `v`.

### Tricky `typeof` Cases

```js
typeof doesntExist;
```

Output:

```text
"undefined"
```

Why?

`doesntExist` was never declared, but `typeof` does not throw an error for undeclared variables. It safely returns `"undefined"`.

```js
var v = null;
typeof v;
```

Output:

```text
"object"
```

Why?

This is a historical JavaScript bug. `null` is not really an object, but `typeof null` returns `"object"`.

```js
var v = function() {};
typeof v;
```

Output:

```text
"function"
```

Why?

Functions are objects, but JavaScript gives them a special `typeof` result because they can be called.

```js
var v = [1, 2, 3];
typeof v;
Array.isArray(v);
```

Output:

```text
"object"
true
```

Why?

Arrays are objects. Use `Array.isArray(v)` when you need to check for an array.

## Undefined vs Undeclared vs Uninitialized

### `undefined`

A variable is `undefined` when it exists, but currently has no assigned value.

```js
var x;
console.log(x);
console.log(typeof x);
```

Output:

```text
undefined
"undefined"
```

Why?

`x` was declared with `var`, but no value was assigned.

### Undeclared

A variable is undeclared when it was never created in any accessible scope.

```js
console.log(typeof y);
```

Output:

```text
"undefined"
```

Why?

`y` does not exist, but `typeof y` is allowed.

```js
console.log(y);
```

Output:

```text
ReferenceError: y is not defined
```

Why?

Directly reading an undeclared variable is an error.

### Uninitialized / TDZ

`let` and `const` variables are in the **Temporal Dead Zone (TDZ)** before their declaration line runs.

```js
console.log(typeof score);
let score = 10;
```

Output:

```text
ReferenceError
```

Why?

`score` exists in the scope, but it is not initialized yet. `typeof` does not save you from the TDZ.

## `NaN` and `isNaN`

`NaN` means **Not a Number**, but its type is still `number`.

```js
console.log(typeof NaN);
```

Output:

```text
"number"
```

Why?

`NaN` is an invalid number value.

Original lecture example:

```js
var myAge = Number("0o46");
var myNextAge = Number("39");
var myCatsAge = Number("n/a");

console.log(myAge);
console.log(myNextAge);
console.log(myCatsAge);
```

Output:

```text
38
39
NaN
```

Why?

- `"0o46"` is an octal number string, so it becomes decimal `38`.
- `"39"` becomes number `39`.
- `"n/a"` cannot become a valid number, so it becomes `NaN`.

### Math With `NaN`

```js
var myAge = Number("0o46");

console.log(myAge - "my son's age");
```

Output:

```text
NaN
```

Why?

The `-` operator needs numbers. JavaScript tries this:

```js
myAge - Number("my son's age");
38 - NaN;
NaN;
```

Any normal math operation with `NaN` usually gives `NaN`.

### `NaN` Is Not Equal to Itself

```js
var myCatsAge = Number("n/a");

console.log(myCatsAge === myCatsAge);
console.log(myCatsAge == myCatsAge);
console.log(undefined == undefined);
console.log(undefined === undefined);
```

Output:

```text
false
false
true
true
```

Why?

`NaN` is the only JavaScript value that is not equal to itself. `undefined` is equal to itself.

### `isNaN` vs `Number.isNaN`

```js
var myAge = Number("0o46");
var myCatsAge = Number("n/a");

console.log(isNaN(myAge));
console.log(isNaN(myCatsAge));
console.log(isNaN("my son's age"));

console.log(Number.isNaN(myCatsAge));
console.log(Number.isNaN("my son's age"));
```

Output:

```text
false
true
true
true
false
```

Why?

- `isNaN(value)` first converts the value to a number, then checks for `NaN`.
- `Number.isNaN(value)` does not convert. It only returns `true` if the value is already the real `NaN` value.

Common mistake:

```js
isNaN("abc");        // true
Number.isNaN("abc"); // false
```

`"abc"` is not actually `NaN`. It is a string. But if converted to number, it becomes `NaN`.

## Negative Zero (`-0`)

JavaScript has both `0` and `-0`.

Original lecture example:

```js
var trendRate = -0;

console.log(trendRate === -0);
console.log(trendRate.toString());
console.log(trendRate === 0);
console.log(trendRate < 0);
console.log(trendRate > 0);
console.log(Object.is(trendRate, -0));
console.log(Object.is(trendRate, 0));
```

Output:

```text
true
"0"
true
false
false
true
false
```

Why?

- `===` treats `0` and `-0` as equal.
- `toString()` hides the negative sign and returns `"0"`.
- `-0` is not less than `0` and not greater than `0`.
- `Object.is()` can tell the difference between `0` and `-0`.

Simple check:

```js
Object.is(-0, 0);
Object.is(NaN, NaN);
```

Output:

```text
false
true
```

Why?

`Object.is()` is stricter for special values. It separates `0` from `-0`, and it treats `NaN` as the same as itself.

## Fundamental Objects

These are also called built-in objects or native functions.

Use `new` with:

```js
Object();
Array();
Function();
Date();
RegExp();
Error();
```

Avoid `new` with:

```js
String();
Number();
Boolean();
```

Why?

Without `new`, `String()`, `Number()`, and `Boolean()` usually convert values. With `new`, they create wrapper objects, which can cause confusing results.

Original lecture example:

```js
var number1 = Number(7);
console.log(number1);

var number2 = new Number(7);
console.log(number2);
```

Output:

```text
7
[Number: 7]
```

Why?

- `Number(7)` returns the primitive number `7`.
- `new Number(7)` returns a Number object that wraps `7`.

Tricky case:

```js
console.log(typeof Number(7));
console.log(typeof new Number(7));
console.log(Number(7) === 7);
console.log(new Number(7) === 7);
```

Output:

```text
"number"
"object"
true
false
```

Why?

A primitive number and a Number object are not the same kind of value.

## Common Mistakes

- Saying everything is an object. Primitive values are not objects.
- Thinking variables have fixed types. Values have types.
- Thinking `typeof null` proves `null` is an object. It is a historical bug.
- Using `typeof` to detect arrays. Use `Array.isArray()`.
- Thinking undeclared and undefined are the same. They behave differently.
- Forgetting that `let` and `const` can throw TDZ errors.
- Thinking `NaN === NaN` is true. It is false.
- Thinking `typeof NaN` is `"NaN"`. It is `"number"`.
- Using global `isNaN()` when you do not want conversion.
- Forgetting that `0 === -0` is true, but `Object.is(0, -0)` is false.
- Creating primitive wrapper objects with `new Number()`, `new String()`, or `new Boolean()`.

## Practice Questions

### Primitive Types and `typeof`

Question 1:

```js
var x = "42";
console.log(typeof x);
x = 42;
console.log(typeof x);
```

Answer:

```text
"string"
"number"
```

Reason: The variable `x` does not have one fixed type. The values have types.

Question 2:

```js
console.log(typeof null);
console.log(typeof []);
console.log(Array.isArray([]));
```

Answer:

```text
"object"
"object"
true
```

Reason: `typeof null` is a historical bug. Arrays are objects, but `Array.isArray()` detects arrays correctly.

Question 3:

```js
console.log(typeof function test() {});
```

Answer:

```text
"function"
```

Reason: Functions are callable objects, and `typeof` gives them the special result `"function"`.

### Undefined, Undeclared, and TDZ

Question 4:

```js
var a;
console.log(a);
console.log(typeof a);
```

Answer:

```text
undefined
"undefined"
```

Reason: `a` exists, but no value was assigned.

Question 5:

```js
console.log(typeof missingValue);
```

Answer:

```text
"undefined"
```

Reason: `typeof` can safely check an undeclared variable.

Question 6:

```js
console.log(missingValue);
```

Answer:

```text
ReferenceError
```

Reason: Directly reading an undeclared variable is not allowed.

Question 7:

```js
console.log(typeof level);
let level = 3;
```

Answer:

```text
ReferenceError
```

Reason: `level` is in the TDZ before the `let` declaration runs.

### `NaN`

Question 8:

```js
var result = Number("hello");
console.log(result);
console.log(typeof result);
```

Answer:

```text
NaN
"number"
```

Reason: `"hello"` cannot become a valid number, so the result is `NaN`. But `NaN` is still a number value.

Question 9:

```js
var n = NaN;
console.log(n === n);
console.log(Number.isNaN(n));
```

Answer:

```text
false
true
```

Reason: `NaN` is not equal to itself. `Number.isNaN()` correctly detects real `NaN`.

Question 10:

```js
console.log(isNaN("abc"));
console.log(Number.isNaN("abc"));
```

Answer:

```text
true
false
```

Reason: `isNaN()` converts `"abc"` to number first, which becomes `NaN`. `Number.isNaN()` does not convert strings.

### Negative Zero

Question 11:

```js
var z = -0;
console.log(z === 0);
console.log(Object.is(z, 0));
console.log(Object.is(z, -0));
```

Answer:

```text
true
false
true
```

Reason: `===` treats `0` and `-0` as equal. `Object.is()` can see the difference.

Question 12:

```js
var z = -0;
console.log(z.toString());
console.log(z < 0);
```

Answer:

```text
"0"
false
```

Reason: `toString()` hides the negative sign. `-0` is not less than `0`.

### Fundamental Objects

Question 13:

```js
console.log(typeof Number("7"));
console.log(typeof new Number("7"));
```

Answer:

```text
"number"
"object"
```

Reason: `Number("7")` creates a primitive number. `new Number("7")` creates a wrapper object.

Question 14:

```js
console.log(Number("7") === 7);
console.log(new Number("7") === 7);
```

Answer:

```text
true
false
```

Reason: The first comparison is number to number. The second is object to number with strict equality, so it is false.

## Final Exam Rules to Remember

- Use `typeof` for basic type checks, but remember its weird cases.
- `typeof null` is `"object"`.
- `typeof undeclaredVariable` is `"undefined"`.
- `typeof` with TDZ variables throws `ReferenceError`.
- Arrays need `Array.isArray()`.
- `NaN !== NaN`.
- `typeof NaN` is `"number"`.
- Prefer `Number.isNaN()` when you do not want conversion.
- Use `Object.is()` for `NaN`, `0`, and `-0` edge cases.
- Avoid `new Number()`, `new String()`, and `new Boolean()` in normal code.
