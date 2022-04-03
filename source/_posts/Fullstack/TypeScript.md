---
title: TypeScript
date: 2022-04-01 11:56:13
categories: 
- Fullstack
tags: 
- Typescript
---

## Why TypeScript

### Types

Types are applied to:

- variables
- function parameters
- function returns
- object values.

### JavaScript is Weakly-Typed.

This means that types are assigned by the interpreter based on the data and makes an educated guess when the code's intention is ambiguous. This can lead to unintended results.

**Example:**

```undefined
2 + '2' = '22' 
```

### TypeScript Adds Typing to JavaScript

In short, TypeScript is a static and strong typed superset of JavaScript. When we're done with our TypeScript code, it compiles to JavaScript.

## Configure TypeScript

### Install TypeScript

```bash
npm init -y
```

```bash
$ npm i typescript // save to dependencies
$ npm i typescript --save-dev // save to devDependencies
```

Add `typescript`, `ts-node` and `@types/node` definitions to dev dependencies.

```bash
npm i --save-dev typescript
npm i --save-dev ts-node 
npm i --save-dev @types/node
```

### Creating your package.json Script

To use TypeScript, you need to add a script to your package.json file to compile TypeScript to JavaScript. This is generally called your "build" script but could be named anything.

```javascript
"scripts": {
  "build": "npx tsc"
},
```

To execute your `"build"` script use the following:

```bash
$ npm run build
```

Run the completed index file.

```bash
$ node build/.
```

`tsconfig.json` can also be named `jsconfig.json`.

To install the config file, run

```bash
$ npx tsc --init 
```

### Helpful configurations to note:

```javascript
{
  "compilerOptions": {
    "target": "es5",                          
    "module": "commonjs",                     
    "lib": ["ES2018", "DOM"], 
    "outDir": "./build",                        
    "strict": true,                           
    "noImplicitAny": true,                 
  },
  "exclude": ["node_modules", "tests"]
}
```

You will see many more options available than what is above. Your application may require additional settings to be configured, but these are typically the main settings to start with.

- `target` - sets what version of JS TypeScript will be transpiled to.
- `module` - sets what module system will be used when transpiling. Node.js uses the common.js module system by default
- `lib` - is used to say what libraries your code is using. In this case, ES2018 and the DOM API
- `outDir` - where you want your src code to output to. Often named build, prod, or server (when using it serverside)
- `strict` - enable strict typing
- `noImplicitAny` - disallow the `"any"` type (covered in TypeScript Basics)
- `exclude` - directories to exclude in compiling

## ES6 Modules

Now that we are using TypeScript for our application, we can also easily utilize the ES6 module system instead of the CommonJS module system. Destructuring should only be used when you are exporting the functions individually. If choosing to use export default, you must import the entire default as a module.

### Import

For importing modules, use the following syntax

```javascript
// Rename the module
import 'name' from 'module';

// Use destructuring to pull in specific functions when they are exported individually
import {function, function} from 'module';
```

### Export

```javascript
// Export an individual function or other type of object in code
export const myFunction = () => {};

// Export a single item at the end
export default object;

// Export a list of objects
export default {object1, object2};
```

## TypeScript Basics

###  Implicit Typing and Explicit Typing

#### Implicit Typing

TypeScript will automatically assume types of objects if the object is not typed. It is best practice to allow TypeScript to type immutable variables and simple functions implicitly.

```javascript
const myNum = 3; // TypeScript implicitly types myNum as a number based on the variable
```

Implicit Typing is a best practice when the app is self-contained (meaning that it does not depend on other applications or APIs) or variables are immutable.

