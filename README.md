# Testing Practices and Principles
Notes for Kent C. Dodds course "Testing Practices and Principles" @FrontendMasters


## Index

[1. Jest](#1-jest)
[2. Tools](#2-tools)
[3. Articles](#3-articles)


## 1. Jest
### test(name, fn)
Basic test:
```javascript
//sum.js
export function sum(a, b) {
  return a + b;
}

//sum.test.js
import { sum } from './sum';
test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

### describe(name, fn)
Creates a block that groups together several related tests in one "test suite".
Basic layout of a describe block:
```javascript
describe('name of the test suite', () => {
    //test function
})
```

### Mock functions
Mock functions make it easy to test the links between code by erasing the actual implementation of a function, capturing calls to the function (and the parameters passed in those calls), capturing instances of constructor functions when instantiated with new, and allowing test-time configuration of return values.
Example:
```javascript
jest.mock(
    //relative path to module to mock
    './utils',
    () => {
        return{
            //function that replace the original function
            jest.fn(()=> /* function */)
        }
    }
)
```
[jestjs.io: Mock functions](https://jestjs.io/docs/en/mock-functions)

### spyOn
Jest spyOn allows the user to keep track of when existing functions are called and mock their implementations.
[jestjs.io: spyOn](https://jestjs.io/docs/en/jest-object.html#jestspyonobject-methodname)

### Explicit
Be explicit in tests, eg:
```javascript
expect().toHaveBeenCalledTimes(1);
//instead of
expect().toHaveBeenCalled();
```

### Database test
```javascript
test('getPost returns all posts in the database', async () => {
    //setup the request and response
    const req = {};
    const res = { json: jest.fn()};
    //test the functionality of the .getPosts method
    await postsController.getPosts(req, res);
    expect(res.json).toHaveBeenCalledTimes(1);
    //test the output of the .getPosts method
    const allPosts = await db.getPosts();
    expect(res.json).toHaveBeenCalledWith({posts: allPosts});
})
```

### Object factories
An object mother is a function/class used in testing to help create example objects that you use for testing.
[MartinFowler: Object mother](https://martinfowler.com/bliki/ObjectMother.html)
```javascript
//object factory
function setup() {
    const req = {
        //request object
    }

    const res = {};
    Object.assign(res, {
        function1: jest.fn(
            function function1() {
                //do something
            }.bind(res)
        ),
        function2: jest.fn(
            function function2() {
                //do something
            }.bind(res)
        ),
    })
    return {req, res};
}

//in the test
const {req, res} = setup();

//if the req or res needs additional objects, call them explicitly
req.body = { body: allPosts };
req.user = { id: testUser.id };
```

### Use schemas as test object
Check the received data against a schema. This reduces the confident level.
```javascript
//load the user from the database and check it against a schema
const userFromDb = await db.getUser(user.id);
expect(userFromDb).toEqual({
    id: expect.any(Number),
    name: expect.any(String)
})
```


## 2. Tools
### jest-in-case
Jest utility for creating variations of the same test.
[GitHub: jest-in-case](https://github.com/atlassian/jest-in-case)

### Instanbul for jest
Code coverage tool.
[Instanbul.js](https://istanbul.js.org/docs/tutorials/jest/)

## 3. Articles
### Co-locating tests
[Medium: What code comments can teach us about scaling a codebase](https://blog.kentcdodds.com/what-code-comments-can-teach-us-about-scaling-a-codebase-90bbfad8d70d)  

### Pure Modules
[Medium: Pure Modules](https://blog.kentcdodds.com/pure-modules-cbef580d1897)

### TDD
[Medium: TDD the RITE way](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)
[Medium: A quick introduction to test driven development with Jest](https://medium.freecodecamp.org/a-quick-introduction-to-test-driven-development-with-jest-cac71cb94e50)
[Pluralsight: Introduction to Test Driven Development in JavaScript](https://www.pluralsight.com/guides/introduction-to-test-driven-development-in-javascript)
[Flavio Copes: TESTING JAVASCRIPT WITH JEST](https://flaviocopes.com/jest/)