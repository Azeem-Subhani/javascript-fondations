# JavaScript Scope, Closure, And Hoisting Quick Study Guide

This short guide covers `scope/lecture_notes_new.md`.

Read time goal: about 10 to 15 minutes.

Use this guide for:

- MCQs
- output questions
- exam questions
- interview-style JavaScript scope quizzes

## 1. Fast Output Strategy

For every scope question, ask these questions:

```text
1. Where was this function written?
2. Is the name declared with var, let, const, or function?
3. Is the name inside a function scope or block scope?
4. Is the code reading a value or assigning a value?
5. Is the variable in the TDZ?
6. Is a function running later, after its outer function finished?
```

Important memory line:

```text
JavaScript scope is lexical: decided by where code is written.
```

## 2. Lexical Scope vs Dynamic Scope

**Lexical scope** means JavaScript decides scope from the place where code is written.

It is decided before runtime, during the compile/parse step.

**Dynamic scope** means scope would depend on who called the function at runtime.

JavaScript does **not** use dynamic scope for normal variables.

Original lecture example:

```js
var teacher = 'Azeem';

function ask() {
    console.log(teacher);
}

function otherClass() {
    var teacher = 'Ali';
    ask("why")
}

otherClass();
```

Output:

```text
Azeem
```

Why?

`ask` was written in the global scope.

So when `ask` needs `teacher`, it looks in its own scope first, then the global scope.

It does **not** use `otherClass` scope just because `otherClass` called it.

If JavaScript used dynamic scope, this would print `Ali`. But JavaScript uses lexical scope, so it prints `Azeem`.

Common mistake:

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

Calling location does not change lexical scope.

### Practice

Question:

```js
var topic = "global";

function printTopic() {
  console.log(topic);
}

function run() {
  var topic = "local";
  printTopic();
}

run();
```

Answer:

```text
global
```

Reason:

`printTopic` was written in global scope, so it uses global `topic`.

## 3. Function Scope

`var` is function-scoped.

That means a `var` variable belongs to the nearest function, not to an `if`, `for`, or plain `{}` block.

Original lecture example:

```js
var teacher = 'Azeem';

function anotherTeacher() {
    var teacher = "Ali";

    console.log(teacher) // Ali
}

(anotherTeacher)();

console.log(teacher); // Azeem
```

Output:

```text
Ali
Azeem
```

Why?

Inside `anotherTeacher`, `var teacher = "Ali"` creates a local function-scoped variable.

That local `teacher` shadows the global `teacher`.

Outside the function, JavaScript uses the global `teacher`, so it prints `Azeem`.

Common mistake:

```js
var teacher = "Azeem";

function anotherTeacher() {
  var teacher = "Ali";
}

anotherTeacher();
console.log(teacher);
```

Output:

```text
Azeem
```

Why?

The local `teacher` inside the function does not change the global `teacher`.

### Practice

Question:

```js
var x = 1;

function test() {
  var x = 2;
  console.log(x);
}

test();
console.log(x);
```

Answer:

```text
2
1
```

Reason:

The function has its own `x`. The global `x` is still `1`.

## 4. IIFE

IIFE means **Immediately Invoked Function Expression**.

It is a function expression that runs immediately.

Original named IIFE:

```js
var teacher = 'Azeem';

(function anotherTeacher() {
    var teacher = "Ali";

    console.log(teacher) // Ali
})();

console.log(teacher); // Azeem
```

Output:

```text
Ali
Azeem
```

Why?

The function runs immediately.

Its local `teacher` exists only inside that function.

Original anonymous IIFE:

```js
var teacher = 'Azeem';

(function() {
    var teacher = "Ali";

    console.log(teacher) // Ali
})();

console.log(teacher); // Azeem
```

Output:

```text
Ali
Azeem
```

Why?

The anonymous function creates a new function scope and runs one time.

The parentheses turn the function into an expression:

```js
(function() {
  console.log("runs now");
})();
```

Output:

```text
runs now
```

Tricky case:

```js
(function anotherTeacher() {
  console.log("inside");
})();

console.log(anotherTeacher);
```

Output:

```text
inside
ReferenceError: anotherTeacher is not defined
```

Why?

`anotherTeacher` is the internal name of the function expression.

It is not available in the outer scope.

### Practice

Question:

```js
var name = "outer";

(function showName() {
  var name = "inner";
  console.log(name);
})();

console.log(name);
```

Answer:

```text
inner
outer
```

Reason:

The IIFE has its own function scope.

