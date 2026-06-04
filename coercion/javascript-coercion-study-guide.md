# JavaScript Abstract Operations and Coercion

## Overview

This guide covers the ideas from `lecture2.md`.

Main idea: JavaScript often changes a value from one type to another. This is called **coercion**.

Example:

```js
var numStudents = 16;

console.log(`There are ${numStudents} students.`);
```

Output:

```text
There are 16 students.
```

Why?

`numStudents` is a number, but the template literal needs text. JavaScript converts `16` into `"16"` behind the scenes.

## Quick Rules

- Abstract operations are internal JavaScript rules.
- You cannot directly call abstract operations like `ToPrimitive`, `ToString`, `ToNumber`, or `ToBoolean`.
- `ToPrimitive` turns objects into primitive values.
- `ToString` turns values into strings.
- `ToNumber` turns values into numbers.
- `ToBoolean` turns values into `true` or `false`.
- The `+` operator can do number addition or string concatenation.
- The `-`, `<`, and `>` operators usually force number conversion.
- Objects are truthy, even if they wrap a falsy value.

## Abstract Operations

An **abstract operation** is an internal algorithm inside JavaScript.

It is part of the JavaScript specification, but it is not a normal function you can call.

Think of it like a hidden helper rule. JavaScript uses it when it needs to convert a value.

Example:

```js
console.log("5" - 2);
```

Output:

```text
3
```

Why?

The `-` operator works with numbers. JavaScript uses the internal `ToNumber` operation:

```js
Number("5") - 2;
5 - 2;
3;
```

### Common Mistake

Mistake:

```js
ToNumber("5");
```

Output:

```text
ReferenceError
```

Why?

`ToNumber` is not a real function in your code. It is an internal rule. In normal code, you can use `Number("5")` to see a similar conversion.

### Practice

Question 1:

```js
console.log("10" - 3);
```

Answer:

```text
7
```

Reason: `-` needs numbers, so `"10"` becomes `10`.

Question 2:

```js
console.log("10" + 3);
```

Answer:

```text
103
```

Reason: With `+`, if one side is a string, JavaScript uses string concatenation.

## `ToPrimitive`

`ToPrimitive` turns an object into a primitive value.

Primitive values include:

- string
- number
- boolean
- `null`
- `undefined`
- symbol
- bigint

Objects and arrays are not primitive values.

JavaScript uses `ToPrimitive` when an object is used in a place that needs a primitive.

Example:

```js
var obj = {
  valueOf() {
    return 10;
  }
};

console.log(obj - 3);
```

Output:

```text
7
```

Why?

The `-` operator needs numbers. JavaScript first needs a primitive from `obj`.

It calls `obj.valueOf()`, gets `10`, then does:

```js
10 - 3;
```

### The Three Hints

`ToPrimitive` uses a **hint**. The hint tells JavaScript what kind of primitive would be useful.

| Hint | Meaning | Common example |
| --- | --- | --- |
| `"number"` | JavaScript expects a number | `obj - 5` |
| `"string"` | JavaScript expects text | `` `${obj}` `` |
| `"default"` | JavaScript is not sure yet | `obj + 5` |

### `valueOf()` and `toString()`

Most objects have these two methods:

- `valueOf()` tries to return the main value of the object.
- `toString()` tries to return a string version of the object.

For many plain objects:

```js
var obj = {};

console.log(obj.valueOf());
console.log(obj.toString());
```

Output:

```text
{}
[object Object]
```

Why?

`valueOf()` gives the object itself. That is not primitive. `toString()` gives the default string `"[object Object]"`.

### Hint Order

For a `"number"` hint:

1. Try `valueOf()` first.
2. If it does not return a primitive, try `toString()`.
3. If neither returns a primitive, throw `TypeError`.

For a `"string"` hint:

1. Try `toString()` first.
2. If it does not return a primitive, try `valueOf()`.
3. If neither returns a primitive, throw `TypeError`.

### Original Lecture Example: Wallet

```js
let wallet = {
  money: 100,

  valueOf() {
    return this.money;
  },

  toString() {
    return `Wallet has $${this.money}`;
  }
};

console.log(wallet - 20);
console.log(`Status: ${wallet}`);
```

Output:

```text
80
Status: Wallet has $100
```

Why?

For `wallet - 20`:

```js
wallet - 20;
100 - 20;
80;
```

The `-` operator asks for a number-like primitive. `valueOf()` runs first and returns `100`.

For the template literal:

```js
`Status: ${wallet}`;
```

JavaScript wants a string. It calls `toString()` first and gets `"Wallet has $100"`.

### When `valueOf()` Does Not Help

```js
var user = {
  name: "Ali",

  valueOf() {
    return this;
  },

  toString() {
    return this.name;
  }
};

console.log(user + " logged in");
```

Output:

```text
Ali logged in
```

Why?

For `+`, JavaScript needs primitive values first. `valueOf()` returns the object, so JavaScript cannot use it. Then `toString()` returns `"Ali"`.

### If Both Methods Fail

```js
var broken = {
  valueOf() {
    return {};
  },

  toString() {
    return {};
  }
};

console.log(broken - 1);
```

Output:

```text
TypeError
```

Why?

Both methods return objects. JavaScript needed a primitive value, so it throws an error.

Important exam rule: the final result of `ToPrimitive` must be a primitive.

### Practice

Question 1:

```js
var score = {
  valueOf() {
    return 20;
  },
  toString() {
    return "score";
  }
};

console.log(score - 5);
```

Answer:

```text
15
```

Reason: `-` asks for a number-like primitive. `valueOf()` returns `20`.

Question 2:

```js
var score = {
  valueOf() {
    return 20;
  },
  toString() {
    return "score";
  }
};

console.log(`Result: ${score}`);
```

Answer:

```text
Result: score
```

Reason: Template literals need strings. `toString()` runs first.

Question 3:

```js
var item = {
  valueOf() {
    return {};
  },
  toString() {
    return "5";
  }
};

console.log(item - 2);
```

Answer:

```text
3
```

Reason: `valueOf()` returns an object, so JavaScript tries `toString()`. `"5"` becomes number `5` for subtraction.

## `ToString`

`ToString` turns a value into a string.

You see this when:

- you use `String(value)`
- you use a template literal
- you use `+` with a string
- an object is used where text is needed

Example:

```js
console.log(String(123));
console.log(String(true));
console.log(String(null));
```

Output:

```text
123
true
null
```

Why?

Each value is converted to its string form.

### `ToString` With Objects

When an object must become a string:

1. JavaScript calls `ToPrimitive` with a `"string"` hint.
2. It tries `toString()` first.
3. If needed, it tries `valueOf()`.

Example:

```js
var person = {
  toString() {
    return "Sara";
  }
};

console.log(String(person));
```

Output:

```text
Sara
```

Why?

`String(person)` needs text. JavaScript calls `person.toString()` and uses `"Sara"`.

### Important Mix-up: `toString([])` vs `[].toString()`

The lecture notes mention this example:

```js
console.log(toString([]));
```

Output in many environments:

```text
[object Undefined]
```

Why?

This is not calling the array method. It is calling a bare/global `toString` function. The argument `[]` is not used the way students often expect.

Use this instead:

```js
console.log([].toString());
console.log(String([]));
```

Output:

```text
(empty line)
(empty line)
```

Why?

An empty array becomes an empty string. `console.log` prints a blank line for each empty string.

### Arrays and `toString()`

Arrays turn into comma-separated text.

```js
console.log([1, 2, 3].toString());
console.log([null, undefined].toString());
console.log([[], [], [], []].toString());
console.log([,,,,].toString());
```

Output:

```text
1,2,3
,
,,,
,,,
```

Why?

- `[1, 2, 3]` becomes `"1,2,3"`.
- `null` and `undefined` inside arrays become empty spaces.
- Empty arrays inside arrays become empty strings.
- Empty slots also become empty strings.

Think of array string conversion like joining items with commas.

### Plain Objects and `toString()`

```js
console.log(({}).toString());
console.log(String({}));
```

Output:

```text
[object Object]
[object Object]
```

Why?

Plain objects do not automatically print their properties. The default object string is `"[object Object]"`.

### Original Lecture Corner Cases

```js
console.log(String(-0));
console.log(String(null));
console.log(String(undefined));
console.log(String([null]));
console.log(String([undefined]));
```

