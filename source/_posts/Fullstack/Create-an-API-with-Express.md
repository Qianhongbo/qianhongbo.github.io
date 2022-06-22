---
title: Create an API with Express
date: 2022-05-19 21:04:44
categories: 
- Fullstack
---

## RESTful APIs

### Definition

- RESTful: **R**epresentational **S**tate **T**ransfer(-**ful**) **A**pplication **P**rogramming **I**nterface.

- REST: A pattern for organizing API endpoints.

### There are five actionable RESTful routes for APIs:

| URL(Routes)          | CRUD   |
| -------------------- | ------ |
| GET ['/cats']        | INDEX  |
| GET ['/cats/:id']    | SHOW   |
| POST ['/cats']       | CREATE |
| PUT ['/cats/:id']    | EDIT   |
| DELETE ['/cats/:id'] | DELETE |

## Define functions

In the `src/handles/book.ts` file. We write some functions to handle the query.

```ts
import express, { Request, Response } from 'express'
import { Book, BookStore } from '../models/book'

const store = new BookStore();

// get all the records
const index = async (_req: Request, res: Response) => {
  const books = await store.index();
  res.json(books);
};

// show one specific record
const show = async (req: Request, res: Response) => {
  const book = await store.show(req.params.id);
  res.json(book);
}

// create an record
// need to put the details in the request body
const create = async (req: Request, res: Response) => {
  try {
    const book: Book = {
        title: req.body.title,
        author: req.body.author,
        total_pages: req.body.total_pages,
        summary: req.body.summary,
    }
    const newBook = await store.create(book)
    res.json(newBook)
  } catch(err) {
      res.status(400)
      res.json(err)
  }
}

// delete a record
const destroy = async (req: Request, res: Response) => {
  const deleted = await store.delete(req.params.id)
  res.json(deleted)
}

const book_routes = (app: express.Application) => {
  app.get('/products', index);
  app.get('/products/:id', show);
  app.post('/products', create);
  app.delete('/products/:id', destroy);
}

export default book_routes;
```

## Add routes to server

In the `src/server.ts`, we can just use one line to call the function in `handler/book.ts`

```ts
import express, { Request, Response } from 'express'
import bodyParser from 'body-parser'
import book_routes from './handles/book';

const app: express.Application = express()
const address: string = "0.0.0.0:3000"

// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

app.get('/', function (req: Request, res: Response) {
  res.send('Hello World!');
})

// just use this line to handle the query!
book_routes(app);

app.listen(3000, function () {
  console.log(`starting app on: ${address}`)
})
```

## Full Stack Big Picture

- Express handles incoming HTTP requests to the API and the handler functions call model methods
- Model methods query the database and send the information back to the handler, which parses it into json and sends the HTTP response

![full stack summary](https://video.udacity-data.com/topher/2021/March/605cf319_l4-create-an-api-with-express-1/l4-create-an-api-with-express-1.png)