## 5. Block Scope

A block is code inside `{}`.

`let` and `const` are block-scoped.

`var` is not block-scoped.

Original lecture example:

```js
var teacher = 'Azeem';

{
    let teacher = "Ali";
    console.log(teacher) // Ali
}

console.log(teacher); // Azeem
```

Output:

```text
Ali
Azeem
```

Why?

`let teacher = "Ali"` belongs only to the block.

After the block ends, JavaScript uses the global `teacher`.

Compare with `var`:

```js
var teacher = "Azeem";

{
  var teacher = "Ali";
  console.log(teacher);
}

console.log(teacher);
```

Output:

```text
Ali
Ali
```

Why?

`var` does not stay inside the block.

At global level, `var teacher` is the same global `teacher`.

Common mistake:

```js
if (true) {
  var a = 1;
  let b = 2;
}

console.log(a);
console.log(b);
```

Output:

```text
1
ReferenceError: b is not defined
```

Why?

`a` escapes the block because it uses `var`.

`b` stays inside the block because it uses `let`.

### Practice

Question:

```js
var score = 10;

{
  let score = 20;
  console.log(score);
}

console.log(score);
```

Answer:

```text
20
10
```

Reason:

The block `score` shadows the outer `score` only inside the block.

## 6. `var` vs `let` vs `const`

Quick rules:

```text
var   = function-scoped, can be redeclared, initialized as undefined
let   = block-scoped, can be reassigned, cannot be redeclared in same scope
const = block-scoped, cannot be reassigned, must be initialized
```

### `var` In `try/catch`

Original lecture example:

```js
function lookupRecord(searchStr) {
    try {
        var id = getRecord ( searchStr );
    }
    catch (err) {
        var id = -1;
    }

    return id;
}
```

Example output:

```js
function getRecord(searchStr) {
  if (searchStr === "bad") {
    throw new Error("not found");
  }

  return 42;
}

console.log(lookupRecord("ok"));
console.log(lookupRecord("bad"));
```

Output:

```text
42
-1
```

Why?

Both `var id` declarations belong to the same function scope.

So `return id` can read `id`.

This version with `let` is wrong:

```js
function lookupRecord(searchStr) {
  try {
    let id = getRecord(searchStr);
  }
  catch (err) {
    let id = -1;
  }

  return id;
}
```

Output:

```text
ReferenceError: id is not defined
```

Why?

Each `let id` exists only inside its own block.

The `return id` line is outside those blocks.

Correct `let` version:

```js
function lookupRecord(searchStr) {
  let id;

  try {
    id = getRecord(searchStr);
  }
  catch (err) {
    id = -1;
  }

  return id;
}
```

Why?

`id` is declared in the function body, so `try`, `catch`, and `return` can all use it.

### Primitives

```js
let score = 1;
score = 2;

console.log(score);
```

Output:

```text
2
```

Why?

`let` allows reassignment.

```js
const score = 1;
score = 2;
```

Output:

```text
TypeError: Assignment to constant variable.
```

Why?

`const` does not allow reassignment.

### Objects

```js
const user = {
  name: "Azeem"
};

user.name = "Ali";

console.log(user.name);
```

Output:

```text
Ali
```

Why?

`const` protects the variable binding, not the inside of the object.

This fails:

```js
const user = {
  name: "Azeem"
};

user = {
  name: "Ali"
};
```

Output:

```text
TypeError: Assignment to constant variable.
```

Why?

This tries to assign a new object to `user`.

### Practice

Question:

```js
const person = { name: "Azeem" };
person.name = "Ali";
console.log(person.name);
```

Answer:

```text
Ali
```

Reason:

The object property can change. The `person` variable cannot be reassigned.

Question:

```js
function test() {
  if (true) {
    var id = 1;
    let code = 2;
  }

  console.log(id);
  console.log(code);
}

test();
```

Answer:

```text
1
ReferenceError: code is not defined
```

Reason:

`var id` belongs to the function. `let code` belongs to the `if` block.

## 7. Hoisting

Hoisting is a teaching word.

JavaScript does not really move your code.

Better idea:

```text
JavaScript registers declarations before normal execution.
```

Original lecture idea:

```js
student;
teacher;

var student = "You";
var teacher = "Azeem";
```

There is no visible output because nothing is printed.

But `student` and `teacher` are not `ReferenceError`.

They exist with value `undefined` before the assignment lines run.

Clear output version:

```js
console.log(student);
console.log(teacher);

var student = "You";
var teacher = "Azeem";
```

