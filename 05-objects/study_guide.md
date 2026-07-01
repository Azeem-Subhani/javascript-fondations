# Objects Study Guide


It focuses on:

- objects
- `this`
- implicit binding
- explicit binding: `call`, `apply`, `bind`
- hard binding
- `new`
- default binding
- binding precedence
- arrow functions and lexical `this`
- classes
- prototypes
- inheritance vs behavior delegation

Most outputs below assume normal browser script behavior. Node.js can be different for top-level `var` and timer callbacks. Exam questions often use browser-like examples unless they say otherwise.

## 1. Objects

An object is a collection of properties.

A property can store:

- a value, like a string or number
- a function, usually called a method

```js
var workshop = {
    teacher: "Azeem",
    topic: "Objects",
    ask(question) {
        console.log(question);
    }
};

console.log(workshop.teacher);
workshop.ask("What is an object?");
```

Expected output:

```txt
Azeem
What is an object?
```

Why:

- `workshop.teacher` reads the `teacher` property.
- `workshop.ask(...)` calls the function stored in the `ask` property.

Common mistake:

```js
console.log(workshop["teacher"]);
```

This is also valid.

Expected output:

```txt
Azeem
```

Why:

- Dot notation and bracket notation can both read object properties.
- Bracket notation is useful when the property name is dynamic.

## 2. The `this` Keyword

`this` is one of the most confusing parts of JavaScript.

Important rule:

> A function's `this` is decided by how the function is called.

It is not decided only by where the function is written.

The place where a function is called is called the call site.

Original lecture example:

```js
function ask(question) {
    console.log(this.teacher, question);
}

function otherClass() {
    var myContext = {
        teacher: "Azeem"
    };

    ask.call(myContext, "Why?");
}

otherClass();
```

Expected output:

```txt
Azeem Why?
```

Why:

- `ask.call(myContext, "Why?")` calls `ask`.
- `.call(...)` sets `this` inside `ask` to `myContext`.
- So `this.teacher` means `myContext.teacher`.
- `myContext.teacher` is `"Azeem"`.

A function that uses `this` can be reused with many different objects.

```js
function ask(question) {
    console.log(this.teacher, question);
}

var class1 = { teacher: "Azeem" };
var class2 = { teacher: "Ali" };

ask.call(class1, "Ready?");
ask.call(class2, "Ready?");
```

Expected output:

```txt
Azeem Ready?
Ali Ready?
```

Why:

- Same function.
- Different `this` value each time.

## 3. Four Main Ways To Call A Function

The lecture notes describe four main rules:

1. Implicit binding
2. Explicit binding
3. `new` binding
4. Default binding

For normal functions, use this checklist:

1. Is the function called with `new`?
2. Is the function called with `call`, `apply`, or `bind`?
3. Is the function called as `object.method()`?
4. If none match, use default binding.

Arrow functions are different. They are covered later.

## 4. Implicit Binding

Implicit binding happens when a function is called as a method of an object.

Pattern:

```js
objectName.functionName();
```

In this case, `this` usually points to the object before the dot.

Original lecture example:

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        console.log(this.teacher, question);
    }
};

workshop.ask("What is implicit binding?");
```

Expected output:

```txt
Azeem What is implicit binding?
```

Why:

- The call site is `workshop.ask(...)`.
- The object before the dot is `workshop`.
- So `this` inside `ask` is `workshop`.
- `this.teacher` is `"Azeem"`.

Original lecture example with one function and two objects:

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop1 = {
    teacher: "Azeem",
    ask: ask
};

var workshop2 = {
    teacher: "Ali",
    ask: ask
};

workshop1.ask("What is implicit binding?");
workshop2.ask("What is implicit binding?");
```

Expected output:

```txt
Azeem What is implicit binding?
Ali What is implicit binding?
```

Why:

- There is only one `ask` function.
- `workshop1.ask(...)` sets `this` to `workshop1`.
- `workshop2.ask(...)` sets `this` to `workshop2`.
- The same function runs with different context objects.

### Tricky Case: Losing Implicit Binding

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        console.log(this.teacher, question);
    }
};

var question = workshop.ask;

question("Lost this?");
```

Expected output in non-strict browser script:

```txt
undefined Lost this?
```

Why:

- `question` now holds the function.
- The call site is `question(...)`, not `workshop.ask(...)`.
- There is no object before the dot.
- So implicit binding is lost.
- Default binding is used.

### Tricky Case: Only The Object Before The Last Dot Matters

```js
var course = {
    teacher: "Outer",
    workshop: {
        teacher: "Inner",
        ask(question) {
            console.log(this.teacher, question);
        }
    }
};