Output:

```text
0
null
undefined
(empty line)
(empty line)
```

Why?

- `String(-0)` hides the minus sign and returns `"0"`.
- `null` becomes `"null"`.
- `undefined` becomes `"undefined"`.
- `[null]` and `[undefined]` become empty strings because array `toString()` treats those values as empty.

### Practice

Question 1:

```js
console.log(String([1, 2, 3]));
```

Answer:

```text
1,2,3
```

Reason: Array string conversion joins items with commas.

Question 2:

```js
console.log(String([null, undefined]));
```

Answer:

```text
,
```

Reason: `null` and `undefined` inside arrays become empty strings. The comma remains.

Question 3:

```js
console.log(String({ name: "Ali" }));
```

Answer:

```text
[object Object]
```

Reason: A plain object uses the default object string.

Question 4:

```js
console.log(String(-0));
```

Answer:

```text
0
```

Reason: String conversion hides the negative sign of `-0`.

## `ToNumber`

`ToNumber` turns a value into a number.

You see this when:

- you use `Number(value)`
- you use `-`, `*`, `/`, or `%`
- you use comparison operators like `<` and `>`
- JavaScript needs numeric behavior

Example:

```js
console.log("8" - 3);
```

Output:

```text
5
```

Why?

The `-` operator needs numbers. `"8"` becomes `8`.

### String to Number

Original lecture examples:

```js
console.log(Number(""));
console.log(Number("0"));
console.log(Number("-0"));
console.log(Number("009"));
console.log(Number("3.14159"));
console.log(Number("0."));
console.log(Number(".0"));
console.log(Number("."));
console.log(Number("0xaf"));
```

Output:

```text
0
0
-0
9
3.14159
0
0
NaN
175
```

Why?

- `""` becomes `0`.
- `"0"` becomes `0`.
- `"-0"` becomes negative zero.
- `"009"` becomes `9`.
- `"3.14159"` becomes `3.14159`.
- `"0."` and `".0"` are valid number forms.
- `"."` is not a valid number, so it becomes `NaN`.
- `"0xaf"` is hexadecimal, so it becomes decimal `175`.

### Boolean, `null`, and `undefined` to Number

```js
console.log(Number(false));
console.log(Number(true));
console.log(Number(null));
console.log(Number(undefined));
```

Output:

```text
0
1
0
NaN
```

Why?

- `false` becomes `0`.
- `true` becomes `1`.
- `null` becomes `0`.
- `undefined` becomes `NaN`.

Corrected note: the raw lecture notes say `true called toNumber -> 0`, but the correct result is `1`.

### Objects to Number

When an object must become a number:

1. JavaScript calls `ToPrimitive` with a `"number"` hint.
2. It tries `valueOf()` first.
3. If needed, it tries `toString()`.
4. Then it converts the primitive result to a number.

Example:

```js
var count = {
  valueOf() {
    return 3;
  }
};

console.log(Number(count));
```

Output:

```text
3
```

Why?

`valueOf()` returns the primitive number `3`, so `Number(count)` becomes `3`.

### Arrays to Number

Original lecture examples:

```js
console.log(Number([""]));
console.log(Number(["0"]));
console.log(Number(["-0"]));
console.log(Number([null]));
console.log(Number([undefined]));
console.log(Number([1, 2, 3]));
console.log(Number([[[[]]]]));
console.log(Number({}));
console.log(Number({ valueOf() { return 3; } }));
```

Output:

```text
0
0
-0
0
0
NaN
0
NaN
3
```

Why?

- `[""]` becomes `""`, then `Number("")` is `0`.
- `["0"]` becomes `"0"`, then `0`.
- `["-0"]` becomes `"-0"`, then `-0`.
- `[null]` becomes `""`, then `0`.
- `[undefined]` becomes `""`, then `0`.
- `[1, 2, 3]` becomes `"1,2,3"`, which is not a valid number.
- `[[[[]]]]` becomes `""`, then `0`.
- `{}` becomes `"[object Object]"`, which is not a valid number.
- `{ valueOf() { return 3; } }` gives `3`.

Corrected note: the raw lecture notes wrote `valudOf`. The correct method name is `valueOf`.

### More Number Corner Cases