Output:

```text
undefined
undefined
```

Why?

The declarations are known early:

```js
var student;
var teacher;
```

But the values `"You"` and `"Azeem"` are assigned later.

### `var` Hoisting

Original lecture example:

```js
teacher = "Azeem"
console.log(teacher);
var teacher;
```

Output:

```text
Azeem
```

Why?

`var teacher` is registered before execution.

So `teacher = "Azeem"` assigns to that variable.

### Where Does Deep `var` Hoist?

`var` hoists to the nearest function scope.

If there is no function, it belongs to global scope.

Example:

```js
var teacher = "global";

function outer() {
  if (true) {
    var teacher = "outer";
  }

  console.log(teacher);
}

outer();
console.log(teacher);
```

Output:

```text
outer
global
```

Why?

The `var teacher` inside `outer` belongs to `outer`, not to the `if` block and not to global scope.

### Function Declaration Hoisting

Original lecture example:

```js
teacher = "Azeem"
console.log(teacher);
var teacher;

console.log(getTeacher())

function getTeacher() {
    return teacher;
}
```

Output:

```text
Azeem
Azeem
```

Why?

Function declarations are available before their line runs.

`teacher` already has the value `"Azeem"` before `getTeacher()` is called.

### Function Expression Is Different

Original lecture idea:

```js
console.log(getTeacher())

var myTeacher = function getTeacher() {
    return teacher;
}
```

Output:

```text
ReferenceError: getTeacher is not defined
```

Why?

`getTeacher` is the inner name of the function expression.

It is not available in the outer scope.

Also, `myTeacher` only gets the function value when the assignment line runs.

Another tricky version:

```js
console.log(myTeacher());

var myTeacher = function getTeacher() {
  return "Azeem";
};
```

Output:

```text
TypeError: myTeacher is not a function
```

Why?

`var myTeacher` is hoisted with value `undefined`.

Before the assignment line, JavaScript tries to call `undefined` like a function.

### Practice

Question:

```js
console.log(name);
var name = "Azeem";
```

Answer:

```text
undefined
```

Reason:

`var name` exists before assignment, but its value is not set yet.

Question:

```js
sayHi();

function sayHi() {
  console.log("Hi");
}
```

Answer:

```text
Hi
```

Reason:

Function declarations are available before normal execution.

Question:

```js
sayHi();

var sayHi = function() {
  console.log("Hi");
};
```

Answer:

```text
TypeError: sayHi is not a function
```

Reason:

`var sayHi` is `undefined` before the assignment.

## 8. TDZ With `let` And `const`

TDZ means **Temporal Dead Zone**.

`let` and `const` are hoisted to their block, but they are not initialized immediately.

You cannot use them before the declaration line.

Original lecture example:

```js
{
    teacher = "Azeem"; // TDZ Error
    let teacher;
}
```

Output:

```text
ReferenceError: Cannot access 'teacher' before initialization
```

Why?

The block has its own `teacher`.

But that `teacher` is in the TDZ until `let teacher` runs.

Another original lecture example:

```js
var teacher = "Azeem";

{
    console.log(teacher); // TDZ error
    let teacher = "Ali"
}
```

Output:

```text
ReferenceError: Cannot access 'teacher' before initialization
```

Why?

The `let teacher` inside the block shadows the global `teacher`.

JavaScript does not use the global `teacher` inside that block.

But the block `teacher` is still in the TDZ at `console.log(teacher)`.

Compare with `var`:

```js
{
  console.log(teacher);
  var teacher = "Ali";
}
```

Output:

```text
undefined
```

Why?

`var teacher` is initialized to `undefined` when the scope starts.

Common mistake:

```js
console.log(typeof teacher);

let teacher = "Azeem";
```

Output:

```text
ReferenceError: Cannot access 'teacher' before initialization
```

Why?

`typeof` is safe for undeclared variables, but it is not safe for TDZ variables.

### Practice

Question:

```js
let x = 1;

{
  console.log(x);
  let x = 2;
}
```

Answer:

```text
ReferenceError: Cannot access 'x' before initialization
```

Reason:

The block has its own `x`, and that block `x` is in the TDZ before `let x = 2`.

Question:

```js
{
  let x;
  console.log(x);
}
```

Answer:

```text
undefined
```

Reason:

After `let x` runs, the variable exists and has value `undefined`.

## 9. Closure

A **closure** happens when a function remembers its lexical scope, even when the function runs outside that scope.

Short memory line:

```text
Closure preserves access to variables.
```