course.workshop.ask("Which teacher?");
```

Expected output:

```txt
Inner Which teacher?
```

Why:

- The function call is `course.workshop.ask(...)`.
- The object directly before `.ask` is `course.workshop`.
- So `this` is `course.workshop`.

### Practice: Implicit Binding

Question 1:

```js
var user = {
    name: "Sara",
    print() {
        console.log(this.name);
    }
};

user.print();
```

Answer:

```txt
Sara
```

Reason:

- `user.print()` sets `this` to `user`.

Question 2:

```js
var user = {
    name: "Sara",
    print() {
        console.log(this.name);
    }
};

var fn = user.print;
fn();
```

Answer in non-strict browser script:

```txt
undefined
```

Reason:

- The call is `fn()`.
- There is no object before the dot.
- Implicit binding is lost.

## 5. Explicit Binding: `call`, `apply`, And `bind`

Explicit binding means you directly tell JavaScript what `this` should be.

### `call`

`call` calls the function immediately.

Syntax:

```js
functionName.call(thisValue, arg1, arg2);
```

Original lecture example:

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop1 = {
    teacher: "Azeem"
};

var workshop2 = {
    teacher: "Ali"
};

ask.call(workshop1, "What is implicit binding?");
ask.call(workshop2, "What is implicit binding?");
```

Expected output:

```txt
Azeem What is implicit binding?
Ali What is implicit binding?
```

Why:

- `ask.call(workshop1, ...)` sets `this` to `workshop1`.
- `ask.call(workshop2, ...)` sets `this` to `workshop2`.

### `apply`

`apply` also calls the function immediately.

The difference:

- `call` passes arguments one by one.
- `apply` passes arguments as an array.

```js
function introduce(greeting, punctuation) {
    console.log(greeting, this.name + punctuation);
}

var person = {
    name: "Azeem"
};

introduce.call(person, "Hello", "!");
introduce.apply(person, ["Hi", "?"]);
```

Expected output:

```txt
Hello Azeem!
Hi Azeem?
```

Why:

- Both set `this` to `person`.
- `call` receives `"Hello", "!"`.
- `apply` receives `["Hi", "?"]` and spreads it into the function call.

### `bind`

`bind` does not call the function immediately.

It returns a new function with `this` fixed.

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop = {
    teacher: "Azeem"
};

var boundAsk = ask.bind(workshop);

boundAsk("What is bind?");
```

Expected output:

```txt
Azeem What is bind?
```

Why:

- `ask.bind(workshop)` creates a new function.
- In that new function, `this` is fixed to `workshop`.
- Calling `boundAsk(...)` uses `workshop` as `this`.

Common mistake:

```js
ask.bind(workshop, "Hello");
```

This does not print anything.

Why:

- `bind` returns a new function.
- You still need to call that new function.

Correct:

```js
var fn = ask.bind(workshop);
fn("Hello");
```

Expected output:

```txt
Azeem Hello
```

### Practice: Explicit Binding

Question 1:

```js
function show() {
    console.log(this.title);
}

var book = {
    title: "JavaScript"
};

show.call(book);
```

Answer:

```txt
JavaScript
```

Reason:

- `.call(book)` makes `this` equal to `book`.

Question 2:

```js
function add(a, b) {
    console.log(this.label, a + b);
}

var obj = {
    label: "Total:"
};

add.apply(obj, [2, 3]);
```

Answer:

```txt
Total: 5
```

Reason:

- `.apply(obj, [2, 3])` sets `this` to `obj`.
- It passes `2` and `3` as arguments.

Question 3:

```js
function say(msg) {
    console.log(this.name, msg);
}

var user = {
    name: "Ali"
};

var sayToAli = say.bind(user);
sayToAli("welcome");
```

Answer:

```txt
Ali welcome
```

Reason:

- `bind` creates a new function with `this` fixed to `user`.

## 6. Hard Binding

Hard binding means using `bind` to strongly fix `this`.

This is useful when a function will be passed somewhere else, like to `setTimeout`.

Original lecture example:

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        console.log(this.teacher, question);
    }
};

setTimeout(workshop.ask, 10, "Lost This?");

setTimeout(workshop.ask.bind(workshop), 10, "Hard bound This?");
```

Expected output:

