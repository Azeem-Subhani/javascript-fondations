# JavaScript Types Study Guide: `==` and `===`

This guide is based on the Types section in `lecture3.md`.

Main idea:

```text
==  allows type conversion when the two values have different types.
=== does not allow type conversion when the two values have different types.
```

But this common sentence is not enough:

```text
== checks value
=== checks value and type
```

That sentence is too simple. It can make object comparisons and corner cases confusing.

Better rule:

```text
==  uses the Abstract Equality Comparison algorithm.
=== uses the Strict Equality Comparison algorithm.
```

For output questions, do not guess by feeling. Follow the algorithm.

---

## 1. Types Matter in Equality

JavaScript values have types.

Examples of common types:

```js
console.log(typeof "Frank");
console.log(typeof 16);
console.log(typeof true);
console.log(typeof undefined);
console.log(typeof {});
console.log(typeof []);
console.log(typeof Symbol("id"));
console.log(typeof 10n);
```

Output:

```text
string
number
boolean
undefined
object
object
symbol
bigint
```

Why?

- `"Frank"` is a string.
- `16` is a number.
- `true` is a boolean.
- `{}` and `[]` are objects.
- `Symbol("id")` is a symbol.
- `10n` is a BigInt.

Important exam point:

```text
Objects are compared by reference, not by how they look.
```

Think of an object like a house address. Two houses can look the same, but if they have different addresses, they are not the same house.

---

## 2. Strict Equality: `===`

`===` uses Strict Equality.

Simple rules:

1. If the types are different, return `false`.
2. If both values are objects, return `true` only when they are the same object in memory.
3. If both values are `null`, return `true`.
4. If both values are `undefined`, return `true`.
5. If either value is `NaN`, return `false`.
6. `+0` and `-0` are treated as equal.
7. Otherwise, compare the actual values.

### 2.1 Different Types

```js
console.log("16" === 16);
console.log(false === 0);
console.log(null === undefined);
console.log([] === false);
console.log(1n === 1);
```

Output:

```text
false
false
false
false
false
```

Why?

The types are different. `===` does not convert them.

### 2.2 Same Primitive Type

```js
console.log("Frank" === "Frank");
console.log(16 === 16);
console.log(true === true);
console.log(false === false);
console.log(10n === 10n);
```

Output:

```text
true
true
true
true
true
```

Why?

The types match, and the values match.

### 2.3 Strings Must Have the Same Characters

```js
console.log("Frank" === "Frank");
console.log("Frank" === "frank");
console.log("Frank " === "Frank");
```

Output:

```text
true
false
false
```

Why?

String comparison is exact. Case and spaces matter.

### 2.4 Numbers: `NaN`, `+0`, and `-0`

```js
console.log(NaN === NaN);
console.log(+0 === -0);
console.log(0 === -0);
console.log(42 === 42);
```

Output:

```text
false
true
true
true
```

Why?

- `NaN` is never equal to `NaN` with `===`.
- `+0` and `-0` are treated as the same value by `===`.
- Same normal numbers are equal.

Common mistake:

```js
console.log(NaN === NaN);
```

Output:

```text
false
```

Why?

Use `Number.isNaN(value)` if you want to test for `NaN`.

```js
console.log(Number.isNaN(NaN));
console.log(Number.isNaN("NaN"));
```

Output:

```text
true
false
```

Why?

`Number.isNaN` checks if the value is really the special number value `NaN`.

### 2.5 `null` and `undefined` with `===`

```js
console.log(null === null);
console.log(undefined === undefined);
console.log(null === undefined);
```

Output:

```text
true
true
false
```

Why?

`null` and `undefined` are different types. `===` does not convert them.

### 2.6 Object Identity

Original lecture example:

```js
var workshop1 = {
    name: "Ali"
}

var workshop2 = {
    name: "Ali"
}

if(workshop1 == workshop2) {

}

if(workshop1 === workshop2) {

}

// the control will never enter in if block because they are not the same
// even through they look like same. they are different objects in memory.
```

Clear output version:

```js
var workshop1 = {
  name: "Ali"
};

var workshop2 = {
  name: "Ali"
};

console.log(workshop1 == workshop2);
console.log(workshop1 === workshop2);
```

Output:

```text
false
false
```

Why?

Both objects look the same, but they are two different objects in memory.

Now compare the same object reference:

```js
var workshop1 = {
  name: "Ali"
};

var workshop2 = workshop1;

console.log(workshop1 == workshop2);
console.log(workshop1 === workshop2);
```

Output:

```text
true
true
```

Why?

Both variables point to the same object.

More object examples:

```js
console.log({} === {});
console.log([] === []);

var arr = [];
var sameArr = arr;

console.log(arr === sameArr);
```

Output:

```text
false
false
true
```

Why?

- Each `{}` creates a new object.
- Each `[]` creates a new array object.
- `sameArr` points to the same array as `arr`.

### 2.7 Symbols with `===`

```js
console.log(Symbol("id") === Symbol("id"));

var id = Symbol("id");
console.log(id === id);
```

Output:

```text
false
true
```

Why?

Each call to `Symbol("id")` creates a new unique symbol. The description `"id"` does not make them equal.

### 2.8 Strict Inequality: `!==`

`!==` is the opposite result of `===`.

```js
console.log("16" !== 16);
console.log(16 !== 16);
console.log(NaN !== NaN);
```

Output:

```text
true
false
true
```

Why?

- `"16" === 16` is `false`, so `"16" !== 16` is `true`.
- `16 === 16` is `true`, so `16 !== 16` is `false`.
- `NaN === NaN` is `false`, so `NaN !== NaN` is `true`.

### Practice: `===`

Predict the output.

```js
console.log("5" === 5);
console.log(false === false);
console.log(null === undefined);
console.log(+0 === -0);
console.log(NaN === NaN);

var a = [1, 2];
var b = [1, 2];
var c = a;

console.log(a === b);
console.log(a === c);
```

Answer:

```text
false
true
false
true
false
false
true
```

Reasoning:

- `"5"` is a string, but `5` is a number.
- `false` and `false` are the same boolean value.
- `null` and `undefined` are different types.
- `+0` and `-0` are equal with `===`.
- `NaN` is not equal to itself with `===`.
- `a` and `b` are two different arrays.
- `c` points to the same array as `a`.

---

## 3. Loose Equality: `==`

`==` uses Abstract Equality.

Important:

```text
If both values already have the same type, == behaves like ===.
```

This is why `==` and `===` often give the same answer.

Original lecture example:

```js
var studentName1 = "Frank";
var studentName2 = `${studentName1}`;

var workshopEnrollment1 = 16;
var workshopEnrollment2 = workshopEnrollment1 + 0;

studentName1 == studentName2; // true
studentName1 === studentName2; // true

workshopEnrollment1 == workshopEnrollment2; // true
workshopEnrollment1 === workshopEnrollment2; // true
```

Output:

```text
true
true
true
true
```

Why?

- `studentName1` and `studentName2` are both strings with the same characters.
- `workshopEnrollment1` and `workshopEnrollment2` are both numbers with the same value.
- When the types match, `==` uses the same result as `===`.

### 3.1 Same Type with `==`

```js
console.log("Ali" == "Ali");
console.log(10 == 10);
console.log(true == true);
console.log(null == null);
console.log(undefined == undefined);
```

Output:

```text
true
true
true
true
true
```

Why?

Same type, same value.

```js
console.log("Ali" == "ali");
console.log(10 == 11);
console.log(true == false);
console.log(NaN == NaN);
```

Output:

```text
false
false
false
false
```

Why?

Same type comparisons use strict equality rules. `NaN` is still not equal to itself.

### Practice: Same Type with `==`

Predict the output.

```js
console.log("JS" == "JS");
console.log("JS" == "Js");
console.log(0 == -0);
console.log(NaN == NaN);
```

Answer:

```text
true
false
true
false
```

Reasoning:

- Same strings, same characters: `true`.
- Case is different: `false`.
- `0` and `-0` are equal.
- `NaN` is never equal to `NaN` with `==` or `===`.

---

## 4. `null` and `undefined` with `==`

Special loose equality rule:

```text
null == undefined is true.
undefined == null is true.
```

But `null` and `undefined` are not loosely equal to other values.

```js
console.log(null == undefined);
console.log(undefined == null);

console.log(null == 0);
console.log(null == false);
console.log(undefined == 0);
console.log(undefined == false);
console.log(undefined == "");
```

Output:

```text
true
true
false
false
false
false
false
```

Why?

`null` and `undefined` are only loosely equal to each other.

Common useful pattern:

```js
var value = null;

console.log(value == null);
```

Output:

```text
true
```

Why?

`value == null` is true when `value` is either `null` or `undefined`.

```js
var value;

console.log(value == null);
```

Output:

```text
true
```

Why?

An unassigned variable has the value `undefined`, and `undefined == null` is true.

With `===`, this is different:

```js
var value;

console.log(value === null);
console.log(value === undefined);
```

Output:

```text
false
true
```

Why?

`===` does not combine `null` and `undefined`.

### Practice: `null` and `undefined`

Predict the output.

```js
console.log(null == undefined);
console.log(null === undefined);
console.log(undefined == false);
console.log(null == "");

var x;
console.log(x == null);
console.log(x === null);
```

Answer:

```text
true
false
false
false
true
false
```

Reasoning:

- `null == undefined` is a special `==` rule.
- `===` sees different types.
- `undefined` is not loosely equal to `false`.
- `null` is not loosely equal to an empty string.
- `x` is `undefined`, so `x == null` is true.
- But `x === null` is false.

---

## 5. String and Number with `==`

Special loose equality rule:

```text
If one value is a number and the other is a string,
JavaScript converts the string to a number.
```

### 5.1 Simple Numeric Strings

```js
console.log("16" == 16);
console.log("0" == 0);
console.log("-5" == -5);
console.log("3.5" == 3.5);
```

Output:

```text
true
true
true
true
```

Why?

The strings can be converted to the same numbers.

### 5.2 Empty String and Spaces

```js
console.log("" == 0);
console.log(" " == 0);
console.log("\n" == 0);
```

Output:

```text
true
true
true
```

Why?

When converted to number:

```text
Number("")   -> 0
Number(" ")  -> 0
Number("\n") -> 0
```

This is a common exam trap.

### 5.3 Non-Numeric Strings

```js
console.log("hello" == 0);
console.log("NaN" == NaN);
console.log("abc" == 123);
```

Output:

```text
false
false
false
```

Why?

These strings become `NaN` when converted to number. Any equality comparison with `NaN` returns `false`.

### 5.4 String and Number with `===`

```js
console.log("16" === 16);
console.log("" === 0);
console.log(" " === 0);
```

Output:

```text
false
false
false
```

Why?

`===` does not convert strings to numbers.

### Practice: String and Number

Predict the output.

```js
console.log("5" == 5);
console.log("5" === 5);
console.log("" == 0);
console.log(" " == 0);
console.log("abc" == 0);
console.log("abc" == NaN);
```

Answer:

```text
true
false
true
true
false
false
```

Reasoning:

- `"5"` becomes `5` for `==`.
- `===` does not convert.
- `""` and `" "` become `0`.
- `"abc"` becomes `NaN`.
- `NaN` is not equal to anything, even itself.

---

## 6. Boolean with `==`

Special loose equality rule:

```text
If one value is a boolean, JavaScript converts the boolean to a number.
true  -> 1
false -> 0
```

Important:

```text
== does not convert both sides to boolean.
```

That is one of the biggest student mistakes.

### 6.1 Boolean and Number

```js
console.log(true == 1);
console.log(false == 0);
console.log(true == 2);
console.log(false == 1);
```

Output:

```text
true
true
false
false
```

Why?

`true` becomes `1`. `false` becomes `0`.

### 6.2 Boolean and String

```js
console.log(true == "1");
console.log(false == "0");
console.log(false == "");
console.log(true == "true");
```

Output:

```text
true
true
true
false
```

Why?

Follow the steps:

```text
true == "1"
1 == "1"
1 == 1
true
```

```text
false == "0"
0 == "0"
0 == 0
true
```

```text
false == ""
0 == ""
0 == 0
true
```

```text
true == "true"
1 == "true"
1 == NaN
false
```

Common mistake:

```js
console.log(Boolean("false"));
console.log("false" == false);
```

Output:

```text
true
false
```

Why?

`Boolean("false")` is true because non-empty strings are truthy.

But `"false" == false` does not convert `"false"` to boolean. It converts `false` to `0`, then converts `"false"` to `NaN`.

### 6.3 Boolean with `===`

```js
console.log(true === 1);
console.log(false === 0);
console.log(false === "");
```

Output:

```text
false
false
false
```

Why?

Different types. No conversion.

### Practice: Boolean Equality

Predict the output.