![](https://video.udacity-data.com/topher/2021/March/603f9e26_fsjs-c1-l2-implicit-typing/fsjs-c1-l2-implicit-typing.jpg)

#### Explicit Typing

```javascript
let myVar: number = 3; // myVar has been explicitly typed as a number 
```

![](https://video.udacity-data.com/topher/2021/March/603fa213_fsjs-c1-l2-explicit-typing/fsjs-c1-l2-explicit-typing.jpg)

### Basic Types

`string` - used for string types, textual data

```javascript
let studentName:string;
studentName = 'Dae Lee'
```

------

`number` - used for number types including integers and decimals

```javascript
let studentAge: number;
studentAge = 10;
```

------

`boolean` - used for `true`/`false` types

```javascript
let studentEnriched: boolean;
studentEnriched = true;
```

------

`Union Types` - used when more than one type can be used

```javascript
let studentPhone: (number | string);
studentPhone = '(555) 555 - 5555';
studentPhone = 5555555555;
```

------

`null` - used when an object or variable is intentionally `null`, typically only functionally found in union types

```javascript
const getCapitals = (str:string):string[] | null => {
  const capitals = str.match(/[A-Z]/);
  return capitals;
}

console.log(getCapitals('something'));
// returns null
console.log(getCapitals('Something'));
// returns ['S']
```

------

`undefined` - used when a variable has yet to be defined

```javascript
const myFunc = (student: string | undefined) => {
  if ( student === undefined ){
    // do something
  } 
};
```

---

`void` - used as a return type when the function returns nothing

```javascript
const myFunc = (student: string): void {
  console.log(student);
};
```

------

`never` - used as a return type when the function will never return anything, such as with functions that throw errors or infinite loops

```javascript
const myError = (err: string): never {
  throw new Error(err); 
}
```

------

`any` - *should be avoided*. Used when the type of the item being typed can be anything

```javascript
const myFunc = (student: any): any => {
  // do something
};
```

------

`unknown` - used when the type of the thing being typed is unknown. Used heavily for type assertion

```javascript
const myFunc = (student: unknown): string => {
  // do something
}
```

### Type Assertions

Type Assertions are used to tell TypeScript that even though TypeScript thinks it should be one type, it is actually a different type. Common to see when a type is `unknown`

```javascript
const myFunc = (student: unknown): string => {
  newStudent = student as string;
  return newStudent;
}
```

### Object-like types

**Array** - to type as an array, use the type, followed by square brackets. Union types can be used to allow for multiple types in an array.

```tsx
let arr: string[]; // only accepts strings
let arr2: (string | number)[]; // accepts strings or numbers
```

------

**Tuple** - tuples are not native to JavaScript. When you know exactly what data will be in the array, and you will not be adding to the array or modifying the type for any value, you can use a tuple.

```typescript
let arr: [string, number, string]; // ['cat', 7, 'dog']
```

------

`enum` - enums are not native to JavaScript but are similar to enumeration used in other languages like C++ and Java. You use an enum when you have a constant set of values that will not be changed. By default, the values in an enum are also given a numeric value starting at 0. However, the numeric value can manually be set to any number explicitly or by calculation. Uses PascalCase to name the type.

```typescript
enum Weekend {
  Friday,
  Saturday,
  Sunday
}
```

### Objects and Interfaces

Objects are easily created in JavaScript due to JavaScript's weak typing. With TypeScript, they take a bit more work. It is possible to create an object in TypeScript, but TypeScript offers better tools for doing so.

**Object** - creating an object requires defining the object before setting values. Once you have defined the object, additional properties cannot be added to the type definition, making it unhelpful when you need to add more properties after creation.

```ts
let student:{ name: string, age: number, enrolled: boolean} = {name: 'Maria', age: 10, enrolled: true};
```

------

`interface` - Interfaces are a concept not native to javascript, but similar concepts exist in other languages like Java, C++, and Python, where you create an abstract class as an interface for creating classes. With TypeScript, interfaces are simply used as the blueprint for the shape of something. Interfaces can be used to create functions but are most commonly seen to create objects.

```tsx
interface Student { 
  name: string, 
  age: number, 
  enrolled: boolean
};
let newStudent:Student = {name: 'Maria', age: 10, enrolled: true};
```

```tsx
interface Undergrad extends Student {
  major: string;
}
```

#### Duck Typing

Duck Typing is a programming concept that tests if an object meets the duck test: "If it walks like a duck and it quacks like a duck, then it must be a duck."

TypeScript uses duck typing for interfaces, meaning that even though you may say a function takes in an argument of interface A, if interface B has the same properties of A, it will also accept B. Interface A is the duck, and Interface B walks and quacks like a duck, so we'll accept it as a duck too.

#### Optional and Readonly Properties

**Optional** - use when an object may or may not have a specific property by adding a `?` at the end of the property name.

```tsx
interface Student { 
  name: string, 
  age: number, 
  enrolled: boolean,
  phone?: number // phone becomes optional
};
```

------

`readonly` - use when a property should not be able to be modified after the object has been created.

```ts
interface Student { 
  name: string, 
  age: number, 
  enrolled: boolean,
  readonly id: number // id is readonly
};
```

### Type Aliases

#### With primitive types for documentation

```tsx
type Name = string;
```

#### Workes with unions and tuples

```tsx
type Input = string | number;
type Coord = [number, number];
```

Type Aliases can also be used as object. It is similar as interface.

### Classes

TypeScript classes look and behave very much like the classes introduced in ES6. The biggest addition to TypeScript classes is the addition of access modifiers. 

```tsx
class Student {
  protected studentGrade: number;
  private studentId: number;
  public constructor(grade: number, id: number) {
    this.studentGrade = grade;
    this.studentId = id;
  }
  id(){
    return this.studentId;
  }
}
```

### Factory Functions

```tsx
interface Student {
  name: string;
  age: number
  greet(): void;
}

const studentFactory = (name: string, age: number): Student =>{ 
  const greet = ():void => console.log('hello'); 
  return { name, age, greet };
}

const myStudent = studentFactory('Hana', 16);
```

## Generics and Asynchronous TypeScript

### Generics

We can combine the following function using generics.

```tsx
const getItem = (arr: number[]):number => {
  return arr[1];
}

const getItem = (arr: string[]):string => {
  return arr[1];
}
```

```tsx
// Generic Function
const getItem = <T>(arr: T[]):T => {
  return arr[1];
};
```

