---
title: Angular Basis
date: 2022-06-20 12:07:47
categories: 
- Fullstack
---

# Angular Basis

## Angular CLI

The **Angular CLI** is a command-line interface (CLI) that allows developers to initialize, develop, scaffold, and maintain Angular applications directly from the command line. In short, the Angular CLI allows us to:

- **Create a new Angular application** from the ground up `ng new`
- **Add features** to the application, such as additional components, support for external libraries, and updates to the application and its dependencies `ng generate`
- **Run unit tests** : `ng test`
- **Run a development server** with live reload (i.e., automated page reloads when a change in the application is detected) `ng serve`
- **Re-build the application** on change (think Webpack)
- **Build the application** for production `ng build`

## Install and Configure the Angular CLI

```zsh
npm install -g @angular/cli
```

> -g means global

```bash
ng --version
```

## Create a New Angular Project

```bash
ng new my_first app
```

```bash
ng serve
```

## Component

### What is a component?

- Fundamental building block of an Angular application
- Controls the view displayed as HTML output
- Components determines the user interface of an Angular app

### What makes up a component?

- An **HTML template** (*.html file) that defines the user interface (i.e., what is actually rendered on the page)
- A **TypeScript component class** (*.ts file) containing the behavior and logic of the component. The component class supports and interacts with the view through the properties and methods defined in it
- **CSS styles** (*.css file) that define the presentation and style of the component

### Visualizing Components

All of the boxes in the following image are components.