```js
console.log(true == "1");
console.log(true == "true");
console.log(false == "");
console.log(false == "false");
console.log(Boolean("false"));
```

Answer:

```text
true
false
true
false
true
```

Reasoning:

- `true` becomes `1`, and `"1"` becomes `1`.
- `"true"` becomes `NaN`.
- `false` becomes `0`, and `""` becomes `0`.
- `"false"` becomes `NaN`.
- Any non-empty string is truthy when converted with `Boolean(...)`.

---

## 7. Object and Primitive with `==`

Special loose equality rule:

```text
If one side is an object and the other side is a primitive,
JavaScript converts the object to a primitive first.
```

Primitive values include:

- string
- number
- boolean
- symbol
- bigint
- `null`
- `undefined`

Objects include:

- arrays
- plain objects
- functions
- wrapper objects like `new Number(3)`

### 7.1 Arrays Convert to Strings First in Many Cases

For many arrays:

```text
[]       -> ""
[1]      -> "1"
[1, 2]   -> "1,2"
[null]   -> ""
[undefined] -> ""
```

Examples:

```js
console.log([] == "");
console.log([] == 0);
console.log([1] == "1");
console.log([1] == 1);
console.log([1, 2] == "1,2");
console.log([1, 2] == 12);
```

Output:

```text
true
true
true
true
true
false
```

Why?

```text
[] == ""
"" == ""
true
```

```text
[] == 0
"" == 0
0 == 0
true
```

```text
[1] == 1
"1" == 1
1 == 1
true
```

```text
[1, 2] == 12
"1,2" == 12
NaN == 12
false
```

### 7.2 Plain Objects

```js
console.log(({}) == "[object Object]");
console.log(({}) == {});
```

Output:

```text
true
false
```

Why?

First example:

```text
({}) becomes "[object Object]"
"[object Object]" == "[object Object]"
true
```

Second example:

```text
({}) == {}
```

Both sides are objects. Since they are different objects in memory, the result is `false`.

### 7.3 Wrapper Objects

```js
console.log(new Number(3) == 3);
console.log(new String("Ali") == "Ali");
console.log(new Boolean(false) == false);

console.log(new Number(3) === 3);
console.log(new String("Ali") === "Ali");
console.log(new Boolean(false) === false);
```

Output:

```text
true
true
true
false
false
false
```

Why?

With `==`, wrapper objects convert to their primitive values.

With `===`, object and primitive are different types.

Very tricky:

```js
var flag = new Boolean(false);

console.log(flag == false);
console.log(flag === false);

if (flag) {
  console.log("runs");
}
```

Output:

```text
true
false
runs
```

Why?

- `flag == false`: the object converts to the primitive `false`.
- `flag === false`: object and boolean are different types.
- `if (flag)`: all objects are truthy, even `new Boolean(false)`.

### Practice: Object and Primitive

Predict the output.

```js
console.log([] == 0);
console.log([] === 0);
console.log([2] == 2);
console.log([2] == "2");
console.log([2, 3] == "2,3");
console.log({} == {});

var obj = {};
var sameObj = obj;
console.log(obj == sameObj);
```

Answer:

```text
true
false
true
true
true
false
true
```

Reasoning:

- `[]` becomes `""`, and `""` becomes `0`.
- `===` does not convert an array to a number.
- `[2]` becomes `"2"`.
- `[2, 3]` becomes `"2,3"`.
- Two different object literals are not the same object.
- `sameObj` references the same object as `obj`.

---

## 8. Symbol with `==`

Symbols are special primitive values.

Same symbol reference:

```js
var id = Symbol("id");

console.log(id == id);
console.log(id === id);
```

Output:

```text
true
true
```

Why?

Both sides are the exact same symbol.

Different symbols:

```js
console.log(Symbol("id") == Symbol("id"));
console.log(Symbol("id") === Symbol("id"));
```

Output:

```text
false
false
```

Why?

Each `Symbol("id")` creates a new unique symbol.

Symbol compared with non-symbol:

```js
console.log(Symbol("id") == "id");
console.log(Symbol("id") == true);
console.log(Symbol("id") == 1);
```

Output:

```text
false
false
false
```

Why?

A symbol is not loosely equal to a normal string, boolean, or number.

Object wrapper around the same symbol:

```js
var id = Symbol("id");
var wrapped = Object(id);

console.log(wrapped == id);
console.log(wrapped === id);
```