```js
console.log(Number(" \t\n"));
console.log(Number([]));
console.log(Number([1]));
console.log(Number([1, 2]));
console.log(Number({}));
```

Output:

```text
0
0
1
NaN
NaN
```

Why?

- A whitespace-only string becomes `0`.
- `[]` becomes `""`, then `0`.
- `[1]` becomes `"1"`, then `1`.
- `[1, 2]` becomes `"1,2"`, which is not a valid number.
- `{}` becomes `"[object Object]"`, which is not a valid number.

### Practice

Question 1:

```js
console.log(Number(""));
console.log(Number(" "));
console.log(Number("."));
```

Answer:

```text
0
0
NaN
```

Reason: Empty and whitespace-only strings become `0`. A single dot is not a valid number.

Question 2:

```js
console.log(Number(true));
console.log(Number(false));
```

Answer:

```text
1
0
```

Reason: `true` becomes `1`; `false` becomes `0`.

Question 3:

```js
console.log(Number([null]));
console.log(Number([undefined]));
console.log(Number([1, 2, 3]));
```

Answer:

```text
0
0
NaN
```

Reason: `[null]` and `[undefined]` become empty strings. `[1, 2, 3]` becomes `"1,2,3"`, which is not numeric.

Question 4:

```js
var x = {
  valueOf() {
    return "6";
  }
};

console.log(x - 2);
```

Answer:

```text
4
```

Reason: `valueOf()` returns `"6"`. The `-` operator converts `"6"` to number `6`.

## `ToBoolean`

`ToBoolean` turns a value into `true` or `false`.

It is simpler than `ToString` and `ToNumber`.

JavaScript checks if the value is in the falsy list.

### Falsy Values

These values are falsy:

- `""`
- `0`
- `-0`
- `null`
- `NaN`
- `false`
- `undefined`

Everything else is truthy.

Example:

```js
console.log(Boolean(""));
console.log(Boolean(0));
console.log(Boolean(null));
console.log(Boolean("hello"));
console.log(Boolean([]));
console.log(Boolean({}));
```

Output:

```text
false
false
false
true
true
true
```

Why?

`""`, `0`, and `null` are in the falsy list. Arrays and objects are not in the falsy list, so they are truthy.

### Object Wrappers Are Truthy

Original lecture example:

```js
console.log(Boolean(new Boolean(false)));
```

Output:

```text
true
```

Why?

`new Boolean(false)` creates an object. Objects are truthy.

This is tricky because the value inside the object is `false`, but the object itself is truthy.

### Common Mistakes

Mistake:

```js
console.log(Boolean([]));
```

Wrong answer:

```text
false
```

Correct answer:

```text
true
```

Reason: An empty array is still an object. Objects are truthy.

Mistake:

```js
console.log(Boolean("false"));
```

Wrong answer:

```text
false
```

Correct answer:

```text
true
```

Reason: `"false"` is a non-empty string. Non-empty strings are truthy.

### Practice

Question 1:

```js
console.log(Boolean(""));
console.log(Boolean("0"));
console.log(Boolean(0));
```

Answer:

```text
false
true
false
```

Reason: `""` and `0` are falsy. `"0"` is a non-empty string, so it is truthy.

Question 2:

```js
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(new Boolean(false)));
```

Answer:

```text
true
true
true
```

Reason: All three are objects. Objects are truthy.

Question 3:

```js
console.log(Boolean(NaN));
console.log(Boolean(undefined));
console.log(Boolean(null));
```

Answer:

```text
false
false
false
```

Reason: All three are in the falsy list.

## Coercion With Template Literals

Template literals convert inserted values to strings.

Original lecture example:

```js
var numStudents = 16;

console.log(`There are ${numStudents} students.`);
```

Output:

```text
There are 16 students.
```

Why?

`${numStudents}` is inside a template literal. JavaScript converts `16` to `"16"`.

Additional example:

```js
var passed = true;

console.log(`Passed: ${passed}`);
```

Output:

```text
Passed: true
```

Why?

`true` becomes `"true"` inside the template literal.

### Practice

Question 1:

```js
var score = 90;

console.log(`Score: ${score}`);
```

Answer:

```text
Score: 90
```