```txt
undefined Lost This?
Azeem Hard bound This?
```

Why:

- First `setTimeout(workshop.ask, ...)` passes only the function.
- It does not pass the `workshop` object as the call site.
- So `this` is lost.
- `this.teacher` becomes `undefined`.
- Second `setTimeout(workshop.ask.bind(workshop), ...)` passes a bound function.
- The bound function keeps `this` as `workshop`.
- So `this.teacher` is `"Azeem"`.

Simple version:

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        console.log(this.teacher, question);
    }
};

var askLater = workshop.ask.bind(workshop);

askLater("Still works");
```

Expected output:

```txt
Azeem Still works
```

Why:

- `askLater` is a bound function.
- It remembers `workshop` as `this`.

### Practice: Hard Binding

Question 1:

```js
var obj = {
    value: 10,
    show() {
        console.log(this.value);
    }
};

var show = obj.show.bind(obj);
show();
```

Answer:

```txt
10
```

Reason:

- `bind(obj)` fixes `this` to `obj`.

Question 2:

```js
var obj = {
    value: 10,
    show() {
        console.log(this.value);
    }
};

var show = obj.show;
show();
```

Answer in non-strict browser script:

```txt
undefined
```

Reason:

- The method was copied into a plain variable.
- The call is `show()`, not `obj.show()`.
- So the object context is lost.

## 7. `new` Binding

When a function is called with `new`, JavaScript creates a new object and sets `this` to that new object.

Original lecture example:

```js
function ask(question) {
    console.log(this.teacher, question);
}

var newEmptyObject = new ask("What is new doing here?");
```

Expected output:

```txt
undefined What is new doing here?
```

Why:

- `new ask(...)` creates a new empty object.
- Inside `ask`, `this` points to that new object.
- The new object has no `teacher` property.
- So `this.teacher` is `undefined`.

The four things `new` does:

1. Creates a brand new empty object.
2. Links that object to another object, usually the function's `.prototype`.
3. Calls the function with `this` set to the new object.
4. If the function does not return an object, it returns `this`.

Constructor-style example:

```js
function Workshop(teacher) {
    this.teacher = teacher;
}

var jsWorkshop = new Workshop("Azeem");

console.log(jsWorkshop.teacher);
```

Expected output:

```txt
Azeem
```

Why:

- `new Workshop(...)` creates a new object.
- Inside `Workshop`, `this` is the new object.
- `this.teacher = teacher` adds a `teacher` property to the new object.

### Tricky Case: Returning A Primitive

```js
function User(name) {
    this.name = name;
    return "hello";
}

var user = new User("Ali");

console.log(user.name);
```

Expected output:

```txt
Ali
```

Why:

- A constructor returned a primitive string.
- With `new`, primitive returns are ignored.
- JavaScript returns the new object instead.

### Tricky Case: Returning An Object

```js
function User(name) {
    this.name = name;
    return {
        name: "Override"
    };
}

var user = new User("Ali");

console.log(user.name);
```

Expected output:

```txt
Override
```

Why:

- A constructor returned an object.
- With `new`, an object return replaces the new object.

### Practice: `new`

Question 1:

```js
function Person(name) {
    this.name = name;
}

var p = new Person("Sara");
console.log(p.name);
```

Answer:

```txt
Sara
```

Reason:

- `new` creates an object.
- `this.name = name` writes to that new object.

Question 2:

```js
function Person(name) {
    this.name = name;
}

var p = Person("Sara");
console.log(p);
```

Answer in non-strict browser script:

```txt
undefined
```

Reason:

- There is no `new`.
- The function does not return anything.
- So `p` gets `undefined`.

## 8. Default Binding

Default binding happens when none of the other rules apply.

Pattern:

```js
functionName();
```

There is no object before the dot.

Original lecture example:

```js
var teacher = "Azeem";

function ask(question) {
    console.log(this.teacher, question);
}

function askAgain(question) {
    "use struct";
    console.log(this.teacher, question);
}

ask("What is the non strict mode defaults?");

askAgain("What is the strict mode defaults?");
```

Important correction:

- The lecture notes say `"use struct"`.
- The real JavaScript strict mode directive is `"use strict"`.
- `"use struct"` does not turn on strict mode.

If the code is run exactly as written with `"use struct"` in a browser script:

```txt
Azeem What is the non strict mode defaults?
Azeem What is the strict mode defaults?
```

Why:

- `"use struct"` is just a normal string.
- Strict mode is not active.
- In non-strict browser script, default `this` is the global object.
- Top-level `var teacher = "Azeem"` creates `window.teacher`.

Correct strict mode version:

```js
var teacher = "Azeem";