Output:

```text
true
false
```

Why?

With `==`, the object wrapper converts back to the symbol primitive.

With `===`, object and symbol are different types.

---

## 9. BigInt with `==` and `===`

BigInt is for integer values that can be very large.

```js
console.log(typeof 1n);
```

Output:

```text
bigint
```

### 9.1 BigInt and Number

```js
console.log(1n == 1);
console.log(1n === 1);
console.log(2n == 2);
console.log(2n == 2.5);
console.log(1n == NaN);
console.log(1n == Infinity);
```

Output:

```text
true
false
true
false
false
false
```

Why?

- `1n == 1` compares their mathematical value.
- `1n === 1` is false because BigInt and Number are different types.
- BigInt cannot equal `NaN` or `Infinity`.
- `2n` is not equal to `2.5`.

### 9.2 BigInt and String

```js
console.log(1n == "1");
console.log(1n == "01");
console.log(1n == "1.0");
console.log(1n == "hello");
```

Output:

```text
true
true
false
false
```

Why?

For `==`, JavaScript tries to convert the string to a BigInt.

- `"1"` can become `1n`.
- `"01"` can become `1n`.
- `"1.0"` cannot become a BigInt because BigInt must be an integer format.
- `"hello"` cannot become a BigInt.

### 9.3 BigInt and Boolean

```js
console.log(1n == true);
console.log(0n == false);
console.log(1n === true);
console.log(0n === false);
```

Output:

```text
true
true
false
false
```

Why?

With `==`, booleans convert to numbers:

```text
true  -> 1
false -> 0
```

Then JavaScript compares the mathematical value with BigInt.

With `===`, the types are different.

### Practice: BigInt

Predict the output.

```js
console.log(5n == 5);
console.log(5n === 5);
console.log(5n == "5");
console.log(5n == "5.0");
console.log(0n == false);
```

Answer:

```text
true
false
true
false
true
```

Reasoning:

- BigInt and Number can be loosely equal if their mathematical values match.
- `===` rejects different types.
- `"5"` can convert to `5n`.
- `"5.0"` cannot convert to BigInt.
- `false` becomes `0`, and `0n == 0` is true.

---

## 10. The Famous Corner Case: `[] == ![]`

Original lecture corner case:

```js
[] == ![] // true
```

Clear output version:

```js
console.log([] == ![]);
```

Output:

```text
true
```

Why?

Step by step:

```text
[] == ![]
```

First, evaluate `![]`.

```text
[] is truthy
![] is false
```

So now:

```text
[] == false
```

Boolean converts to number:

```text
false -> 0
[] == 0
```

Array converts to primitive:

```text
[] -> ""
"" == 0
```

String converts to number:

```text
"" -> 0
0 == 0
true
```

Final result:

```text
true
```

Common mistake:

```text
Students think [] is false.
```

But:

```js
console.log(Boolean([]));
```

Output:

```text
true
```

Why?

All objects are truthy. Arrays are objects.

### More Array Corner Cases

```js
console.log([] == false);
console.log([] == true);
console.log([0] == false);
console.log([1] == true);
console.log([2] == true);
console.log([null] == 0);
console.log([undefined] == 0);
```

Output:

```text
true
false
true
true
false
true
true
```

Why?

```text
[] == false
[] == 0
"" == 0
0 == 0
true
```

```text
[] == true
[] == 1
"" == 1
0 == 1
false
```

```text
[0] == false
"0" == 0
0 == 0
true
```

```text
[1] == true
"1" == 1
1 == 1
true
```

```text
[2] == true
"2" == 1
2 == 1
false
```

```text
[null] -> ""
[undefined] -> ""
"" == 0
true
```

### Practice: `[] == ![]`

Predict the output.

```js
console.log(Boolean([]));
console.log(![]);
console.log([] == false);
console.log([] == true);
console.log([] == ![]);
```

Answer:

```text
true
false
true
false
true
```

Reasoning:

- `[]` is an object, so it is truthy.
- `![]` is `false`.
- `[] == false` becomes `0 == 0`.
- `[] == true` becomes `0 == 1`.
- `[] == ![]` becomes `[] == false`, then `0 == 0`.

---

## 11. Truthiness Is Not the Same as `== true`

Original lecture example:

```js
var workshop = []

if(workshop) { // true

}

if(workshop == true) { // false

}

if(workshop == false) { // true

}

// explain the above code why?
```