Reason: `score` is converted to a string inside the template literal.

Question 2:

```js
var data = null;

console.log(`Data: ${data}`);
```

Answer:

```text
Data: null
```

Reason: `null` becomes `"null"` during string conversion.

## Coercion With the `+` Operator

The `+` operator has two jobs:

- number addition
- string concatenation

If one side becomes a string, `+` joins strings.

Original lecture example:

```js
var msg1 = "There are ";
var numStudents = 16;
var msg2 = " students.";

console.log(msg1 + numStudents + msg2);
```

Output:

```text
There are 16 students.
```

Why?

First:

```js
msg1 + numStudents;
"There are " + 16;
"There are 16";
```

Then:

```js
"There are 16" + msg2;
"There are 16 students.";
```

Because one side is a string, JavaScript converts the other side to a string too.

### Order Matters

```js
console.log(1 + 2 + "3");
console.log("1" + 2 + 3);
```

Output:

```text
33
123
```

Why?

For `1 + 2 + "3"`:

```js
1 + 2 + "3";
3 + "3";
"33";
```

For `"1" + 2 + 3`:

```js
"1" + 2 + 3;
"12" + 3;
"123";
```

JavaScript works left to right.

### Original Lecture Example: Add and Kick Students

The lecture notes show this idea:

```js
var numStudents = "16";

function addNewStudent(numStudents) {
  return numStudents + 1;
}

function kickStudentOut(numStudents) {
  return numStudents - 1;
}

console.log(addNewStudent(numStudents));
console.log(kickStudentOut(numStudents));
```

Output:

```text
161
15
```

Why?

For `numStudents + 1`:

```js
"16" + 1;
"161";
```

Because one side is a string, `+` joins strings.

For `numStudents - 1`:

```js
"16" - 1;
16 - 1;
15;
```

The `-` operator only does numeric subtraction, so `"16"` becomes `16`.

Corrected note: the raw lecture notes wrote `var numStudents = 16`, but the output `161` only happens if `numStudents` is the string `"16"`. If it is the number `16`, `numStudents + 1` gives `17`.

### Practice

Question 1:

```js
console.log("5" + 1);
console.log("5" - 1);
```

Answer:

```text
51
4
```

Reason: `+` with a string concatenates. `-` converts to numbers.

Question 2:

```js
console.log(10 + 5 + "2");
console.log("10" + 5 + 2);
```

Answer:

```text
152
1052
```

Reason: JavaScript evaluates left to right. Once the result is a string, later `+` operations concatenate.

Question 3:

```js
var n = "20";

console.log(n + 1);
console.log(n - 1);
```

Answer:

```text
201
19
```

Reason: `+` concatenates with a string. `-` converts `"20"` to `20`.

## Boxing

Boxing happens when JavaScript temporarily wraps a primitive value in an object.

This lets you access properties and methods on primitives.

Original lecture idea:

```js
var name = "Ali";

console.log(name.length);
console.log(name.toUpperCase());
```

Output:

```text
3
ALI
```

Why?

`"Ali"` is a primitive string. It is not an object. But JavaScript temporarily boxes it like a String object, so `.length` and `.toUpperCase()` can work.

After the operation, the temporary object is gone.

### Boxing Does Not Mean Primitives Are Objects

```js
var name = "Ali";

console.log(typeof name);
console.log(typeof new String("Ali"));
```

Output:

```text
string
object
```

Why?

`name` is a primitive string. `new String("Ali")` creates a real object wrapper.

### `null` and `undefined` Cannot Be Boxed

```js
console.log(null.length);
```

Output:

```text
TypeError
```

Why?

JavaScript cannot box `null` or `undefined`. They have no properties.

### Practice

Question 1:

```js
var word = "hello";

console.log(word.length);
```

Answer:

```text
5
```

Reason: JavaScript temporarily boxes the string so `.length` can be read.

Question 2:

```js
console.log(typeof "hello");
console.log(typeof new String("hello"));
```

Answer:

```text
string
object
```

Reason: The first value is a primitive. The second value is an object wrapper.

Question 3:

```js
console.log(undefined.toString());
```

Answer:

```text
TypeError
```

Reason: `undefined` cannot be boxed.

## Comparison Coercion