function ask(question) {
    console.log(this.teacher, question);
}

function askAgain(question) {
    "use strict";
    console.log(this.teacher, question);
}

ask("What is the non strict mode defaults?");
askAgain("What is the strict mode defaults?");
```

Expected output in a browser script:

```txt
Azeem What is the non strict mode defaults?
TypeError: Cannot read properties of undefined
```

Why:

- `ask(...)` uses default binding.
- In non-strict browser script, `this` becomes the global object.
- `this.teacher` is `"Azeem"`.
- `askAgain(...)` is strict mode.
- In strict mode, default `this` stays `undefined`.
- Reading `this.teacher` causes a `TypeError`.

### Practice: Default Binding

Question 1:

```js
var name = "Global";

function print() {
    console.log(this.name);
}

print();
```

Answer in non-strict browser script:

```txt
Global
```

Reason:

- Default binding uses the global object.
- `var name = "Global"` becomes a global property in browser scripts.

Question 2:

```js
function print() {
    "use strict";
    console.log(this.name);
}

print();
```

Answer:

```txt
TypeError
```

Reason:

- In strict mode, default `this` is `undefined`.
- `undefined.name` is an error.

Question 3:

```js
function print() {
    "use strict";
    console.log(this);
}

print();
```

Answer:

```txt
undefined
```

Reason:

- Strict mode does not replace `this` with the global object.
- It leaves `this` as `undefined`.

## 9. Binding Precedence

Sometimes more than one rule seems possible.

Use this order:

1. `new` binding
2. Explicit binding: `call`, `apply`, `bind`
3. Implicit binding
4. Default binding

Original lecture example:

```js
var workshop = {
    teacher: "Kyle",
    ask: function ask(question) {
        console.log(this.teacher, question);
    },
};

new (workshop.ask.bind(workshop))("What Does this Do?");
```

Expected output:

```txt
undefined What Does this Do?
```

Why:

- `workshop.ask.bind(workshop)` creates a bound function.
- But the bound function is called with `new`.
- `new` has higher precedence for `this`.
- So `this` becomes the new object, not `workshop`.
- The new object has no `teacher`.
- So `this.teacher` is `undefined`.

Important note:

- `bind` still keeps bound arguments.
- But if a bound function is called with `new`, the bound `this` is ignored.

### Explicit Binding Beats Implicit Binding

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop1 = {
    teacher: "Azeem",
    ask: ask
};

var workshop2 = {
    teacher: "Ali"
};

workshop1.ask.call(workshop2, "Who is this?");
```

Expected output:

```txt
Ali Who is this?
```

Why:

- The code looks like `workshop1.ask`, so implicit binding seems possible.
- But `.call(workshop2, ...)` is explicit binding.
- Explicit binding wins.
- So `this` is `workshop2`.

### Practice: Precedence

Question 1:

```js
function show() {
    console.log(this.name);
}

var a = {
    name: "A",
    show: show
};

var b = {
    name: "B"
};

a.show.call(b);
```

Answer:

```txt
B
```

Reason:

- `.call(b)` wins over `a.show`.

Question 2:

```js
function ShowName() {
    console.log(this.name);
}

var obj = {
    name: "Object"
};

var BoundShowName = ShowName.bind(obj);
new BoundShowName();
```

Answer:

```txt
undefined
```

Reason:

- `new` wins over the bound `this`.
- The new object has no `name`.

## 10. Arrow Functions And Lexical `this`

Arrow functions do not create their own `this`.

If you use `this` inside an arrow function, JavaScript looks outward to find a `this` from an enclosing scope.

Simple analogy:

An arrow function asks its outer scope, "Can I use your `this`?"

It is not exactly hard binding. It is lexical lookup.

Original lecture example:

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        setTimeout(() => {
            console.log(this.teacher, question);
        }, 100);
    },
};

workshop.ask("Is this lexical? 'this' ? ");
```

Expected output:

```txt
Azeem Is this lexical? 'this' ?
```

Why:

- `workshop.ask(...)` calls the normal `ask` method.
- Inside `ask`, `this` is `workshop`.
- The callback passed to `setTimeout` is an arrow function.
- The arrow function does not make its own `this`.
- It uses `this` from `ask`.
- So `this.teacher` is `"Azeem"`.

### Nested Arrow Functions

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        (() => {
            (() => {
                console.log(this.teacher, question);
            })();
        })();
    }
};

workshop.ask("Still lexical?");
```