Original lecture example:

```js
function ask(question) {
    setTimeout(function waitASec() {
        console.log(question)
    }, 100);
}

ask("What is closure?");
```

Output after about 100 milliseconds:

```text
What is closure?
```

Why?

`ask` finishes before `waitASec` runs.

But `waitASec` still remembers the `question` variable from `ask`.

That is closure.

Original lecture example:

```js
function ask(question) {
    return function holdYourQuestion() {
        console.log(question);
    }
}

var myQuestion = ask("What is closure");

myQuestion();
```

Output:

```text
What is closure
```

Why?

`ask` returns the inner function.

Later, `myQuestion()` runs outside `ask`, but it can still access `question`.

Important tricky idea:

```text
Closure remembers the variable, not just a frozen copy of the value.
```

Example:

```js
function outer() {
  let message = "first";

  function inner() {
    console.log(message);
  }

  message = "second";
  return inner;
}

var fn = outer();
fn();
```

Output:

```text
second
```

Why?

`inner` closes over the `message` variable.

Before `outer` returns, `message` changes to `"second"`.

So `inner` prints the current value of that variable.

Useful closure example:

```js
function makeCounter() {
  let count = 0;

  return function countUp() {
    count++;
    console.log(count);
  };
}

var counter = makeCounter();

counter();
counter();
counter();
```

Output:

```text
1
2
3
```

Why?

All three calls use the same closed-over `count` variable.

### Practice

Question:

```js
function makePrinter(text) {
  return function print() {
    console.log(text);
  };
}

var printHello = makePrinter("hello");
printHello();
```

Answer:

```text
hello
```

Reason:

`print` remembers `text` from `makePrinter`.

Question:

```js
function makeChanger() {
  let value = 1;

  function change() {
    value = 2;
  }

  function print() {
    console.log(value);
  }

  return { change, print };
}

var tools = makeChanger();
tools.change();
tools.print();
```

Answer:

```text
2
```

Reason:

Both returned functions close over the same `value` variable.

## 10. Closure In Loops: `var` vs `let`

Classic lecture example with `var`:

```js
for (var i = 1; i <= 3; i++) {
    setTimeout(function() {
        console.log(`i: ${i}`);
    }, i * 1000)
}
```

Output:

```text
i: 4
i: 4
i: 4
```

Why?

`var` creates one shared `i` for the whole loop.

By the time the callbacks run, the loop is finished and `i` is `4`.

Classic lecture example with `let`:

```js
for (let i = 1; i <= 3; i++) {
    setTimeout(function() {
        console.log(`i: ${i}`);
    }, i * 1000)
}
```

Output:

```text
i: 1
i: 2
i: 3
```

Why?

`let` creates a new `i` for each loop iteration.

Each callback closes over a different `i`.

Common mistake:

```js
for (var i = 1; i <= 3; i++) {
  console.log(i);
}

console.log("after", i);
```

Output:

```text
1
2
3
after 4
```

Why?

After the loop condition fails, `i` is `4`.

### Practice

Question:

```js
for (var i = 0; i < 2; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
```

Answer:

```text
2
2
```

Reason:

Both callbacks share the same `var i`.

When callbacks run, the loop is done and `i` is `2`.

Question:

```js
for (let i = 0; i < 2; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
```

Answer:

```text
0
1
```

Reason:

Each callback gets its own `let i`.

## 11. Module Patterns

A module groups data and behavior together.

But a real module also has **encapsulation**.

Encapsulation means hiding data so outside code cannot directly change it.

Original lecture object example:

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        console.log(this.teacher, question);
    }
}

workshop.ask("is this a module?");
```

Output:

```text
Azeem is this a module?
```

Why?

`this.teacher` reads the public `teacher` property from `workshop`.

But this is not a full module pattern because `teacher` is not hidden.

Outside code can do this:

```js
workshop.teacher = "Ali";
workshop.ask("changed?");
```

Output:

```text
Ali changed?
```

Why?

`teacher` is public data.

Closure module pattern:

```js
var workshop = (function WorkshopModule() {
  var teacher = "Azeem";

  function ask(question) {
    console.log(teacher, question);
  }

  return {
    ask: ask
  };
})();

workshop.ask("is this a module?");
console.log(workshop.teacher);
```

Output:

```text
Azeem is this a module?
undefined
```

Why?

`teacher` is inside the IIFE.

Only `ask` has closure access to it.

The outside object exposes `ask`, not `teacher`.

### ES Module Pattern

Original lecture idea:

```js
// workshop.mjs
var teacher = "Azeem"