Comparison operators like `<` and `>` compare values.

When needed, JavaScript converts values to numbers.

Example:

```js
console.log("5" < 10);
```

Output:

```text
true
```

Why?

`"5"` becomes number `5`.

```js
5 < 10;
true;
```

### Original Lecture Example: Chained Comparisons

```js
console.log(1 < 2);
console.log(2 < 3);
console.log(1 < 2 < 3);
```

Output:

```text
true
true
true
```

Why?

JavaScript does not read `1 < 2 < 3` like math class.

It reads left to right:

```js
(1 < 2) < 3;
true < 3;
1 < 3;
true;
```

`true` becomes `1`.

Another original lecture example:

```js
console.log(3 > 2 > 1);
```

Output:

```text
false
```

Why?

JavaScript reads it left to right:

```js
(3 > 2) > 1;
true > 1;
1 > 1;
false;
```

### Very Tricky Case

```js
console.log(2 < 1 < 3);
```

Output:

```text
true
```

Why?

```js
(2 < 1) < 3;
false < 3;
0 < 3;
true;
```

This looks strange because JavaScript is not doing a real chained range check.

Correct range check:

```js
console.log(2 < 1 && 1 < 3);
```

Output:

```text
false
```

Why?

Use `&&` when you want both comparisons to be true.

### Practice

Question 1:

```js
console.log(1 < 2 < 3);
```

Answer:

```text
true
```

Reason: `1 < 2` is `true`, then `true` becomes `1`, so `1 < 3` is true.

Question 2:

```js
console.log(3 > 2 > 1);
```

Answer:

```text
false
```

Reason: `3 > 2` is `true`, then `true` becomes `1`, so `1 > 1` is false.

Question 3:

```js
console.log(5 > 4 > 0);
```

Answer:

```text
true
```

Reason: `5 > 4` is `true`, then `true` becomes `1`, so `1 > 0` is true.

Question 4:

```js
console.log(5 > 4 > 1);
```

Answer:

```text
false
```

Reason: `5 > 4` is `true`, then `true` becomes `1`, so `1 > 1` is false.

## Big Corner Case Review

### Number Cases

```js
console.log(Number(""));
console.log(Number(" \t\n"));
console.log(Number(null));
console.log(Number(undefined));
console.log(Number([]));
console.log(Number([1, 2, 3]));
console.log(Number([null]));
console.log(Number([undefined]));
console.log(Number({}));
console.log(Number(true));
console.log(Number(false));
```

Output:

```text
0
0
0
NaN
0
NaN
0
0
NaN
1
0
```

Why?

- Empty strings and whitespace strings become `0`.
- `null` becomes `0`.
- `undefined` becomes `NaN`.
- `[]` becomes `""`, then `0`.
- `[1, 2, 3]` becomes `"1,2,3"`, then `NaN`.
- `[null]` and `[undefined]` become `""`, then `0`.
- `{}` becomes `"[object Object]"`, then `NaN`.
- `true` becomes `1`.
- `false` becomes `0`.

### String Cases

```js
console.log(String(-0));
console.log(String(null));
console.log(String(undefined));
console.log(String([null]));
console.log(String([undefined]));
console.log(String({}));
```

Output:

```text
0
null
undefined
(empty line)
(empty line)
[object Object]
```

Why?

- `-0` becomes `"0"`.
- `null` becomes `"null"`.
- `undefined` becomes `"undefined"`.
- `[null]` and `[undefined]` become empty strings, so `console.log` prints blank lines.
- A plain object becomes `"[object Object]"`.

### Boolean Cases

```js
console.log(Boolean(""));
console.log(Boolean("false"));
console.log(Boolean(0));
console.log(Boolean(-0));
console.log(Boolean(NaN));
console.log(Boolean([]));
console.log(Boolean({}));
console.log(Boolean(new Boolean(false)));
```

Output:

```text
false
true
false
false
false
true
true
true
```

Why?

Only the falsy list becomes `false`. Non-empty strings and all objects become `true`.

## Common Mistakes