![](https://video.udacity-data.com/topher/2021/March/604d07e8_fsjs-c3-l2-components/fsjs-c3-l2-components.jpg)

### Generate a Component

```bash
ng generate component <name>
# example: 
ng generate component Photos
```

```shell
# Result:
~$ ng generate component Photos
CREATE src/app/photos/photos.component.css (0 bytes)
CREATE src/app/photos/photos.component.html (21 bytes)
CREATE src/app/photos/photos.component.spec.ts (626 bytes)
CREATE src/app/photos/photos.component.ts (275 bytes)
UPDATE src/app/app.module.ts (475 bytes)
```

The Angular CLI tells us that four files were created:

- The Photos component CSS styles
- The Photos component HTML template
- The Photos component unit test (optional)
- The Photos component class (TypeScript)

### What is in a Component Class

```typescript
// src/app/photos/photos.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-photos',
  templateUrl: './photos.component.html',
  styleUrls: ['./photos.component.css']
})
export class PhotosComponent implements OnInit {

  constructor() { }

  ngOnInit(): void {
  }

}
```

The component class contains a `@Component` **decorator**. A decorator is a feature that provides a way to add annotations and meta-programming syntax for class declarations. Angular needs this metadata so that it knows how to build that particular component. Specifically, it takes a look at a few properties:

- `selector`: The identifying name for the component, which can be used in an HTML template
- `templateUrl`: The path of the HTML template file associated with component
- `styleUrls`: The path(s) for the file(s) containing CSS stylesheets that the component can use

The rest of the file contains an exported `PhotosComponent` class, with two methods:

- `constructor()`, which is used primarily for dependency injection and initializing variables. 
- `ngOnInit()`, which is called after the component has initialized. The `ngOnInit()` method is a lifecycle method used to handle additional initialization tasks.

### Data in the Component Class

```typescript
// students.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-student',
  templateUrl: './student.component.html',
  styleUrls: ['./student.component.css']
})
export class StudentComponent implements OnInit {
  name: string = "Taylor";
  grade: number = 12;

  constructor() { }

  ngOnInit(): void {
  }

}
```

In the above example, we define two properties:

- `name`, which points to a `string` with the value `"Taylor"`
- `grade`, which points to a `number` with the value `12`

We can use these data in our html file with double curly braces.

```html
<!-- student.component.html -->

<p>{{ name }} is a student in grade {{ grade }}.</p>
```

As it turns out, the double curly braces syntax helps facilitate **interpolation**. By interpolating data, what we mean is that under the hood, Angular first evaluates the data properties (e.g., `name` and `grade`) of the component class (e.g., `StudentComponent`). It then converts them to strings before finally rendering everything on the page:

```
Taylor is a student in grade 12.
```

As such, we can say that the `name` and `grade` properties are *bound* (i.e., through **data binding**) to the component class.

### The component lifecycle

Every Angular component goes through a cycle: from the moment it is created and mounted to the document object model (DOM), all the way to the moment it is unmounted and ultimately destroyed. This is what we refer to as the **component lifecycle**.

![](https://video.udacity-data.com/topher/2021/March/604d05aa_fsjs-c3-l2-component-lifecycle/fsjs-c3-l2-component-lifecycle.jpg)

### Lifecycle Methods

The answer to the question lives in the component's **lifecycle methods**. These predefined methods in the component class allow us to run any code or logic during any particular stage of the component's lifecycle:

- `ngOnChanges()`
- `ngOnInit()`
- `ngDoCheck()`
- `ngAfterContentInit()`
- `ngAfterContentChecked()`
- `ngAfterViewInit()`
- `ngAfterViewChecked()`
- `ngOnDestroy()`

Note that these methods are called *automatically* during specific points in time of the lifecycle (e.g., `ngOninit()` during initialization). You won't need to manually invoke these functions in your application.

Another thing to keep in mind that usage of these methods is not required. That is, you won't need to use leverage lifecycle methods in every single Angular application that you build. That said, using certain methods is considered standard in the industry and can greatly benefit your application's functionality.

### `ngOnInit()`

The `ngOnInit()` lifecycle method is called during the `OnInit` event, which occurs during the initializing phase of a component. It is one of the most common lifecycle methods you'll see and use, and is only called *once* during a component's lifecycle.

```typescript
// countries.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-countries',
  templateUrl: './countries.component.html',
  styleUrls: ['./countries.component.css']
})
export class CountriesComponent implements OnInit {
  countryList: object[] = [];

  constructor() { }

  ngOnInit(): void {
    this.countryList = [
      {
        name: 'Spain',
        population: 46754778
      },
      {
        name: 'New Zealand',
        population: 4822233    
      },
      {
        name: 'United States of America',
        population: 331002651    
      }
    ]
  }

}
```

We first import `OnInit` from `@angular/core` (which is where all lifecycle events are imported from). When `OnInit` occurs, the variable `countryList`, which is initially an empty array, is available for us to use.

At this point, Angular automatically invokes `ngOnInit()` for us, the lifecycle method containing the code that we want to run during this initialization. In particular, we reassign the value of the component class's `countryList` property to a new array of objects. This way, when the component mounts to the DOM, the `countryList` will be populated with an array of objects containing the data that we need, and the template will have access to the country data.

> ### 💡️ Can I Just Use the `constructor()` Instead? 
>
> On the surface, it seems like the `constructor()` would be a logical place to keep initialization tasks as well.
>
> Recall that the `constructor()` is called *before* `ngOnInit()` in the component lifecycle. That is, data-bound properties haven't be set and ready to use yet. If we tried referencing `countryList` in the `constructor()`, it would be an `undefined` value rather than the array we'd expect.
>
> As such, the `constructor()` should generally only be used for dependency injection (which we'll visit later in this course), and not much more. Ideally, your `constructor()` functions should be kept relatively simple, and away from business logic.



## Modules

### What is a Module?

In general, a module collects a block of code dedicated to a single purpose.

- Cohesive, organized set of functionality
- TypeScript 
- At least one root module(AppModule)
- Generated with the Angular CLI

### Recall

Recall the terminal output when we ran the command, `ng generate component Photos`:

```bash
~$ ng generate component Photos
CREATE src/app/photos/photos.component.css (0 bytes)
CREATE src/app/photos/photos.component.html (21 bytes)
CREATE src/app/photos/photos.component.spec.ts (626 bytes)
CREATE src/app/photos/photos.component.ts (275 bytes)
UPDATE src/app/app.module.ts (475 bytes)
```

The Angular CLI reports that four files were created, and one file was updated: `src/app/app.module.ts`. This is because generating an Angular component involves two steps:

1. Creating the component files
2. Registering the newly-generated component to a module (recall that a module is a set of closely-related capabilities or functionalities)

As such, the Angular CLI automatically registers the module to the root application module, the `AppModule`.

### The `AppModule`

So, what happens to the `AppModule` when it is updated by the Angular CLI? Specifically, by registering the component, the Angular CLI updates a property called `declarations`:

```typescript
// src/app/app.module.ts

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
// The new component is automatically imported:
import { PhotosComponent } from './photos/photos.component';

@NgModule({
  declarations: [
    AppComponent,
    PhotosComponent // The new component is added to declarations
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Registering a component with a module is crucial, as it allows the component to be recognized by Angular. After importing `PhotosComponent` using ES6 `import` syntax, it is added to the `declarations` array, which defines all the components that live within the module. If the new component weren't included here, and we try to use that component in our application, Angular will throw an error!

### Generate a Module

```bash
~$ ng generate module books
CREATE src/app/books/books.module.ts (191 bytes)
```

The file created will have everything ready for you to start registering your components!

```typescript
// src/app/books/books.module.ts

import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  declarations: [],
  imports: [
    CommonModule
  ]
})
export class BooksModule { }
```

We can open the folder of the module and create a component. This component will belong to the module.

After we create a new module, we should add this module to `AppModule` in the imports part.

## Directives

### Types

The main responsibility for directives is to manipulate the DOM. There are three kinds of directives in Angular:

- **Components** are directives with a template
- **Structural directive** manipulate the DOM by adding or removing DOM elements
- **Attribute directives** modify the appearance or behavior of a DOM element

### `ngFor`

```html
<!-- countries.component.html -->

{{ countryList }}

<!-- 
Browser output:

[object Object],[object Object],[object Object] 
-->
```

```html
<!-- countries.component.html -->

<ul>
  <li *ngFor="let country of countryList">
    {{ country.name }} has {{ country.population }} people.
  </li>
</ul>

<!-- 
Browser output:

- Spain has 46754778 people.
- New Zealand has 4822233 people.
- United States of America has 331002651 people.
-->
```

### `ngIf`

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-blogs',
  templateUrl: './blogs.component.html',
  styleUrls: ['./blogs.component.css']
})
export class BlogsComponent implements OnInit {
  title: string = 'My Favorite Blogs';

  constructor() { }

  ngOnInit(): void {
  }

}
<!-- blogs.component.html -->

<p *ngIf="title === 'My Favorite Blogs'">
  This is a list of my favorite blogs!
</p>

<!-- 
Browser output:

This is a list of my favorite blogs!
-->
```

### Attribute Directives

Along with structural directives, we can also leverage attribute directives to help manipulate the DOM.

Recall that the attribute directive's main responsibility is to modify the behavior or appearance of a DOM element. Consider `ngStyle`, which is an attribute directive that updates styles for the containing HTML element:

```typescript
// header.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.css']
})
export class HeaderComponent implements OnInit {
  title: string = 'Header';

}
<!-- header.component.html -->

<p [ngStyle]="{'font-size': title === 'Header' ? '48px' : '12px' }">This is the header.</p>
```

The `Header` component class above contains a `title` property whose value is set to `'Header'`. The component's template leverages the `ngStyle` directive to conditionally apply styles directly to the paragraph element.

## Sending data

### Passing Data From Parent to Child: `@Input`

Angular gives us a useful tool to facilitate passing data from a parent component to its child component. By leveraging the `@Input` decorator in the child component class, we can get it ready to receive data from its parent.

### Example

#### PostItem Class

```typescript
export class PostItemComponent implements OnInit {
  @Input() post: Post;

  constructor() { 
    this.post = {
      id: 0,
      title: '',
      body: '',
      votes: 1
    }
  }

  ngOnInit(): void {
  }
```

#### PostItem html

```html
<h3>{{ post.title }}</h3>
<p>{{ post.body }}</p>
<p>Votes: {{ post.votes }}</p>
<br>
```

#### Posts html

```html
<!-- still use ngFor to iterate posts. 
Rather than show the posts use list, pass each post to its child (post item) -->
<app-post-item *ngFor = "let post of posts" [post] = "post"></app-post-item>
```

## Event binding

**Event binding** allows us to listen for and respond to such user actions. That is, we detect when something has occurred on a target DOM element, and in response, we can invoke certain function methods in the template's associated component class. As a result, with event binding, we can send information from the template *to* its corresponding component class.

Consider the following example of an HTML template.

```html
<!-- click.component.html -->

<button (click)="onClick()">Click me!</button>
```

On the `<button>` element, note the block of code that says `(click)="onClick()"`. This shows standard event binding syntax in Angular, which is made of two parts:

- The **target event** name in parentheses to the left of the equal sign (i.e., `click`)
- The **template statement** on the right of the equal sign, which is the method (`onClick()`) called in the component class whenever the target event occurs.

Back in the component class, then, we might see something like this:

```typescript
// click.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-click',
  templateUrl: './click.component.html',
  styleUrls: ['./click.component.scss']
})
export class ClickComponent implements OnInit {

  clickAmount: number = 0;

  constructor() { }

  ngOnInit(): void {
  }

  onClick(): void {
    this.clickAmount += 1;
  }

}
```

## Sending data to a parent component

In many real-world applications, data for the book list, as well as any associated data for that list, might live in the parent `BooksList` component. As such, the parent needs to be notified of certain actions that occur in its child component.

To accomplish this, the child component needs to *emit an event up* to its parent. We can leverage the `@Output` decorator as well as the `EvenEmitter` class to do just that:

```typescript
// book-item.component.ts

import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-book-item',
  templateUrl: './book-item.component.html',
  styleUrls: ['./book-item.component.css']
})
export class BookItemComponent implements OnInit {
  @Input() book;
  @Output() marked = new EventEmitter();

  constructor() { }

  ngOnInit(): void {
  }

}
```

Similar to the event binding that we saw on the previous page, we can bind an event handler method from the parent `BooksList` component to the output property of its child component.

```html
<!-- books-list-component.html -->

<h1>List of available books</h1>
<app-book-item *ngFor="let book of bookList" [book]="book" (marked)="markAsFavorite(book)"></app-book-item>
```

In the above template, we "capture" the event emitted from the child component on the `marked` output property. This way, when the user clicks the button, the `BooksList` component invokes its `markAsFavorite()` method:

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-books-list',
  templateUrl: './books-list.component.html',
  styleUrls: ['./books-list.component.css']
})
export class BooksListComponent implements OnInit {
  bookList: object[] = [];

