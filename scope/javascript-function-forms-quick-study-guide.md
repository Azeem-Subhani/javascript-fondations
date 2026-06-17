# JavaScript Function Forms Quick Study Guide

This short guide covers `scope/lecture2.md`.

Read time goal: about 5 to 7 minutes.

## 1. The Main Idea

JavaScript has different ways to create functions:

- function declaration
- named function expression
- anonymous function expression
- arrow function

For output questions, always ask:

```text
Where is the function name available?
When is the function value assigned?
Is the function being called before or after assignment?
```

Note: consoles may display function objects differently. In this guide, `[Function: name]` means "a function object with this name."

## 2. Function Declaration

A **function declaration** creates a function and attaches its name to the enclosing scope.

Example:

```js
function teacher() {}

console.log(teacher);
```

Output:

```text
[Function: teacher]
```

Why?

`teacher` is created in the surrounding scope. So `console.log(teacher)` can find it.

Function declarations are available before the line where they appear:

```js
teacher();

function teacher() {
  console.log("Azeem");
}
```

Output:

```text
Azeem
```

Why?

JavaScript registers function declarations before normal code runs.

## 3. Function Expression

A **function expression** creates a function as a value.

That value is usually stored in a variable.

Original lecture example:

```js
function teacher() {}

var myTeacher = function anotherTeacher() {
    console.log(anotherTeacher);
}

console.log(teacher);
console.log(myTeacher);
console.log(anotherTeacher);
```

Output:

```text
[Function: teacher]
[Function: anotherTeacher]
ReferenceError: anotherTeacher is not defined
```

Why?

`teacher` is a function declaration, so its name is available in the outer scope.

`myTeacher` is a variable in the outer scope. It stores the function value.

`anotherTeacher` is the name of the function expression. That name is available only inside that function itself.

Key rule from the lecture:

```text
Function declarations attach their name to the enclosing scope.
Function expressions put their own name inside their own function scope.
```

So this works:

```js
var myTeacher = function anotherTeacher() {
  console.log(anotherTeacher);
};

myTeacher();
```

Output:

```text
[Function: anotherTeacher]
```

Why?

Inside the function body, `anotherTeacher` is visible.

But this does not work:

```js
console.log(anotherTeacher);
```

Output:

```text
ReferenceError: anotherTeacher is not defined
```

Why?

The outer scope does not get the name `anotherTeacher`.

## 4. Calling A Function Expression Too Early

Function expressions are assigned during runtime.

Example:

```js
myTeacher();

var myTeacher = function anotherTeacher() {
  console.log("Ali");
};
```

Output:

```text
TypeError: myTeacher is not a function
```

Why?

`var myTeacher` exists before the code runs, but its value is `undefined` until the assignment line runs.

So JavaScript tries to call `undefined` like a function.

Compare with a declaration:

```js
teacher();

function teacher() {
  console.log("Ali");
}
```

Output:

```text
Ali
```

Why?

Function declarations are registered with their function value before execution.

## 5. Named Function Expression

A **named function expression** is a function expression with its own name.

Example:

```js
var ask = function askQuestion() {
  console.log("Why?");
};

ask();
```

Output:

```text
Why?
```

Why?

`ask` is the variable used from the outside.

`askQuestion` is the function's internal name.

The lecture recommends named function expressions because they give:

- reliable self-reference
- better stack traces
- more self-documenting code

### Reliable Self-Reference

Self-reference means the function can refer to itself.

Example:

```js
var countDown = function count(n) {
  if (n === 0) {
    console.log("done");
    return;
  }

  count(n - 1);
};

countDown(2);
```

Output:

```text
done
```

Why?

Inside the function, the name `count` always points to the function itself.

This is useful for recursion.

### Better Stack Traces And Clearer Code

Compare:

```js
var ids = persons.map(function getId(person) {
  return person.id;
});
```

The name `getId` tells us what the function does.

If an error happens, a stack trace can show a useful name like `getId` instead of an anonymous function.

## 6. Anonymous Function Expression

An **anonymous function expression** has no function name.

Example:

```js
var ask = function(question) {
  console.log(question);
};

ask("Why?");
```

Output:

```text
Why?
```

Why?

The variable `ask` stores the function, so we can call it with `ask(...)`.

But the function itself has no clear internal name.

The lecture's advice:

```text
Prefer named function expressions.
```

Why?