- Thinking abstract operations are normal functions you can call.
- Forgetting that `+` can concatenate strings.
- Forgetting that `-` forces number conversion.
- Thinking `Number(true)` is `0`. It is `1`.
- Thinking `Boolean([])` is `false`. It is `true`.
- Thinking `Boolean({})` is `false`. It is `true`.
- Thinking `Boolean(new Boolean(false))` is `false`. It is `true`.
- Thinking `[null]` becomes `"null"`. It becomes `""` when converted by array `toString()`.
- Thinking `[undefined]` becomes `"undefined"`. It becomes `""` when converted by array `toString()`.
- Thinking `{}` becomes an empty string. It becomes `"[object Object]"`.
- Thinking `1 < 2 < 3` is a normal math-style range check.
- Forgetting that chained comparisons run left to right.
- Forgetting that `String(-0)` gives `"0"`.

## Final Exam Rules to Remember

- If an operator needs a number, expect `ToNumber`.
- If a value is placed inside a template literal, expect `ToString`.
- If an object must become primitive, expect `ToPrimitive`.
- For number-like object conversion, `valueOf()` is usually tried before `toString()`.
- For string-like object conversion, `toString()` is usually tried before `valueOf()`.
- `Number("")` is `0`.
- `Number(" ")` is `0`.
- `Number(".")` is `NaN`.
- `Number(null)` is `0`.
- `Number(undefined)` is `NaN`.
- `Number(true)` is `1`.
- `Number(false)` is `0`.
- `String([null])` is `""`.
- `String([undefined])` is `""`.
- `Boolean(new Boolean(false))` is `true`.
- Chained comparisons are evaluated left to right.

## Exercises Section

Use these mixed questions after reading the topic practice sections above.

### Mixed Practice Questions

Question 1:

```js
console.log([] + 1);
```

Answer:

```text
1
```

Reason: `[]` becomes `""`. Then `"" + 1` becomes `"1"`. The output looks like `1`, but it is a string.

Question 2:

```js
console.log([] - 1);
```

Answer:

```text
-1
```

Reason: `[]` becomes `""`, then `Number("")` is `0`. So `0 - 1` is `-1`.

Question 3:

```js
console.log([1, 2] + 3);
```

Answer:

```text
1,23
```

Reason: `[1, 2]` becomes `"1,2"`. Then `"1,2" + 3` becomes `"1,23"`.

Question 4:

```js
console.log([1, 2] - 3);
```

Answer:

```text
NaN
```

Reason: `[1, 2]` becomes `"1,2"`. `Number("1,2")` is `NaN`.

Question 5:

```js
var obj = {
  valueOf() {
    return 2;
  },
  toString() {
    return "5";
  }
};

console.log(obj + 1);
console.log(obj - 1);
```

Answer:

```text
3
1
```

Reason: For ordinary objects, `+` first gets a primitive. `valueOf()` returns number `2`, so `2 + 1` is `3`. The `-` operator also gets `2`, so `2 - 1` is `1`.

Question 6:

```js
var obj = {
  valueOf() {
    return {};
  },
  toString() {
    return "5";
  }
};

console.log(obj + 1);
console.log(obj - 1);
```

Answer:

```text
51
4
```

Reason: `valueOf()` returns an object, so JavaScript uses `toString()` and gets `"5"`. With `+`, `"5" + 1` is `"51"`. With `-`, `"5"` becomes number `5`, so `5 - 1` is `4`.

Question 7:

```js
console.log(Boolean(""));
console.log(Boolean(" "));
console.log(Boolean([]));
```

Answer:

```text
false
true
true
```

Reason: Empty string is falsy. A space string is non-empty, so it is truthy. An array is an object, so it is truthy.

Question 8:

```js
console.log(10 > 9 > 8);
```

Answer:

```text
false
```

Reason: `10 > 9` is `true`. Then `true > 8` becomes `1 > 8`, which is false.

Question 9:

```js
console.log(Number([[[[]]]]));
console.log(String([[[[]]]]));
```

Answer:

```text
0
(empty line)
```

Reason: The nested empty arrays eventually become an empty string. `Number("")` is `0`, and `String([[[[]]]])` is `""`.

Question 10:

```js
var box = new Boolean(false);

if (box) {
  console.log("yes");
} else {
  console.log("no");
}
```

Answer:

```text
yes
```

Reason: `box` is an object. Objects are truthy, even when they wrap `false`.
