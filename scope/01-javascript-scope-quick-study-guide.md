# JavaScript Scope Quick Study Guide

This short guide covers the main ideas from `leacture4.md`.

Read time goal: about 6 to 7 minutes.

## 1. What Is Scope?

**Scope** means: where JavaScript looks for a name.

A name like `teacher`, `ask`, `topic`, or `question` is called an **identifier**.

Think of identifiers like marbles and scopes like buckets:

- each variable/function name is a marble
- each scope is a bucket
- functions and blocks can create buckets
- inner buckets can look outside, but outer buckets cannot look inside

Example:

```js
X = 42;
console.log(y);
```

Output in non-strict mode:

```text
ReferenceError: y is not defined
```

Why?

`X = 42` is an assignment. In non-strict mode, JavaScript may create a global variable for it.

`console.log(y)` is a read. JavaScript tries to find `y`, but no scope has it, so it throws `ReferenceError`.

Important rule:

```text
Reading an undeclared variable gives ReferenceError.
Assigning to an undeclared variable in non-strict mode can create an auto-global.
```

## 2. Assignment vs Retrieval

Variables usually do two things:

```text
1. Receive a value.
2. Give back a value.
```

Example:

```js
var teacher = "Azeem";

teacher = "Ali";

console.log(teacher);
```

Output:

```text
Ali
```

Why?

`var teacher = "Azeem"` declares the variable and gives it a value.

`teacher = "Ali"` changes the value.

`console.log(teacher)` reads the current value.

## 3. JavaScript Prepares Scope Before Running Code

JavaScript is parsed/compiled before normal runtime execution.

The lecture mentions these stages:

```text
lexing/tokenization -> parsing into AST -> code generation/preparation
```

For scope questions, remember this:

```text
Declarations are registered before the code runs.
```

Example:

```js
console.log(teacher);

var teacher = "Azeem";
```

Output:

```text
undefined
```

Why?

JavaScript already knows there is a variable called `teacher`.

But the value `"Azeem"` is assigned later, so before that assignment the value is `undefined`.

Compare:

```js
console.log(teacher);
```

Output:

```text
ReferenceError: teacher is not defined
```

Why?

There is no declaration for `teacher`.

## 4. Function Scope And Shadowing

Original lecture example:

```js
var teacher = "Azeem";

function otherClass() {
    var teacher = "Ali";
    console.log("Welcome!");
}

function ask() {
    var question = 'Why?';
    console.log(question);
}

otherClass();
ask();
```

Output:

```text
Welcome!
Why?
```

Compile-time idea:

```text
Global scope:
- teacher
- otherClass
- ask

otherClass scope:
- teacher

ask scope:
- question
```

Runtime idea:

`teacher = "Azeem"` goes into the global `teacher`.

Inside `otherClass`, `var teacher = "Ali"` creates a different local `teacher`.

Inside `ask`, `question` is local to `ask`.

### Shadowing

When an inner scope has the same name as an outer scope, the inner name **shadows** the outer name.

Example:

```js
var teacher = "Azeem";

function otherClass() {
  var teacher = "Ali";
  console.log(teacher);
}

otherClass();
console.log(teacher);
```

Output:

```text
Ali
Azeem
```

Why?

Inside `otherClass`, JavaScript finds the local `teacher` first.

Outside the function, JavaScript uses the global `teacher`.

Common mistake:

```js
var teacher = "Azeem";

function otherClass() {
  var teacher = "Ali";
}

otherClass();
console.log(teacher);
```

Output:

```text
Azeem
```

Why?

The local `teacher` does not change the global `teacher`.

## 5. Auto-Globals

Original lecture example:

```js
var teacher = "Azeem";

function otherClass() {
    teacher = "Ali";
    topic = "React";
    console.log("Welcome!");
}

otherClass();

teaher;
topic;
```

Output:

```text
Welcome!
ReferenceError: teaher is not defined
```

Important: `teaher` is a typo. It is not the same as `teacher`.

If we print the correct names:

```js
var teacher = "Azeem";

function otherClass() {
  teacher = "Ali";
  topic = "React";
  console.log("Welcome!");
}

otherClass();
console.log(teacher);
console.log(topic);
```

Output in non-strict mode:

```text
Welcome!
Ali
React
```

Why?

`teacher = "Ali"` does not create a local variable. It finds and updates global `teacher`.

`topic = "React"` has no declaration anywhere. In non-strict mode, JavaScript creates a global `topic`. This is called an **auto-global**.

Auto-global happens only when the assignment runs:

```js
function setTopic() {
  topic = "React";
}

console.log(topic);
setTopic();
```

Output:

```text
ReferenceError: topic is not defined
```

Why?

`setTopic()` has not run yet, so `topic` has not been created.

## 6. Strict Mode

Strict mode stops auto-globals.

Example:

```js
"use strict";

function otherClass() {
  topic = "React";
  console.log("Welcome!");
}

otherClass();
```

Output:

```text
ReferenceError: topic is not defined
```

Why?

`topic` was not declared. Strict mode does not create it automatically.

`"Welcome!"` is not printed because the error happens first.

Strict mode still allows assigning to an existing variable:

```js
"use strict";

var teacher = "Azeem";

function otherClass() {
  teacher = "Ali";
}

otherClass();
console.log(teacher);
```

Output:

```text
Ali
```

Why?

`teacher` already exists in global scope.

## 7. Nested Scope And Lexical Scope

Lexical scope means:

```text
Scope is decided by where the function is written.
```

Original lecture example:

```js
var teacher = "Azeem";

function otherClass() {
    var teacher = "Ali";

    function ask(question) {
        console.log(teacher, question);
    }

    ask("why?");
}

otherClass();
ask("???????");
```

Output:

```text
Ali why?
ReferenceError: ask is not defined
```

Why?

`ask` is written inside `otherClass`, so it can access `otherClass` variables.

Inside `ask`, JavaScript finds `teacher = "Ali"` from the parent scope.

But global scope cannot access `ask`, because `ask` exists only inside `otherClass`.

Tricky lexical example:

```js
var teacher = "Azeem";

function ask() {
  console.log(teacher);
}

function otherClass() {
  var teacher = "Ali";
  ask();
}

otherClass();
```

Output:

```text
Azeem
```

Why?

`ask` was written in global scope, so it looks for `teacher` in global scope.

Calling `ask()` inside `otherClass` does not change its scope.

## 8. Block Scope

JavaScript has function scope and block scope.

A block is code inside `{ }`.

`var` is function-scoped, not block-scoped.

`let` and `const` are block-scoped.

Example with `var`:

```js
if (true) {
  var topic = "React";
}

console.log(topic);
```

Output:

```text
React
```

Why?

`var` does not stay inside the `if` block.

Example with `let`:

```js
if (true) {
  let topic = "React";
}

console.log(topic);
```

Output:

```text
ReferenceError: topic is not defined
```

Why?

`let topic` exists only inside the block.

## 9. `undefined` vs `undeclared`

`undefined` means the variable exists, but it has no value yet.

```js
var teacher;
console.log(teacher);
```

Output:

```text
undefined
```

Why?

`teacher` exists, but no value was assigned.

`undeclared` means no accessible scope has that variable.

```js
console.log(teacher);
```

Output:

```text
ReferenceError: teacher is not defined
```

Why?

No scope has a declaration for `teacher`.

Tricky case:

```js
var a;

console.log(typeof a);
console.log(typeof b);
```

Output:

```text
undefined
undefined
```

Why?

`a` exists and has value `undefined`.

`b` is undeclared, but `typeof` is special. It does not throw an error for undeclared variables.

## 10. ReferenceError vs TypeError

`ReferenceError` means JavaScript cannot find the identifier.

```js
console.log(user);
```

Output:

```text
ReferenceError: user is not defined
```

`TypeError` means JavaScript found a value, but you used it in a wrong way.

```js
var user = null;
console.log(user.name);
```

Output:

```text
TypeError
```

Why?

`user` exists, but `null` cannot be used like an object.

## 11. Fast Output Strategy

Use this checklist:

1. Find declarations.
2. Put each name in the correct scope.
3. Remember: `var` is function-scoped.
4. Remember: `let` and `const` are block-scoped.
5. For each line, ask: read or assignment?
6. Look in the current scope first.
7. If not found, go outward one scope at a time.
8. If a read is not found, `ReferenceError`.
9. If an assignment is not found:
   - non-strict mode: auto-global
   - strict mode: `ReferenceError`

## 12. Short Practice

### Question 1

```js
var x = "global";

function test() {
  var x = "local";
  console.log(x);
}

test();
console.log(x);
```

Answer:

```text
local
global
```

Reason:

Local `x` shadows global `x` inside `test`.

### Question 2

```js
var x = 1;

function test() {
  x = 2;
}

test();
console.log(x);
```

Answer:

```text
2
```

Reason:

There is no local `x`, so assignment updates global `x`.

### Question 3

```js
function test() {
  topic = "Scope";
}

test();
console.log(topic);
```

Answer in non-strict mode:

```text
Scope
```

Reason:

Assignment to undeclared `topic` creates an auto-global.

### Question 4

```js
"use strict";

function test() {
  topic = "Scope";
}

test();
```

Answer:

```text
ReferenceError: topic is not defined
```

Reason:

Strict mode does not allow auto-globals.

### Question 5

```js
var topic = "global";

function outer() {
  var topic = "outer";

  function inner() {
    console.log(topic);
  }

  inner();
}

outer();
```

Answer:

```text
outer
```

Reason:

`inner` looks outward and finds `topic` in `outer`.

### Question 6

```js
if (true) {
  var a = 1;
  let b = 2;
}

console.log(a);
console.log(b);
```

Answer:

```text
1
ReferenceError: b is not defined
```

Reason:

`var a` escapes the block. `let b` stays inside the block.

### Question 7

```js
var a;

console.log(a);
console.log(c);
```

Answer:

```text
undefined
ReferenceError: c is not defined
```

Reason:

`a` is declared but empty. `c` is undeclared.

## Final Memory Lines

```text
Scope = where to look.
Shadowing = inner name hides outer name.
Lexical scope = based on where code is written.
undefined = declared but no value.
undeclared = no variable found.
ReferenceError = name not found.
TypeError = value found, but used wrongly.
```