Expected output:

```txt
Azeem Still lexical?
```

Why:

- The inner arrows do not create their own `this`.
- They keep looking outward.
- They finally use `this` from `ask`.
- `ask` was called as `workshop.ask(...)`.

### Arrow Function As Object Method

Original lecture example:

```js
var workshop = {
    teacher: "Azeem",
    ask: (question) => {
        console.log(this.teacher, question);
    }
};

workshop.ask("What hapened to 'this' ?");
workshop.ask.call(workshop, "Still no 'this'? ");
```

Expected output in many environments where global `this.teacher` is not set:

```txt
undefined What hapened to 'this' ?
undefined Still no 'this'?
```

Why:

- `ask` is an arrow function.
- Arrow functions do not have their own `this`.
- `workshop.ask(...)` cannot set `this` for the arrow.
- `.call(workshop, ...)` also cannot set `this` for the arrow.
- The arrow looks outside the object.
- It does not use `workshop` as `this`.

If global `this.teacher` exists, the output can be different:

```js
var teacher = "Global Teacher";

var workshop = {
    teacher: "Azeem",
    ask: (question) => {
        console.log(this.teacher, question);
    }
};

workshop.ask("Who?");
```

Expected output in a browser script:

```txt
Global Teacher Who?
```

Why:

- The arrow uses outer/global `this`.
- In a browser script, global `this` is `window`.
- `var teacher = "Global Teacher"` creates `window.teacher`.

### Common Arrow Function Mistakes

Mistake 1: Using an arrow as an object method when you need dynamic `this`.

```js
var user = {
    name: "Sara",
    print: () => {
        console.log(this.name);
    }
};

user.print();
```

Expected output:

```txt
undefined
```

Why:

- Arrow functions ignore the object call site.
- `this` does not become `user`.

Better:

```js
var user = {
    name: "Sara",
    print() {
        console.log(this.name);
    }
};

user.print();
```

Expected output:

```txt
Sara
```

Mistake 2: Thinking `call`, `apply`, or `bind` can change arrow `this`.

```js
var obj = {
    name: "Object"
};

var arrow = () => {
    console.log(this.name);
};

arrow.call(obj);
```

Expected output in many environments:

```txt
undefined
```

Why:

- `.call(obj)` cannot change the `this` of an arrow function.

Mistake 3: Using `new` with an arrow function.

```js
var User = () => {};

new User();
```

Expected output:

```txt
TypeError: User is not a constructor
```

Why:

- Arrow functions cannot be used as constructors.

### Practice: Arrow Functions

Question 1:

```js
var obj = {
    value: 42,
    show() {
        var arrow = () => {
            console.log(this.value);
        };

        arrow();
    }
};

obj.show();
```

Answer:

```txt
42
```

Reason:

- `show` is called as `obj.show()`.
- So `this` inside `show` is `obj`.
- The arrow uses `this` from `show`.

Question 2:

```js
var obj = {
    value: 42,
    show: () => {
        console.log(this.value);
    }
};

obj.show();
```

Answer in many environments:

```txt
undefined
```

Reason:

- `show` is an arrow function.
- It does not get `this` from `obj`.

Question 3:

```js
var obj = {
    value: 42,
    show() {
        return () => {
            console.log(this.value);
        };
    }
};

var fn = obj.show();
fn();
```

Answer:

```txt
42
```

Reason:

- `obj.show()` sets `this` in `show` to `obj`.
- The returned arrow remembers that lexical `this`.
- Calling `fn()` later still prints `42`.

## 11. Classes

The lecture notes list `Class` as a topic.

A class is syntax for creating objects with shared methods.

```js
class Workshop {
    constructor(teacher) {
        this.teacher = teacher;
    }

    ask(question) {
        console.log(this.teacher, question);
    }
}

var js = new Workshop("Azeem");

js.ask("What is class?");
```

Expected output:

```txt
Azeem What is class?
```

Why:

- `new Workshop("Azeem")` creates a new object.
- `constructor` runs with `this` set to the new object.
- `this.teacher = teacher` stores `"Azeem"` on that object.
- `js.ask(...)` uses implicit binding.
- So `this` inside `ask` is `js`.

Class methods still follow normal `this` rules.