export default function ask(question){
    console.log(teacher, question);
}
```

Example use:

```js
// main.mjs
import ask from "./workshop.mjs";

ask("is this an ES module?");
```

Output:

```text
Azeem is this an ES module?
```

Why?

ES modules have file scope.

`teacher` is not exported, so outside files cannot directly access it.

`ask` is exported, so outside files can call it.

Note:

```text
In Node.js, .mjs files are treated as ES modules.
```

### Practice

Question:

```js
var moduleLike = {
  secret: "open",
  show() {
    console.log(this.secret);
  }
};

moduleLike.secret = "changed";
moduleLike.show();
```

Answer:

```text
changed
```

Reason:

`secret` is public, so outside code can change it.

Question:

```js
var realModule = (function() {
  var secret = "hidden";

  return {
    show() {
      console.log(secret);
    }
  };
})();

console.log(realModule.secret);
realModule.show();
```

Answer:

```text
undefined
hidden
```

Reason:

`secret` is private inside the IIFE. The `show` method can access it by closure.

## 12. Common Tricky Cases

### Same Name, Inner Scope Wins

```js
var teacher = "Azeem";

function ask() {
  var teacher = "Ali";
  console.log(teacher);
}

ask();
```

Output:

```text
Ali
```

Why?

JavaScript looks in the current scope first.

### `let` Shadowing Can Still Throw TDZ

```js
var teacher = "Azeem";

{
  console.log(teacher);
  let teacher = "Ali";
}
```

Output:

```text
ReferenceError: Cannot access 'teacher' before initialization
```

Why?

The block `teacher` shadows the global `teacher` for the whole block.

### `var` In A Block Leaks Out

```js
if (true) {
  var topic = "scope";
}

console.log(topic);
```

Output:

```text
scope
```

Why?

`var` is not block-scoped.

### Function Declaration vs Function Expression

```js
console.log(one());

function one() {
  return 1;
}

console.log(two());

var two = function() {
  return 2;
};
```

Output:

```text
1
TypeError: two is not a function
```

Why?

`one` is a function declaration.

`two` is a `var` variable with value `undefined` until the assignment line.

## Final Practice

### Question 1

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

Answer:

```text
Azeem
```

Reason:

`ask` uses lexical scope, not the caller's scope.

### Question 2

```js
var x = "global";

(function() {
  var x = "iife";
  console.log(x);
})();

console.log(x);
```

Answer:

```text
iife
global
```

Reason:

The IIFE has its own function scope.

### Question 3

```js
console.log(a);
var a = 10;
```

Answer:

```text
undefined
```

Reason:

`var a` is initialized as `undefined` before execution.

### Question 4

```js
console.log(a);
let a = 10;
```

Answer:

```text
ReferenceError: Cannot access 'a' before initialization
```

Reason:

`let a` is in the TDZ before its declaration line.

### Question 5

```js
function outer() {
  var x = 1;

  return function inner() {
    console.log(x);
  };
}

var fn = outer();
fn();
```

Answer:

```text
1
```

Reason:

`inner` remembers `x` through closure.

### Question 6

```js
for (var i = 1; i <= 2; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
```

Answer:

```text
3
3
```

Reason:

Both callbacks share one `var i`. After the loop, `i` is `3`.

### Question 7

```js
for (let i = 1; i <= 2; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
```

Answer:

```text
1
2
```

Reason:

Each loop iteration gets a new `let i`.

### Question 8

```js
var workshop = (function() {
  var teacher = "Azeem";

  return {
    ask(question) {
      console.log(teacher, question);
    }
  };
})();

workshop.teacher = "Ali";
workshop.ask("why?");
```

Answer:

```text
Azeem why?
```

Reason:

The real `teacher` variable is private inside the IIFE.

Adding `workshop.teacher = "Ali"` creates a public property, but `ask` does not use it.

## Final Memory Lines

```text
Lexical scope = scope based on where code is written.
Dynamic scope = scope based on who calls the function. JavaScript does not use this for normal variables.
var = function-scoped and initialized as undefined.
let/const = block-scoped and have TDZ.
IIFE = function expression that runs immediately.
Hoisting = declarations are registered before execution.
Function declarations are callable early.
Function expressions are not callable before assignment.
Closure = a function keeps access to its lexical variables.
var loop = one shared variable.
let loop = new variable per iteration.
Module pattern = encapsulation plus closure.
ES module = file scope plus explicit exports.
```