  constructor() { }

  ngOnInit(): void {
    this.bookList = [
      {
        id: 1,
        title: 'Pride and Prejudice',
      },
      {
        id: 2,
        title: 'The Great Gatsby',
      },
      {
        id: 3,
        title: 'The Catcher in the Rye',
      }
    ]
  }

  markAsFavorite(book: any): void {
    console.log(`${book.title} has been added to favorites.`);
  }
}
```

## Navigation

## Configuring the Angular Router

In order to map a component to a particular path, most of the work involves configuring `routes`, which is an array of `Route` objects. Each object in the array defines a route in the application, as well as the component that responds to that route.

For example, consider a web application that shows information about the libraries in your local county. Its `routes` may look something like this:

```typescript
// app-routing.module.ts

import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './components/home/home.component';
import { ContactComponent } from './components/contact/contact.component';
import { LibrariesComponent } from './components/pages/libraries/libraries.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'contact', component: ContactComponent },
  { path: 'libraries/:id', component: LibrariesComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

```html
<!-- app.component.html -->

<app-header></app-header>

<router-outlet></router-outlet>
```

### Setting Up Hyperlinks

At this point, our routes do indeed work, but only if our users directly access them. That is, if the user types the URL (e.g., `http://localhost:4200/contact`) directly into the browser.

This, of course, wouldn't be an optimal experience for users of your application. In order to make anchor tags (`<a>`) work properly in the browser, we must add the proper route to the `routerLink` directive. For example:

```html
<!-- navigation.component.html -->

<h1>Library Directory</h1>

<nav>
  <a routerLink="/">Home</a>
  <a routerLink="/contact">Contact us</a>
</nav>
```

Since we are building a single-page application, using `routerLink` (versus `href`) allows us to navigate to other components without reloading the page. Note that unlike the `path` property of the `Route` objects we saw previously, there is a preceding `/` that we use before the component name in the value for `routerLink`.

