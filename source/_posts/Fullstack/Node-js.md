---
title: Node.js
date: 2022-03-07 22:08:58
categories: 
- Fullstack
tags: 
- Node.js
---

## History of Backend Development

![](https://video.udacity-data.com/topher/2021/February/60351af5_screen-shot-2021-02-23-at-9.04.16-am/screen-shot-2021-02-23-at-9.04.16-am.png)

## pros and cons

### The Node.js Advantage

- Node.js allows for JavaScript to be used on the frontend and backend.
- Node.js allows for easy application scaling and maintenance.

### The Node.js Disadvantage

Node.js is **not** well-suited for applications that require heavy processing and computation, like video processing, 3D games, and traffic mapping.

## Javascript with Node.js

### To run `index.js` use:

```bash
$ node src/index.js
```

## Common JS Module System

The module system creates the ability to export and import JavaScript from separate files.

### Export

```javascript
// working file = util/logger.js

// exports as object
module.exports = { 
    myFirstFunction: myFirstFunction,
    mySecondFunction: mySecondFunction
}

// using ES6 shorthand property names
module.exports = { 
    myFirstFunction,
    mySecondFunction
}
```

### Require

```javascript
// working file = index.js
// all functions in util/logger.js are available
const logger = require('./util/logger.js');

// using ES6 object destructuring, only myFirstFunction is available
const { myFirstFunction } = require('./util/logger.js');
```

## Event Loop

Nearly every Node.js feature is considered to be asynchronous (non-blocking). This means that we can request an API using promises and have our application continue running while that request is being waited for. But how does Node.js process that asynchronous request? Both the Browser and Node.js take advantage of something called the Event Loop. The Event Loop isn't an API or language; it's a process that runs anytime you have asynchronous code.

The Event Loop controls the order in which results (output) of asynchronous tasks (input) are displayed. Think of the Event Loop as the person working the door at an exclusive venue. That person lets people in based on a set of information provided by the venue. Your application is the venue, your asynchronous tasks are the people trying to get in, and it's your job to tell the door person how to do so. Once you become familiar with the Event Loop and the order in which Node.js handles tasks, you will control when those tasks occur in your application

### Six Phases of The Event Loop

1. **Timers** - executes callbacks using timers. If there are timers set to `0 ms` or `setImmediate(),` they will run here. Incomplete timers will run in later iterations of the loop.
2. **Pending** - *internal phase*
3. **Idle/Prepare** - *internal phase*
4. **Poll** - process I/O callbacks
5. **Check** - execute any `setImmediate()` timers added in the Poll phase
6. **Close** - loop continues if there are more timers or I/O calls. If all timers and I/O calls are done, the loop closes and the process ends.

![](https://video.udacity-data.com/topher/2021/February/6033fb75_fsjs-c1-l1-event-loop/fsjs-c1-l1-event-loop.jpg)

## NPM

### Initializing `npm` and Creating a `package.json` file

Initializing npm will create a package.json within the root of your application folder containing general information about the project.

To initialize `npm` and go through all of the settings use:

```bash
$ npm init
```

To automatically select all defaults use `-y`

```bash
$ npm init -y 
```

### Adding Dependencies

> www.npmjs.com is the node package management library.

Applications will either include both dependencies and devDependencies or just dependencies. It is dependent on the team setting up the project. `devDependencies` are thought of as dependencies that are only necessary for development whereas `dependencies` are those dependencies used in both development and production. 

An example would be needing TypeScript added as a dependency for development, but since it compiles to standard JavaScript to be used in production, TypeScript is not needed for production and therefore could be just a `devDependency`. 

```bash
$ npm i module-name // install module to dependencies
$ npm i --save-dev module-name // install to dev dependencies
$ npm i --save-dev module-name@1.19 // install a specific version (1.19 here) of module
```

Installing dependencies adds the dependency to your package.json file in the format:

```javascript
  "devDependencies": {
    "prettier": "^2.2.1"
  }
```

The version states what was installed, but it also clarifies how it can be updated should you remove the node_modules and package-lock.json files and reinstall all dependencies with `$ npm install`.

### package-lock.json

`package-lock.json` contains all of the information for the dependencies of the modules you have installed.

It is best practice to add `package-lock.json` as well as .`/node_modules` to your `.gitignore` file when using a repository. The `node_modules` folder can grow rapidly, containing thousands of files. It is best to clone a repository without `node_modules` and run `npm install` to reinstall all dependencies of the project directly from npm.

### npm update

running `$ npm update` will update all of your dependencies based on the specifications given in your package.json file.

### scripts

To run a script that you have added to your package.json file, simply run `$ npm run` argument with the name of the script as the argument.

```bash
$ npm run prettier
```

```json
// touch .prettierrc
{
    "semi": true,
    "singleQuote": true
}
```

```js
"scripts": {
    "prettier": "prettier --config .prettierrc 'NPM/**/*.js' --write"
  }
```

## Compiled vs. Interpreted Language

### Compiled Language

- Language is written and compiled to machine code inside of an application
- Errors are detected during compiling
- The code won’t compile until it’s error-free
- Examples: C, C++, Erlang, Go

### JavaScript is an Interpreted Language

- Errors found when the code is run
- The interpreter translates and runs code one statement at a time
- Interpreted code runs more slowly

### Node.js Is an Interpreter

- Node.js is mostly written in C++
- Many node modules also include some C++ code
- Other modules include Python or C
