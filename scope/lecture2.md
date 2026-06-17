—-----------
Function Expression:

function teacher() {}

var myTeacher = function anotherTeacher() {
    console.log(anotherTeacher);
}

console.log(teacher);
console.log(myTeacher);
console.log(anotherTeacher);

//+ One of the key differences is between function declaration another function expression is function declaration attach their name to the enclosing scope where as function expression attach themsolve to their own scope *//

// Key difference function expression but their identifier in their own scope

// Let's talk about named function expressions
// A function expression that is given a name

// Anonymys function expression

// You should 100% use with zero expection use named function expression but why is that?

// 1. A named function expression produces a reliable self reference inside itself. such as recursive

// 2. More debugable stack trace

// 3. More self documenting code

—---------------

Arrow functions

var ids = persons.map((person) => person.id);

var ids = persons.map(function getId(person) {
    return person.id
})

//
getPerson().then(person => getDate(person.id)).then(renderData);

getPerson().then(function getDataFrom(person){
    return getData(person.id);
}).then(renderData);