Clear output version:

```js
var workshop = [];

if (workshop) {
  console.log("if workshop");
}

if (workshop == true) {
  console.log("workshop == true");
}

if (workshop == false) {
  console.log("workshop == false");
}
```

Output:

```text
if workshop
workshop == false
```

Why?

First `if`:

```text
if (workshop)
if ([])
```

Arrays are objects. Objects are truthy. So the first block runs.

Second `if`:

```text
workshop == true
[] == true
[] == 1
"" == 1
0 == 1
false
```

So the second block does not run.

Third `if`:

```text
workshop == false
[] == false
[] == 0
"" == 0
0 == 0
true
```

So the third block runs.

Very important:

```text
if (value) checks truthiness.
value == true uses the == algorithm.
They are not the same thing.
```

### Truthy and Falsy Values

Falsy values:

```text
false
0
-0
0n
""
null
undefined
NaN
```

Most other values are truthy.

Examples:

```js
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean("false"));
console.log(Boolean("0"));
console.log(Boolean(0));
console.log(Boolean(""));
```

Output:

```text
true
true
true
true
false
false
```

Why?

- Arrays and objects are truthy.
- Non-empty strings are truthy, even `"false"` and `"0"`.
- `0` and `""` are falsy.

Now compare with `==`:

```js
console.log("0" == false);
console.log("false" == false);
console.log([] == false);
console.log({} == false);
```

Output:

```text
true
false
true
false
```

Why?

- `"0" == false`: `false -> 0`, `"0" -> 0`, so true.
- `"false" == false`: `"false" -> NaN`, so false.
- `[] == false`: `[] -> "" -> 0`, so true.
- `{}` becomes `"[object Object]"`, which becomes `NaN`, so false.

### Practice: Truthiness vs Equality

Predict the output.

```js
var a = [];
var b = "0";
var c = "false";

console.log(Boolean(a));
console.log(a == false);

console.log(Boolean(b));
console.log(b == false);

console.log(Boolean(c));
console.log(c == false);
```

Answer:

```text
true
true
true
true
true
false
```

Reasoning:

- `[]` is truthy, but `[] == false` becomes `0 == 0`.
- `"0"` is a non-empty string, so it is truthy, but `"0" == false` becomes `0 == 0`.
- `"false"` is a non-empty string, so it is truthy, but `"false" == false` becomes `NaN == 0`.

---

## 12. Full `==` Algorithm in Simple English

Use this order for output questions.

### Rule 1: Same Type

If both values have the same type:

```text
Use strict equality rules.
```

Example:

```js
console.log("a" == "a");
console.log(NaN == NaN);
```

Output:

```text
true
false
```

Why?

Same type means `==` acts like `===`.

### Rule 2: `null` and `undefined`

```js
console.log(null == undefined);
console.log(null == 0);
```

Output:

```text
true
false
```

Why?

`null` and `undefined` are only loosely equal to each other.

### Rule 3: Number and String

```js
console.log(10 == "10");
console.log(0 == "");
console.log(0 == "hello");
```

Output:

```text
true
true
false
```

Why?

The string converts to a number.

### Rule 4: Boolean

```js
console.log(true == 1);
console.log(false == 0);
console.log(true == "true");
```

Output:

```text
true
true
false
```

Why?

The boolean converts to number first.

### Rule 5: Object and Primitive

```js
console.log([1] == 1);
console.log([] == 0);
console.log({} == {});
```

Output:

```text
true
true
false
```

Why?

Object plus primitive means convert the object to a primitive. But object plus object means compare references.

### Rule 6: Symbol

```js
console.log(Symbol("x") == "x");
```

Output:

```text
false
```

Why?

A symbol is not loosely equal to a non-symbol primitive.

### Rule 7: BigInt with Number or String

```js
console.log(1n == 1);
console.log(1n == "1");
console.log(1n == "1.5");
```

Output:

```text
true
true
false
```

Why?

BigInt can loosely compare with a number or a string when the mathematical integer value matches.

### Rule 8: Otherwise

If no rule creates equality, return `false`.

```js
console.log(false == null);
console.log(undefined == "");
console.log(Symbol("x") == 1);
```

Output:

```text
false
false
false
```

Why?

No valid loose equality rule makes these equal.

---

## 13. `!=` and `!==`

