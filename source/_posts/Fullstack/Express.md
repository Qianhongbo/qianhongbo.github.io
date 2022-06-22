---
title: Express
date: 2022-04-06 15:01:57
categories: 
- Fullstack
---

## HTTP Basic

### HTTP Request

- **GET** - retrieves data from the server
- **POST** - sends data to the server
- **DELETE** - removes data from the server
- **PUT** - replaces data on the server
- **PATCH** - updates data on the server

### Query Parameters

- Query strings are parameters in the URL, identified by a ‘?’
  - Ex: https://coffee.com/search?decaf=true
- To chain multiple parameters together in a query string, use ‘&”
  - Ex: https://coffee.com/search?decaf=true&size=large&creamer=soy

### HTTP Response Status Codes

| Status Code Range               | Example Code                                            |
| :------------------------------ | :------------------------------------------------------ |
| 100-199: information            | 100 Continue                                            |
| 200-299: request was successful | 200 OK 201 Created                                      |
| 300-399: request was redirected | 301 Moved Permanently 307 Temporary Redirect            |
| 400-499: client-side error      | 400 Bad Request 401 Unauthorized 405 Method Not Allowed |
| 500-599: server-side error      | 500 Internal Server Error                               |

## Express basics

### Application

Every Express application requires the creation of what is known as the application object. All of the core functions of express take place on the application object including endpoint methods.

```js
const app = express();
```

### Core Methods

- `.listen()` - listens for connections to a specified host and port
- `.get()` - used to get a route and takes a route and a callback function as arguments. The callback function takes two arguments, the request from the browser and the response from the server. Additionally, middleware can also be passed in as an argument which will be covered in the middleware section.
- `.post()`, `.put()`, `.delete()` - the other app methods that make up endpoints. They require having the ability to store data. `.post()` is used to post a new item, `.put()` used to edit an item already in existence, and `.delete()` to remove an item from the data. Like get above, all three methods will take a route.

### Create an endpoint

```tsx
app.get('/students', (req, res) => {
  // perform an action with req or res. 
});
```

## Middleware

Middleware is a function that is applied between the request and response. Meaning you get the request, do something with it, and then send the response. Common uses of middleware include checking the authentication status of a user before sending a response or logging the request before sending the response.

### Using middleware

#### `.use();`

The `.use();` method is a method that can be applied to the application object or to route objects. It is used for applying middleware and can take in a route, and middleware as arguments

```javascript
app.use(middleware);
```

#### Endpoint level

Applies middleware to a specific endpoint.

```javascript
students.get('/', middleware, (req, res) => { // do stuff });
```

### Writing middleware

Middleware is really just a function that is applied between the request and response. As such, if writing middleware, you create a function.

A middleware function takes at least 3 arguments (req, res, next); a 4th is also available of err (err, req, res, next) for use in writing error-handling middleware.

#### `next();`

The next method is a method from the express router. next() calls the next middleware in a chain of middlewares. Without adding next to your middleware function, your application will get stuck on the middleware.

```javascript
const myMiddleware = (req, res, next) => {
  // do stuff
  next();
};
```

### Working with routes

> The sample code for this topic is on the [website](https://github.com/Qianhongbo/JavaScript/tree/master/work_with_routes).

When building an express application, it's best practice to keep the server and application endpoints and functionality separate. With the router object, you're able to create a directory of routes and separate the functionality of each route onto its own file.

#### `Router()`

```js
import express from 'express';   
const routes = express.Router();

routes.get('/', (req, res) => { //do something });

export default routes;
```

#### Use the Router

```js
import routes from './routes/index';

app.use('/', routes);
```

## When to use Express

Everything we've done in this lesson so far has been the foundation of what's known as a REST API (Representational State Transfer). It's an architectural standard for APIs and currently the most popular type of API in use. RESTful APIs are considered to be stateless meaning the user/browser is independent of the server and they don't care what the other is doing.

But what if you need the interactions between the server and the user to be stateful meaning the server is aware of what the user is doing. Think about when you are waiting for someone to send a text message and you see the dots pop up letting you know they are working on a response--that's stateful. So what do we do when we need a real-time application? Instead of a REST API, you create a **WebSocket API.**

Websocket APIs are stateful and allow for real-time communication between the user and the server allowing for one user to know what another user is doing. Websockets do not use the HTTP protocol and have their own WebSocket protocol.
