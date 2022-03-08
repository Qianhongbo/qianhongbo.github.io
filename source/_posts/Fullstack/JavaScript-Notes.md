---
title: JavaScript Notes
date: 2022-03-07 22:08:43
categories: 
- Fullstack
tags: 
- JavaScript
---

## Null, Undefined and NaN

### null means "value of nothing"

```javascript
var x = null
```

### undefined means "absence of value"

```javascript
var x;
console.log(x);
```

### NaN means "not a number"

```javascript
// calculating the square root of a negative number will return NaN
Math.sqrt(-10)

// trying to divide a string by 5 will return NaN
"hello"/5
```

## Equality

```js
"1" == 1
```

> **Returns:** true

```js
"1" === 1
```

> **Returns:** false

## Function

```javascript
function add(x, y) {
  var sum = x + y;
  return sum;
}
var sum = add(1, 2);
```

> If you don't explicitly define a return value, the function will return `undefined` by default.

## Hoisting

```javascript
sayHi("Julia");

function sayHi(name) {
  console.log(greeting + " " + name);
  var greeting;
}
// undefined Julia
```

```js
sayHi("Julia");

function sayHi(name) {
  console.log(greeting + " " + name);
  var greeting = "Hello";
}
// undefined Julia

/*
The about function is equal to the following function...
function sayHi(name) {
  var greeting;
  console.log(greeting + " " + name);
  greeting = "Hello";
}
*/
```

## Function Expressions

```js
var catSays = function(max) {
  var catMessage = "";
  for (var i = 0; i < max; i++) {
    catMessage += "meow ";
  }
  return catMessage;
};
```

Notice how the `function` keyword no longer has a name.

```js
var catSays = function(max) { 
  // code here 
};
```

It's an **anonymous function**, a function with no name, and you've stored it in a variable called `catSays`.

### Use function expressions as parameters

```js
// function expression catSays
var catSays = function(max) {
  var catMessage = "";
  for (var i = 0; i < max; i++) {
    catMessage += "meow ";
  }
  return catMessage;
};

// function declaration helloCat accepting a callback
function helloCat(callbackFunc) {
  return "Hello " + callbackFunc(3);
}

// pass in catSays as a callback function
helloCat(catSays);
```

### Inline function expressions

```js
var favoriteMovie = function displayFavorite(movieName) {
  console.log("My favorite movie is " + movieName);
};

function movies(messageFunction, name) {
  messageFunction(name);
}

movies(favoriteMovie, "Finding Nemo");
```

```js
function movies(messageFunction, name) {
  messageFunction(name);
}

movies(function displayFavorite(movieName) {
  console.log("My favorite movie is " + movieName);
}, "Finding Nemo");
```

## Arrays

```js
// creates a `donuts` array with three strings
var donuts = ["glazed", "powdered", "jelly"];
```

But strings aren’t the only type of data you can store in an array. You can also store numbers, booleans… and really anything!

```js
// creates a `mixedData` array with mixed data types
var mixedData = ["abcd", 1, true, undefined, null, "all the things"];
```

You can even store an array in an array to create a **nested array**!

```js
var arraysInArrays = [
  [1, 2, 3], 
  ["Julia", "James"], 
  [true, false, true, false]
];
```

### Indexing

```js
var donuts = ["glazed", "powdered", "sprinkled"];
console.log(donuts[0]);
```

### Length

```js
var donuts = ["glazed", "powdered", "sprinkled"];
console.log(donuts.length);
```

### Push

```js
var donuts = ["glazed", "chocolate frosted", "Boston creme", "glazed cruller", "cinnamon sugar", "sprinkled"];
donuts.push("powdered"); // the `push()` method returns 7 because the `donuts` array now has 7 elements
```

### Pop

```js
var donuts = ["glazed", "chocolate frosted", "Boston creme", "glazed cruller", "cinnamon sugar", "sprinkled", "powdered"];
donuts.pop(); 
```

### Loop

```js
var donuts = ["jelly donut", "chocolate donut", "glazed donut"];

for (var i = 0; i < donuts.length; i++) {
    donuts[i] += " hole";
    donuts[i] = donuts[i].toUpperCase();
}
```

### forEach loop

```js
var donuts = ["jelly donut", "chocolate donut", "glazed donut"];

donuts.forEach(function(donut) {
  donut += " hole";
  donut = donut.toUpperCase();
  console.log(donut);
});
```

The function that you pass to the `forEach()` method can take up to three parameters. In the video, these are called `element`, `index`, and `array`, but you can call them whatever you like.

```js
words = ["cat", "in", "hat"];
words.forEach(function(word, num, all) {
  console.log("Word " + num + " in " + all.toString() + " is " + word);
});
```

### Map

With the `map()` method, you can take an array, perform some operation on each element of the array, and return a new array.

```js
var donuts = ["jelly donut", "chocolate donut", "glazed donut"];

var improvedDonuts = donuts.map(function(donut) {
  donut += " hole";
  donut = donut.toUpperCase();
  return donut;
});
```

> **donuts array:** ["jelly donut", "chocolate donut", "glazed donut"]
> **improvedDonuts array:** ["JELLY DONUT HOLE", "CHOCOLATE DONUT HOLE", "GLAZED DONUT HOLE"]

## Object

```js
var sister = {
  name: "Sarah",
  age: 23,
  parents: [ "alice", "andy" ],
  siblings: ["julia"],
  favoriteColor: "purple",
  pets: true,
  paintPicture: function() { return "Sarah paints!"; }
};

sister.paintPicture();
```

> **Returns**: "Sarah paints!"