```js
class Workshop {
    constructor(teacher) {
        this.teacher = teacher;
    }

    ask(question) {
        console.log(this.teacher, question);
    }
}

var js = new Workshop("Azeem");
var ask = js.ask;

ask("Lost?");
```

Expected output:

```txt
TypeError
```

Why:

- Class methods run in strict mode.
- `ask("Lost?")` is a plain function call.
- There is no object before the dot.
- In strict mode, default `this` is `undefined`.
- `this.teacher` causes a `TypeError`.

Important:

```js
class Workshop {}

Workshop();
```

Expected output:

```txt
TypeError: Class constructor Workshop cannot be invoked without 'new'
```

Why:

- Classes must be called with `new`.

### Practice: Classes

Question 1:

```js
class User {
    constructor(name) {
        this.name = name;
    }

    print() {
        console.log(this.name);
    }
}

var u = new User("Sara");
u.print();
```

Answer:

```txt
Sara
```

Reason:

- `new` creates the object.
- `u.print()` sets `this` to `u`.

Question 2:

```js
class User {
    print() {
        console.log(this.name);
    }
}

var u = new User();
u.print();
```

Answer:

```txt
undefined
```

Reason:

- `this` is `u`.
- But `u` has no `name` property.

## 12. Prototypes

The lecture notes list `Prototypes` as a topic.

Every normal object can be linked to another object.

If JavaScript cannot find a property on the object itself, it looks up the prototype chain.

```js
var parent = {
    teacher: "Azeem"
};

var child = Object.create(parent);

console.log(child.teacher);
```

Expected output:

```txt
Azeem
```

Why:

- `child` does not have its own `teacher`.
- JavaScript looks at `child`'s prototype.
- The prototype is `parent`.
- `parent.teacher` is `"Azeem"`.

### Constructor Prototype

When you use `new`, the new object is linked to the constructor function's `.prototype`.

```js
function Workshop(teacher) {
    this.teacher = teacher;
}

Workshop.prototype.ask = function(question) {
    console.log(this.teacher, question);
};

var js = new Workshop("Azeem");

js.ask("Prototype?");
```

Expected output:

```txt
Azeem Prototype?
```

Why:

- `new Workshop("Azeem")` creates `js`.
- `js` is linked to `Workshop.prototype`.
- `ask` is found on `Workshop.prototype`.
- The call site is `js.ask(...)`.
- So `this` inside `ask` is `js`, not `Workshop.prototype`.

This is very important for quizzes:

```js
function Workshop(teacher) {
    this.teacher = teacher;
}

Workshop.prototype.teacher = "Prototype Teacher";

Workshop.prototype.ask = function() {
    console.log(this.teacher);
};

var js = new Workshop("Azeem");

js.ask();
```

Expected output:

```txt
Azeem
```

Why:

- `ask` is found on the prototype.
- But `this` is still `js`.
- `js.teacher` is `"Azeem"`.
- The prototype's `teacher` is not used because `js` already has its own `teacher`.

### Property Shadowing

```js
var parent = {
    name: "Parent"
};

var child = Object.create(parent);
child.name = "Child";

console.log(child.name);
console.log(parent.name);
```

Expected output:

```txt
Child
Parent
```

Why:

- `child.name = "Child"` creates an own property on `child`.
- It does not change `parent.name`.
- The child property shadows the prototype property.

### Practice: Prototypes

Question 1:

```js
var parent = {
    value: 10
};

var child = Object.create(parent);

console.log(child.value);
```

Answer:

```txt
10
```

Reason:

- `child` does not have `value`.
- JavaScript finds it on `parent`.

Question 2:

```js
var parent = {
    value: 10
};

var child = Object.create(parent);
child.value = 20;

console.log(child.value);
console.log(parent.value);
```

Answer:

```txt
20
10
```

Reason:

- Assignment creates `child.value`.
- It does not change `parent.value`.

Question 3:

```js
function User(name) {
    this.name = name;
}

User.prototype.print = function() {
    console.log(this.name);
};

var u = new User("Ali");
u.print();
```

Answer:

```txt
Ali
```

Reason:

- `print` is found on `User.prototype`.
- The call site is `u.print()`.
- So `this` is `u`.

## 13. Inheritance Vs Behavior Delegation

The lecture notes list `Inheritance vs Behavior Delegation` as a topic.

In many languages, inheritance means one class copies or receives behavior from another class.

In JavaScript, objects are linked to other objects. If an object cannot handle a property or method, it delegates the lookup to another object.