Named function expressions are easier to debug, easier to read, and safer for self-reference.

## 7. Arrow Functions

Arrow functions are shorter function expressions.

Original lecture example:

```js
var ids = persons.map((person) => person.id);
```

Complete example:

```js
var persons = [
  { id: 1, name: "Ali" },
  { id: 2, name: "Sara" }
];

var ids = persons.map((person) => person.id);

console.log(ids);
```

Output:

```text
[1, 2]
```

Why?

`map` runs the arrow function once for each person.

For each object, `person.id` is returned.

The same code with a named function expression:

```js
var persons = [
  { id: 1, name: "Ali" },
  { id: 2, name: "Sara" }
];

var ids = persons.map(function getId(person) {
  return person.id;
});

console.log(ids);
```

Output:

```text
[1, 2]
```

Why?

`getId` receives each `person` and returns its `id`.

The named version is longer, but the name is useful for debugging and reading.

## 8. Arrow Functions In Promise Chains

Original lecture idea:

```js
getPerson().then(person => getDate(person.id)).then(renderData);
```

Named function expression version from the lecture:

```js
getPerson().then(function getDataFrom(person){
    return getData(person.id);
}).then(renderData);
```

Note: the lecture has `getDate` in one line and `getData` in the other. The main idea is the same: get the person's `id`, then pass it to another function.

Complete example:

```js
function getPerson() {
  return Promise.resolve({ id: 7 });
}

function getData(id) {
  return Promise.resolve("data for " + id);
}

function renderData(data) {
  console.log(data);
}

getPerson()
  .then(function getDataFrom(person) {
    return getData(person.id);
  })
  .then(renderData);
```

Output:

```text
data for 7
```

Why?

`getPerson()` resolves to `{ id: 7 }`.

`getDataFrom` receives that person and returns `getData(7)`.

`renderData` receives `"data for 7"` and prints it.

Arrow functions are nice for short callbacks:

```js
person => person.id
```

Named function expressions are often better when the callback has an important meaning:

```js
function getDataFrom(person) {
  return getData(person.id);
}
```

## 9. Common Mistakes

Mistake 1:

```text
Thinking the name of a function expression is available outside.
```

It is not.

```js
var fn = function innerName() {};

console.log(innerName);
```

Output:

```text
ReferenceError: innerName is not defined
```

Mistake 2:

```text
Calling a var function expression before assignment.
```

```js
fn();

var fn = function run() {};
```

Output:

```text
TypeError: fn is not a function
```

Mistake 3:

```text
Using anonymous callbacks everywhere.
```

They can work, but named callbacks are clearer and easier to debug.

## 10. Practice

### Question 1

```js
function teacher() {}

console.log(teacher);
```

Answer:

```text
[Function: teacher]
```

Reason:

Function declarations attach their name to the enclosing scope.

### Question 2

```js
var myTeacher = function anotherTeacher() {};

console.log(myTeacher);
console.log(anotherTeacher);
```

Answer:

```text
[Function: anotherTeacher]
ReferenceError: anotherTeacher is not defined
```

Reason:

`myTeacher` exists outside. `anotherTeacher` exists only inside the function body.

### Question 3

```js
teacher();

function teacher() {
  console.log("Azeem");
}
```

Answer:

```text
Azeem
```

Reason:

Function declarations are available before execution reaches their line.

### Question 4

```js
myTeacher();

var myTeacher = function anotherTeacher() {
  console.log("Ali");
};
```

Answer:

```text
TypeError: myTeacher is not a function
```

Reason:

`myTeacher` exists but is still `undefined` when it is called.

### Question 5

```js
var numbers = [1, 2, 3];

var doubled = numbers.map((num) => num * 2);

console.log(doubled);
```

Answer:

```text
[2, 4, 6]
```

Reason:

The arrow function returns each number multiplied by `2`.

### Question 6

```js
var numbers = [1, 2, 3];

var doubled = numbers.map(function double(num) {
  return num * 2;
});

console.log(doubled);
```

Answer:

```text
[2, 4, 6]
```

Reason:

`double` runs once for each array item and returns the new value.

## Final Memory Lines

```text
Function declaration: name goes in the enclosing scope.
Named function expression: outside variable gets the function; inner name stays inside the function.
Anonymous function expression: works, but has no useful internal name.
Arrow function: short function expression, best for small callbacks.
Prefer named function expressions when clarity and debugging matter.
```