The notes mention equality operators:

```text
== and !=
=== and !==
```

Rules:

```text
a != b  is the opposite of a == b
a !== b is the opposite of a === b
```

Examples:

```js
console.log(1 == "1");
console.log(1 != "1");

console.log(1 === "1");
console.log(1 !== "1");
```

Output:

```text
true
false
false
true
```

Why?

- `1 == "1"` is true because `"1"` converts to `1`.
- So `1 != "1"` is false.
- `1 === "1"` is false because types are different.
- So `1 !== "1"` is true.

More examples:

```js
console.log(null != undefined);
console.log(null !== undefined);
console.log(NaN != NaN);
console.log(NaN !== NaN);
```

Output:

```text
false
true
true
true
```

Why?

- `null == undefined` is true, so `null != undefined` is false.
- `null === undefined` is false, so `null !== undefined` is true.
- `NaN == NaN` and `NaN === NaN` are both false.
- So both `NaN != NaN` and `NaN !== NaN` are true.

### Practice: Not Equal Operators

Predict the output.

```js
console.log("2" != 2);
console.log("2" !== 2);
console.log(false != 0);
console.log(false !== 0);
console.log([] != false);
```

Answer:

```text
false
true
false
true
false
```

Reasoning:

- `"2" == 2` is true, so `"2" != 2` is false.
- `"2" === 2` is false, so `"2" !== 2` is true.
- `false == 0` is true, so `false != 0` is false.
- `false === 0` is false, so `false !== 0` is true.
- `[] == false` is true, so `[] != false` is false.

---

## 14. Common Mistakes

### Mistake 1: Thinking `==` Converts Both Sides to Boolean

Wrong thinking:

```text
[] is truthy, so [] == true should be true.
```

Actual result:

```js
console.log([] == true);
```

Output:

```text
false
```

Why?

`==` does not ask, "Are both values truthy?" It follows the equality algorithm.

```text
[] == true
[] == 1
"" == 1
0 == 1
false
```

### Mistake 2: Thinking Two Similar Objects Are Equal

```js
console.log({ name: "Ali" } == { name: "Ali" });
console.log({ name: "Ali" } === { name: "Ali" });
```

Output:

```text
false
false
```

Why?

They are different objects in memory.

### Mistake 3: Forgetting `NaN`

```js
console.log(NaN == NaN);
console.log(NaN === NaN);
```

Output:

```text
false
false
```

Why?

`NaN` is never equal to itself using `==` or `===`.

### Mistake 4: Mixing Truthiness with Equality

```js
console.log(Boolean("0"));
console.log("0" == false);
```

Output:

```text
true
true
```

Why?

`"0"` is truthy as a boolean. But with `==`, it converts to number `0`.

### Mistake 5: Forgetting That `+0` and `-0` Are Equal

```js
console.log(+0 === -0);
console.log(+0 == -0);
```

Output:

```text
true
true
```

Why?

Both `==` and `===` treat `+0` and `-0` as equal.

If you need to tell them apart:

```js
console.log(Object.is(+0, -0));
console.log(Object.is(NaN, NaN));
```

Output:

```text
false
true
```

Why?

`Object.is` uses a different comparison rule. It can tell `+0` and `-0` apart, and it treats `NaN` as the same as `NaN`.

---

## 15. Quick Decision Checklist for Output Questions

When you see `a == b`, ask:

1. Are the types the same?
   - Use strict equality rules.
2. Is one `null` and the other `undefined`?
   - Return `true`.
3. Is one value `null` or `undefined` and the other is something else?
   - Return `false`.
4. Is one value a boolean?
   - Convert boolean to number.
5. Is one value a string and the other a number?
   - Convert string to number.
6. Is one value an object and the other a primitive?
   - Convert object to primitive.
7. Is BigInt involved?
   - Compare mathematical integer value if possible.
8. Is Symbol involved with a different primitive type?
   - Usually `false`.
9. Still no match?
   - Return `false`.

When you see `a === b`, ask:

1. Are the types different?
   - Return `false`.
2. Are both objects?
   - Return `true` only if they are the same object reference.
3. Is either value `NaN`?
   - Return `false`.
4. Are they same primitive values?
   - Return `true`.

---

## 16. Mixed Exam Practice

Predict the output first. Then check the answer.

### Question 1

```js
console.log(0 == false);
console.log(0 === false);
console.log("0" == false);
console.log("0" === false);
```