That is why many teachers call JavaScript's model behavior delegation.

### Behavior Delegation With `Object.create`

```js
var Workshop = {
    ask(question) {
        console.log(this.teacher, question);
    }
};

var jsWorkshop = Object.create(Workshop);
jsWorkshop.teacher = "Azeem";

jsWorkshop.ask("Delegation?");
```

Expected output:

```txt
Azeem Delegation?
```

Why:

- `jsWorkshop` does not have its own `ask` method.
- JavaScript finds `ask` on `Workshop`.
- The call site is `jsWorkshop.ask(...)`.
- So `this` inside `ask` is `jsWorkshop`.
- `jsWorkshop.teacher` is `"Azeem"`.

### Class Inheritance Uses Prototype Delegation

```js
class Workshop {
    constructor(teacher) {
        this.teacher = teacher;
    }

    ask(question) {
        console.log(this.teacher, question);
    }
}

class JavaScriptWorkshop extends Workshop {
    constructor(teacher, topic) {
        super(teacher);
        this.topic = topic;
    }

    describe() {
        console.log(this.teacher, this.topic);
    }
}

var js = new JavaScriptWorkshop("Azeem", "Objects");

js.ask("Class inheritance?");
js.describe();
```

Expected output:

```txt
Azeem Class inheritance?
Azeem Objects
```

Why:

- `JavaScriptWorkshop` extends `Workshop`.
- `super(teacher)` calls the parent constructor.
- `ask` is found through the prototype chain.
- `describe` is found on `JavaScriptWorkshop.prototype`.
- In both method calls, `this` is `js`.

### Practice: Delegation

Question 1:

```js
var parent = {
    say() {
        console.log(this.name);
    }
};

var child = Object.create(parent);
child.name = "Child";

child.say();
```

Answer:

```txt
Child
```

Reason:

- `say` is found on `parent`.
- But the call site is `child.say()`.
- So `this` is `child`.

Question 2:

```js
var parent = {
    name: "Parent",
    say() {
        console.log(this.name);
    }
};

var child = Object.create(parent);

child.say();
```

Answer:

```txt
Parent
```

Reason:

- `this` is `child`.
- `child` has no own `name`.
- JavaScript looks up the prototype chain and finds `parent.name`.

## 14. Fast Output Prediction Checklist

Use this process in quizzes:

1. Is the function an arrow function?
   - If yes, ignore `call`, `apply`, `bind`, and object call rules for `this`.
   - Look outward to find `this`.
2. If it is a normal function, is it called with `new`?
   - If yes, `this` is the new object.
3. Is it called with `call`, `apply`, or `bind`?
   - If yes, use the object passed there.
4. Is it called as `object.method()`?
   - If yes, `this` is the object before the method.
5. Otherwise, default binding applies.
   - Non-strict browser script: global object.
   - Strict mode: `undefined`.

## 15. Common Mistakes

Mistake 1: Thinking `this` means the function itself.

Wrong idea:

```js
function ask() {
    console.log(this === ask);
}

ask();
```

Expected output:

```txt
false
```

Why:

- `this` is based on how the function is called.
- It is not automatically the function object.

Mistake 2: Thinking `this` means the object where the function was written.

```js
var workshop = {
    teacher: "Azeem",
    ask: function(question) {
        console.log(this.teacher, question);
    }
};

var other = {
    teacher: "Ali",
    ask: workshop.ask
};

other.ask("Who?");
```

Expected output:

```txt
Ali Who?
```

Why:

- The function was first written inside `workshop`.
- But the call site is `other.ask(...)`.
- So `this` is `other`.

Mistake 3: Forgetting that `bind` returns a new function.

```js
function show() {
    console.log(this.name);
}

var user = {
    name: "Sara"
};

show.bind(user);
```

Expected output:

```txt

```

No output.

Why:

- `bind` creates a new function.
- The new function was never called.

Mistake 4: Misspelling `"use strict"`.

```js
function test() {
    "use struct";
    console.log(this);
}

test();
```

Expected output in non-strict browser script:

```txt
Window {...}
```

Why:

- `"use struct"` is not strict mode.
- It is just a string expression.

Mistake 5: Using arrow functions as object methods.

```js
var obj = {
    name: "Azeem",
    show: () => {
        console.log(this.name);
    }
};

obj.show();
```

Expected output in many environments:

```txt
undefined
```

Why:

- Arrow functions do not get `this` from the object call site.

