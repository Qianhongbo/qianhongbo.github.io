---
title: Jasmine
date: 2022-04-03 12:01:39
categories: 
- Fullstack
---

## Configure Jasmine

### Install Jasmine:

1. To install Jasmine run:

   ```bash
   npm i jasmine 
   ```

2. Add a reporter for outputting Jasmine results to the terminal:

   ```bash
   npm i jasmine-spec-reporter
   ```

3. Add type definitions for Jasmine with :

   ```bash
   npm i --save-dev @types/jasmine
   ```

### Add Testing Scripts:

Add the scripts object in the `package.json` and add the followingto run Jasmine:

```bash
"jasmine": "jasmine"
```

### Set up the file structure

```
├── node_modules
├── spec
│      └── support
│           └── jasmine.json
├── src
│     ├──  tests
│     │     ├── helpers
│     │     │      └── reporter.ts
│     │     └── indexSpec.ts
│     └── index.ts
├── package-lock.json
├── package.json
└── tsconfig.json
```

### Add code to `reporter.ts`

```ts
import {DisplayProcessor, SpecReporter, StacktraceOption} from "jasmine-spec-reporter";
import SuiteInfo = jasmine.SuiteInfo;

class CustomProcessor extends DisplayProcessor {
    public displayJasmineStarted(info: SuiteInfo, log: string): string {
        return `${log}`;
    }
}

jasmine.getEnv().clearReporters();
jasmine.getEnv().addReporter(new SpecReporter({
    spec: {
        displayStacktrace: StacktraceOption.NONE
    },
    customProcessors: [CustomProcessor],
}));
```

### Add code to `jasmine.json`

```tsx
{
    "spec_dir": "build/tests",
    "spec_files": [
        "**/*[sS]pec.js"
    ],
    "helpers": [
        "helpers/**/*.js"
    ],
    "stopSpecOnExpectationFailure": false,
    "random": false
}
```

### Add code to `tsconfig.json`

```json
  "exclude": ["node_modules", "./dist", "spec"]
```

## Write a Basic Test

`index.ts`

```typescript
const myFunc = (num: number): number => {
  return num * num;
};

export default myFunc;
```

`indexSpec.ts`

```typescript
import myFunc from '../index';

it('expect myFunc(5) to equal 25', () => {
  expect(myFunc(5)).toEqual(25);
});
```

To test this we'll need to first run the build script and then the test script:

```bash
npm run build
npm run jasmine
```

## Jasmine syntax

> The sample code is in this [website](https://github.com/Qianhongbo/JavaScript/tree/master/jasmine_comparisons).

> - **Spec**: an individual test
> - **Suite**: a collection of similar tests related to one function

- Use the **describe** keyword followed by a short description of what the suite is testing and one or more specs.
- A best practice is to start a sentence with “it” and then complete the sentence with the description of what the suite is testing.

```tsx
describe(“suite description”, () => {
    it(“describes the spec”, () => {
        const myVar = true;
        expect(myVar).toBe(true);
    });
}); 
```

### Comparisons

- `.toEqual` : check the value

-  `.toBe` : check the object
- `.toBeTruthy()`passes when
  - The expectation has any non-zero value
  - The expectation evaluates to `true`
- `.toBeFalsy()` passes when the value is:
  - `0`
  - `''` (an empty string)
  - `undefined`
  - `null`
  - `NaN`
- `.toBeCloseTo(expected value, precision)`
  - Passes if a value is within a specified precision of the expected value
  - Precision is optional and is represented the number of decimal points to check (defaults to 2)
- `.toBeGreaterThan(expected value)`
- `.toBeLessThan(expected value)`
- `.toBeGreaterThanOrEqual(expected value)`
- `.toBeLessThanOrEqual(expected value)`
- use `.not` to negate the matcher

## Test asynchronous code

> The sample code is in this [website](https://github.com/Qianhongbo/JavaScript/tree/master/jasmine_asynchronous).

### async/await

```tsx
it('expects asyncFunc() result to equal value', 
  async () => {
    const result = await asyncFunc();
    expect(result).toEqual(value);
});
```

### promise

```tsx
it('expects asyncFunc() result to equal value',
  () => {
    return asyncFunc().then( result => {
        expect(result).toEqual(value);
    });
});
```

## Endpoint testing

> The sample code is in this [website](https://github.com/Qianhongbo/JavaScript/tree/master/endpoint_tesing).

### Benefits of Endpoint Testing

- Confirms that the server is working.
- Confirms that endpoints are configured properly.
- More efficient than manual testing.

### Setting Up Endpoint Testing

- Install Supertest as a dependency.

```bash
$ npm i supertest
```

- Add type definition to allow the code to compile without TypeScript errors.

```bash
$ npm i --save-dev @types/supertest. 
```

- Import SuperTest in the spec file.

```javascript
import supertest from 'supertest';
import app from '../index';

const request = supertest(app);
describe('Test endpoint responses', () => {
    it('gets the api endpoint', async (done) => {
        const response = await request.get('/api');
        expect(response.status).toBe(200);
        done();
    }
)});
```

- Create and Run Tests

```bash
$ npm run test
```

## Setup and teardown

- `beforeAll();`can be used to run some code before the specs run, and any log statements show up before the specs.

- `afterAll();` allows functionality to be added after all of the specs in a suite have run. Log statements will show after the specs.
- `beforeEach();` and `afterEach();` will run before or after each one of the individual specs.
- `fdescribe` and `fit` allows jasmine to focus on one specific suite, skipping the others
- `xdescribe` and `xit` allows Jasmine to skip a specific suite or test, running all others.