Answer:

```text
true
false
true
false
```

Reasoning:

- `false` becomes `0` with `==`.
- `===` rejects number vs boolean.
- `"0"` becomes number `0` after `false` becomes `0`.
- `===` rejects string vs boolean.

### Question 2

```js
console.log("" == false);
console.log(" " == false);
console.log("false" == false);
console.log(Boolean("false"));
```

Answer:

```text
true
true
false
true
```

Reasoning:

- `false` becomes `0`.
- `""` and `" "` become `0`.
- `"false"` becomes `NaN`.
- `"false"` is a non-empty string, so it is truthy.

### Question 3

```js
console.log([] == 0);
console.log([] == "");
console.log([] === "");
console.log([] == false);
console.log([] === false);
```

Answer:

```text
true
true
false
true
false
```

Reasoning:

- `[]` converts to `""`.
- `"" == 0` is true because `""` becomes `0`.
- `===` does not convert array to string or boolean.
- `[] == false` becomes `0 == 0`.

### Question 4

```js
console.log([1] == 1);
console.log([1] === 1);
console.log([1] == true);
console.log([2] == true);
```

Answer:

```text
true
false
true
false
```

Reasoning:

- `[1]` becomes `"1"`, then number `1`.
- Array and number are different types with `===`.
- `true` becomes `1`, so `[1] == true` becomes `1 == 1`.
- `[2] == true` becomes `2 == 1`.

### Question 5

```js
var x = {};
var y = {};
var z = x;

console.log(x == y);
console.log(x === y);
console.log(x == z);
console.log(x === z);
```

Answer:

```text
false
false
true
true
```

Reasoning:

- `x` and `y` are different objects.
- `z` points to the same object as `x`.

### Question 6

```js
console.log(null == undefined);
console.log(null === undefined);
console.log(null == false);
console.log(undefined == false);
```

Answer:

```text
true
false
false
false
```

Reasoning:

- `null == undefined` is a special rule.
- `===` sees different types.
- `null` and `undefined` are not loosely equal to `false`.

### Question 7

```js
console.log(NaN == NaN);
console.log(NaN === NaN);
console.log(NaN != NaN);
console.log(Number.isNaN(NaN));
```

Answer:

```text
false
false
true
true
```

Reasoning:

- `NaN` is not equal to itself with `==` or `===`.
- So `NaN != NaN` is true.
- `Number.isNaN(NaN)` correctly checks for `NaN`.

### Question 8

```js
console.log(1n == 1);
console.log(1n === 1);
console.log(1n == "1");
console.log(1n == true);
```

Answer:

```text
true
false
true
true
```

Reasoning:

- `1n` and `1` have the same mathematical value for `==`.
- `===` rejects BigInt vs Number.
- `"1"` can convert to `1n`.
- `true` becomes `1`, and `1n == 1` is true.

### Question 9

```js
var value = new Boolean(false);

console.log(Boolean(value));
console.log(value == false);
console.log(value === false);
```

Answer:

```text
true
true
false
```

Reasoning:

- `value` is an object, and all objects are truthy.
- With `==`, the object converts to primitive `false`.
- With `===`, object and boolean are different types.

### Question 10

```js
console.log([] == ![]);
console.log([0] == ![]);
console.log([1] == ![]);
```

Answer:

```text
true
true
false
```

Reasoning:

- `![]` is `false`.
- `[] == false` becomes `0 == 0`.
- `[0] == false` becomes `"0" == 0`, then `0 == 0`.
- `[1] == false` becomes `"1" == 0`, then `1 == 0`.

---

## 17. Final Summary

Use this mental model:

```text
=== asks: Are these the same type and same value/reference?

== asks: Can these become equal after following JavaScript's conversion rules?
```

Most important rules to remember:

- Same type: `==` behaves like `===`.
- `null == undefined` is true.
- `null` and `undefined` are not loosely equal to other normal values.
- Booleans convert to numbers in `==`.
- Strings can convert to numbers in `==`.
- Objects compared with primitives convert to primitives.
- Objects compared with objects are compared by reference.
- `NaN` is never equal to itself with `==` or `===`.
- `+0` and `-0` are equal with `==` and `===`.
- Truthiness is not the same as `== true`.

Best exam habit:

```text
Never solve == questions by intuition.
Write the conversion steps.
```