## 16. Mixed Practice Quiz

### Question 1

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop = {
    teacher: "Azeem",
    ask: ask
};

workshop.ask("Ready?");
```

Answer:

```txt
Azeem Ready?
```

Reason:

- Implicit binding.
- `this` is `workshop`.

### Question 2

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop = {
    teacher: "Azeem",
    ask: ask
};

var fn = workshop.ask;
fn("Ready?");
```

Answer in non-strict browser script:

```txt
undefined Ready?
```

Reason:

- `fn()` is a plain function call.
- The object context was lost.

### Question 3

```js
function ask(question) {
    console.log(this.teacher, question);
}

var workshop = {
    teacher: "Azeem"
};

var other = {
    teacher: "Ali"
};

var boundAsk = ask.bind(workshop);

boundAsk.call(other, "Who?");
```

Answer:

```txt
Azeem Who?
```

Reason:

- `boundAsk` is already bound to `workshop`.
- `.call(other, ...)` cannot change that normal bound function's `this`.

### Question 4

```js
var teacher = "Global";

var workshop = {
    teacher: "Azeem",
    ask: () => {
        console.log(this.teacher);
    }
};

workshop.ask.call(workshop);
```

Answer in a browser script:

```txt
Global
```

Reason:

- `ask` is an arrow function.
- `.call(workshop)` cannot set arrow `this`.
- The arrow uses outer/global `this`.

### Question 5

```js
function Workshop(teacher) {
    this.teacher = teacher;
}

Workshop.prototype.ask = function() {
    console.log(this.teacher);
};

var js = new Workshop("Azeem");
var ask = js.ask;

ask();
```

Answer in non-strict browser script:

```txt
undefined
```

Reason:

- `ask` is copied into a plain variable.
- The call site is `ask()`.
- There is no object before the dot.
- Default binding is used.

### Question 6

```js
var parent = {
    teacher: "Parent",
    ask() {
        console.log(this.teacher);
    }
};

var child = Object.create(parent);
child.teacher = "Child";

child.ask();
```

Answer:

```txt
Child
```

Reason:

- `ask` is found on `parent`.
- But the call site is `child.ask()`.
- So `this` is `child`.

### Question 7

```js
class Workshop {
    constructor(teacher) {
        this.teacher = teacher;
    }

    ask() {
        console.log(this.teacher);
    }
}

var js = new Workshop("Azeem");
var fn = js.ask;

fn();
```

Answer:

```txt
TypeError
```

Reason:

- Class methods are strict mode.
- `fn()` has default binding.
- In strict mode, `this` is `undefined`.
- `this.teacher` causes an error.

### Question 8

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        setTimeout(() => {
            console.log(this.teacher, question);
        }, 10);
    }
};

var ask = workshop.ask;
ask("Lost?");
```

Answer in non-strict browser script:

```txt
undefined Lost?
```

Reason:

- The call site is `ask("Lost?")`, not `workshop.ask(...)`.
- So inside `ask`, `this` is the global object.
- The arrow uses `this` from `ask`.
- That `this` is not `workshop`.

### Question 9

```js
var workshop = {
    teacher: "Azeem",
    ask(question) {
        setTimeout(() => {
            console.log(this.teacher, question);
        }, 10);
    }
};

workshop.ask.call({ teacher: "Ali" }, "Who?");
```

Answer:

```txt
Ali Who?
```

Reason:

- `.call({ teacher: "Ali" }, ...)` sets `this` inside `ask`.
- The arrow callback uses `this` from `ask`.
- So it prints `"Ali"`.

### Question 10

```js
function Ask(question) {
    console.log(this.teacher, question);
}

var workshop = {
    teacher: "Azeem"
};

var BoundAsk = Ask.bind(workshop);

new BoundAsk("New?");
```

Answer:

```txt
undefined New?
```

Reason:

- `new` wins over the bound `this`.
- The new object has no `teacher`.

## 17. Final Memory Rules

- `this` is decided by the call site.
- `object.method()` means `this` is usually `object`.
- `call` and `apply` call now and set `this`.
- `bind` returns a new function with fixed `this`.
- Passing a method as a callback usually loses `this`.
- `new` creates a new object and makes `this` point to it.
- Default binding is global in non-strict browser scripts.
- Default binding is `undefined` in strict mode.
- Arrow functions do not have their own `this`.
- Prototype methods are shared, but `this` is still based on the call site.
- JavaScript objects delegate behavior through the prototype chain.
