# Vite + Lit 

This tutorial covers creating a to-do list with web components. 

## What are Web Components 

Web Components are a new technology that is very interesting and has a lot of potential. In a nutshell, Web Components allow you, the developer, to create new HTML tags that have built-in functionality. 

Web components represent a new way to build web applications. They are used by Google, Salesforce, and many others. Notably, Photoshop for the web uses web components! 

## Lit

Lit is a library from Google that makes using Web Components easier by providing much of the boilerplate code you might write to use Web Components. 

For a look at how Web Components work with vanilla JS see my other tutorial: https://github.com/Tech-at-DU/simple-component

## Vite

Vite is a code bundler and server that is designed to be quick and efficient. It's highly extensible and works with React and Lit. 

This tutorial will use it to scaffold this project. 

## Getting Started 

Create a new Lit project with Vite. 

```
npm create vite@latest
```

Answer the questions with: 

- Name your project: "lit-todo-app"
- Choose Lit from the menu
- Choose JavaScript

This should create a new folder with some default files. 

CD to your directory open the newly created folder or open that folder with your code editor.

Install dependencies: 

```
npm i
```

From here you can run or build your project with the following commands. 

Run your project with: 

```
npm run dev
```

Or, build with: 

```
npm run build
```

## Test your project

Run: `npm run dev`. Open http://127.0.0.1:5173 in your browser. You should the default page created from the Vite started code. 

This page is created using a simple web component! 

There is a basic HTML page with some simple content, including some styles, and a button that counts, click it a few times. 

Explore the files. Take a look in the src folder. You should see index.html. Open this up. 

Note the CSS `<link rel="stylesheet" href="./src/index.css" />` This line links to CSS that applies to the page generally and it only positions the component and sets the background color. Web components can encapsulate their styles. 

You can remove this and see that some styles remain. 

Next, you'll see `<script type="module" src="/src/my-element.js"></script>` This line links to the JS file that backs up the custom component. This file defines a new web component which manifests as a new custom HTML tag. In this case, the tag is: `<my-element>`. You'll see this below in the body of the page, find it: 

```
<my-element>
  <h1>Vite + Lit</h1>
</my-element>
```

Web Components allow developers to create new HTML tags and these tags can "carry" their own JS and CSS with them. They can also generate and display HTML which is not seen in the HTML document. 

Take a look at `my-element.js`. 

At the top of the page, the import statements import things this component will use. 

A web component is defined as a JS class. Notice that this file defines the class: `MyElement` which extends `LitElement`. 

At the very bottom of the page, there is this line: `window.customElements.define('my-element', MyElement)`. This line defines a new custom element, which is a new HTML tag. The name of the tag is `my-element` which would be used as `<my-element>`, you saw this in `index.html`. The second argument to `customElement.define()` is the class that backs up the new custom element `MyElement` in this case. Each `<my-element>` created on a page creates a new instance of this class. 

**Important!** Custom elements must be named with a hyphen. For example: `my-element` or `todo-list` work but `myelement` and `todo_list` are invalid! Names must contain a hyphen to prevent collisions with existing tag names. 

## Create an app component

This will be the first component. First, you will use this to test a few Web Component and Lit concepts, later it will become the base component of your app. 

Create a new file: `src/todo-app.js`

Import your dependencies: 

```JS
import { LitElement, css, html } from 'lit'
```

Define a new class class and a custom element: 

```js
export class TodoApp extends LitElement {

}

window.customElements.define('todo-app', TodoApp)
```

This defines a new class `TodoApp` that extends `LitElement` and defines a new custom element `todo-app`. You will use this tag in your pages like this: `<todo-app></todo-app>`.

Try it out! Open `index.html`, and add this line to the `<head>` of the document: 

```html
<script type="module" src="/src/todo-app.js"></script>
```

Then add this to the `<body>`:

```html
<todo-app></todo-app>
```

Testing your page you should not see any errors, and everything should look the same, since the new custom tag doesn't render anything, yet. Inspecting your page you should see the custom tag, "open" it up and you'll see the `Shadow Content (Open)`. The custom element renders its content in this shadow node. 

### Add a render method 

Add a render method and render some content to this component: 

```js
render() {
  return html`
  <h1>Hello World</h1>`
}
```

The render method is where a Lit element displays its content. You'll use the `html` function imported from `lit` to generate html content. 

**Note!** `html` is a function. It makes use of a `tagged literal` expression. Read about it here: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates

In short, this is a function call that includes arguments from the following template string. 

Take a look at the browser. You should see `<h1>Hello World</h1>` in the browser. 

Inspect this element. You should find the `<h1>` in the "Shadow Content". 

### Style your Lit Element

Add styles using the `css` function (imported from `lit`) You'll use the static getter styles to provide your styles. Add the following to the `TodoApp` class: 

```JS
static get styles() {
  return css`
    h1 {
      font-size: 48px;
      color: tomato;
      font-family: Helvetica;
    }
  `
}
```

Check this browser. These styles should be applied to the `<h1>` inside the `TodoApp` component. 

**Note!** These styles only apply to the elements inside the TodoApp component and do not apply to elements outside! test it for yourself. 

Add: `<h1>Test</h1>` to the body of `index.html` notice that this tag doesn't pick up these styles. It would normally be if you used the selector `h1`. 

Styles inside a Web Component are encapsulated! 

### Slots

You can use a slot to display content inside the tag defined in the HTML document in your web component. 

Slots are part of native web components. Read more about slots here: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot.  Lit has a default implementation of this feature. 

Edit `<todo-app>` tag in `index.html`.

```html
<todo-app>Todo List</todo-app>
```

Notice this text is not displayed. If you inspect the tag using the inspector in the browser you will see that the text exists outside the "Shadow Content". 

To display content text edit `TodoApp.js` find the `render` method and edit it. 

```js
render() {
  return html`
  <h1><slot></slot></h1>`
}
```

Adding a `<slot></slot>` tag here displays the text you wrote inside the tag!

### Add an Event Listener

Lit elements use the same events you find in vanilla JS, the syntax is a little different. 

Edit `TodoApp.js`, find the `render` method, and make the following change: 

```JS
render() {
  return html`
  <h1 @click=${this._click}><slot></slot></h1>`
}
```

Notice we used the `@` symbol to signify an event, this is part of Lit Element. 

Next, you need to define an `_click` method. This can be any name you like.

```JS
_click(e) {
  console.log(e)
}
```

Clicking the `<todo-app>` tag in the browser will display the event object in the console. 

Read more about Lit Element events here: https://lit.dev/docs/components/events/

### Lit Components and reactive properties

Components can have reactive properties. Similar to "state" in React components. A change to a reactive property causes a component to render itself. 

Add the following to `TodoApp` class. This should be inside the class but outside any of the other methods. 

```JS
static get properties() {
  return {
    count: { type: Number },
  }
}
```

Here you defined a reactive property named `count` its type is Number. 

Add a `constructor` and use this to set the default value for the `count` property. 

```JS
constructor() {
  super()
  this.count = 0
}
```

**Note!** You must call `super()` in the constructor! 

Change the value of `count` on a click. Find the `_click` method and add the following: 

```JS
_click(e) {
  console.log(e)
  this.count += 1
}
```

Display the count in the `render` method. 

```JS
render() {
  return html`
  <h1 @click=${this._click}><slot></slot> ${this.count} </h1>`
}
```

Here you displayed `this.count` after the `<slot>`.

Test your work. The count should display after the slot content and should update each time you click the component! 

## Great work So far!

So far you have created a web component with lit. This required defining a new custom tag. Displaying this tag in the DOM renders that tag through the class definition that you created. 

You used `html` to generate HTML that was rendered by your component, and `css` to style your component. Remember content was rendered to the Shadow content of the element and CSS styles were encapsulated by the shadow content.  

You used a slot to pull the content of the original tag into your custom class, handled events, defined a reactive property, and updated the component when the property changed. 

## Making the Todo List 

Imagine the Todo List will be made of several Web Components. 

- TodoApp - is a container for the whole todo app
- TodoList - displays a list of TodoItems
- TodoItem - displays a single todo item

Each of the above will be a separate web component. if you've worked with React you may notice that this follows a similar structure that you might have used with React. 

One thing that you will need to do is create a system of communication between components. Pay attention to the tutorial and note to yourself where this happens. 

## Creating the TodoItem component 

Create a new file: `todo-item.js`

Import your dependencies at the top: 

```JS
import { LitElement, css, html } from 'lit'
```

Define a class that extends `LitElement`:

```JS
class TodoItem extends LitElement {
  
}
```

**Notice!** that we're naming the file with the name used for the tag in kebabcase and using camelcase for the name of the class! 

Give the new class some reactive properties.

```JS
class TodoItem extends LitElement {
  static get properties() {
    return {
      task: { type: String },
      completed: { type: Boolean },
    };
  }
}
```

Define a constructor with some default values: 

```JS
class TodoItem extends LitElement {
  static get properties() {
    return {
      task: { type: String },
      completed: { type: Boolean },
    };
  }

  constructor() {
    super()
    this.task = 'Default Task'
    this.completed = false
  }
}
```

Now add a `render` method: 

```JS
render() {
    return html`
      <label class="todo">
        <input 
          type="checkbox" 
          .checked=${this.completed} 
          @change=${this._handleCheckboxChange} 
        />
        <div 
          class="todo-text ${this.completed ? 'completed' : ''}">
            ${this.task}
          </div>
      </label>
    `;
  }
```

Notice that you're rendering the html that will be rendered by this element. 

Last define a new custom element for this class. Add the following line at the end of the class!

```JS
customElements.define('todo-item', TodoItem);
```

### Test the todo-item

Test out the Todo Item by adding it to your page. You can add this anywhere you need it! 

Where you add it will determine how it communicates with other components. 

You will also need to import the JS file that defines this new tag. You saw how to do that with the `todo-app.js`. This time you will import a component into another component. 

Open `todo-app.js` add the following line to the top of the page: 

```JS
import './todo-item.js'
```

**Notice!** You are not importing a defined export. Instead, you are just loading the file. This runs the JS it contains which runs the last line `customElement.define()` and it's this line that defines the new tag!

Next display the new tag in the render method: 

```JS
render() {
  return html`
  <h1 @click=${this._click}><slot></slot> ${this.count} </h1>
  <todo-item></todo-item>
  `
}
```

**Notice!** that I placed the tag at the end of the HTML this tag renders. You can place it anywhere and we don't need to have a single node hierarchy!

Test your work. You should see the new component below the name and count. 

### Style the Todo Item

Currently, the to-do item has no styles. Open `todo-item.js` and add a `static get styles` method. This method goes inside the class but not inside any other method there!  

```JS
static get styles() {
  return css`
    /* styles here */
  `;
}
```

You can style this any way you like! Keep in mind that these styles only apply this element. Also note that there are two classes in the markup: `todo` and `todo-text`. You can use those names here! 

Here is what I came up with: 

```JS
static get styles() {
    return css`
     .todo {
        display: flex;
        align-items: center;
        padding: 0.5rem;
        background-color: green;
        color: white;
        font-family: Helvetica;
        font-size: 16px;
        margin: 1px;
      }

      input {
        margin: 0.5rem;
      }
    `;
  }
```

**Note!** `rem` in this case refers to **root** em. The root in this case is the root element of this shadow node. In this case that is `label.todo`. Any descendent of this element that uses `rem` will refer to `label.todo` to find out how large a `rem` is! Which is 16px in this example. 

It is not possible to style the default checkbox. It is possible to make a Web Component that acts as a custom checkbox! 

### Setting properties of components

You can set the property of a component from outside that component by setting the attribute with the name of the property. 

Open `todo-app.js`, and find the `render` method. Find `<todo-item></todo-item>`. Remember, `todo-item` defines two properties: `task` and `completed`.

```JS
<todo-item .task="Hello World"></todo-item>
```
This tests the `task` property to: "Hello World". 

Try setting `completed="true"`

**Note!** You used the . (dot) in front of the property name. This is part of the Lit Element syntax allowing you to set a property defined in the class that backs this custom element. 

## Make a todo list component 

Todo List will display a list of Todo Items. It will be a web component and a Lit Element. It should have a reactive property that holds a list of objects describing tasks.

Start with a new file `todo-list.js`. Add your dependencies. This component will display `todo-item`s so include that. 

```JS
import { css, html, LitElement } from 'lit';
import './todo-item.js';
```


Define a new class and define a new custom element. 

```JS
class TodoList extends LitElement {
  
}

customElements.define('todo-list', TodoList);
```

Define some reactive properties for this class. 

```JS
static get properties() {
  return {
    todos: { type: Array },
  };
}
```

Add a constructor: 

```JS
constructor() {
  super();
  this.todos = []
}
```

Next, define a `render` method: 

```JS
render() {
  return html`
    ${this.todos.length === 0
      ? html`<div class="no-todos">You have no to-do items</div>`
      : this.todos.map(
          (todo) =>
            html`<todo-item task=${todo.task} .completed=${todo.completed}></todo-item>`
        )}
  `;
}
```

Here the `render` method displays the message "You have no to-do items" when the `todos` list is empty and maps the list to `<todo-item>` tags if the list is not empty. 

The code here assumes that the `todos` contains a list of objects that have a `task` and `completed` properties. 

There are no styles yet but we can add them if needed. Stub the `static get styles` method. 

```JS
static get styles() {
    return css`
     /* CSS Styles here */
    `;
  }
```

### Test the Todo List component 

Let's render a Todo List component. Let's place this inside the Todo App component. 

Import the `todo-list` at the top of `todo-app.js`:

```JS
import './todo-list.js'
```

Now add `<todo-list></todo-list>` inside the `render` method. This is what I have, but you could place this anywhere here. 

```JS
render() {
  return html`
  <h1 @click=${this._click}><slot></slot> ${this.count} </h1>
  <todo-item task="Hello World" completed="true"></todo-item>
  <todo-list></todo-list>
  `
}
```

Testing your app now the `todo-list` should render the message "You have no to-do items" since the `todos` list is empty. 

Remove that `<todo-item>` tag since you'll be rendering todo items in the list. You can also remove the counter and everything associated with it.

```JS
render() {
  return html`
  <h1><slot></slot></h1>
  <todo-list></todo-list>
  `
}
```

Now pass a list of todos to the `todo-list`: 

```JS
render() {
  return html`
    <h1><slot></slot></h1>
    <todo-list .todos=${
      [
        {task:'Foo', completed:false}, {task:'Bar', completed:true}
      ]
    }></todo-list>
  `
}
```

**Notice!** You are setting `.todos`, this is the property name with a dot. Also note that all of this is inside of a template string so the value needs to be wrapped in the `${}`.

This should be displayed as a list of two to-do items. 

## Creating new Todo Items

To create new Todo Items you need to add a form. It's easy for us to pass information down to child components and harder to pass information up to parent components and siblings. For this reason, placing the form in `TodoApp` will be best. 

### Define a reactive property to hold a list of todos

Open `todo-app.js`. Define a property for the task name and the list of to-do items. 

```JS
  static get properties() {
    return {
      todos: { type: Array },
      taskName: { type: String }
    }
  }
```

**Note!** You are setting the property name and its type. 

Now find the constructor and define these properties with default values. 

```JS
constructor() {
  super()
  this.todos = []
  this.taskName = ''
}
```

Now edit the `render` method so that it uses the `this.todos` in place of the mocked-up list. 

```JS
render() {
  return html`
  <h1><slot></slot></h1>
  <todo-list .todos=${this.todos}></todo-list>
  `
}
```

### Add a form to create a new todo item

Add a form to the render method. 

**Note!** This could be another Web Component! In this case, we will keep it simple and make it part of the todo-app component, consider this a stretch challenge! 

```JS
render() {
  return html`
  <h1><slot></slot></h1>
  <form>
    <label>
      Task Name
      <input 
        type="text"
        placeholder="New Task"
      />
    </label>
    <button type="submit">Add Todo</button>
  </form>
  <todo-list .todos=${this.todos}></todo-list>
  `
}
```

This is just a start, you will need to modify this. What do we have so far? 

A form tag groups form elements and will handle creating a new task when the button is clicked. 

A label and an input allow us to enter the name of the new task. The input is type text and displays some placeholder text. 

Since the button is type "submit" the form will handle a click on the button with a submit event! 

Handle submit events. Modify the `<submit>` tag:

```JS
<form @submit="${this.onSubmit}">
```

Add `onSubmit` as a method of this class.

```JS
onSubmit(e) {
  e.preventDefault()
  this.todos = [...this.todos, {task: this.taskName, completed: false}]
  this.taskName = ''
}
```

Here you are preventing the default behavior of submitting a form, which is to load a page, in this case, it would refresh this page which would reset everything. 

Next, you are copying the list of todos into a new list and adding a new todo at the end. 

The last line clears the task from the input after it is submitted. 

**Important!** You must create a new list for Lit Element to see that this property has changed and trigger a render! 

Now modify the `<input />` tag. 

```JS
<input 
  type="text"
  placeholder="New Task"
  .value="${this.taskName}"
  @input="${e => this.taskName = e.target.value}"
/>
```

Here you are setting the value of the input to `this.taskName` to reflect the value of that property as the text displayed by the input. You are handling input events with `@input`. These input events use an arrow function to set `this.taskName` to the value entered into the input. 

### Test your work!

Add a new item by entering a name, then click the Add Todo button. You should see a new item added to the list! 

### Styling the form

Add a `static get styles` method: 

```JS
static get styles() {
  return css`
    /* styles here */
  `;
}
```

Here is what I did for styles: 

```JS
static get styles() {
  return css`
      h1 {
        font-size: 48px;
        color: tomato;
        font-family: Helvetica;
      }
      form {
        display: flex;
      }
      label {
        display: flex;
        flex-direction: column;
        font-family: Helvetica;
        font-size: 16px;
      }
      input {
        padding: 0.5rem;
        font-size: 1rem;
        border: 1px solid;
        margin: 0.5rem 1px;
      }
      button {
        border: none;
        background-color: tomato;
        font-size: 1rem;
        padding: 0.5rem 1rem;
        margin: 0.5rem 0;
        height: calc(2rem + 5px);
        align-self: flex-end;
      }
    `;
  }
```

## Stretch challenges! 

Do you want to learn this? You need to write some code! Try these challenges: 

- Add a button, checkbox, or other UI option to sort todos based on completion. Completed todos at bottom. 
- Add a new priority property to each to-do item. Set it with a select tag. This could have options of "Low", "Normal", or "High". Display the priority in the to-do item. Set the priority in the form. Save this as a new property along with the task and completed. 
- Add a date property to each to-do item. Display the date along with the task name. Get the current date and save that as a part of the todo object with the task, and completed. Bonus challenge, create a web component that displays a formatted date. It should take a date object as a property display and format it.
- Create a component for the form. To do this you'll need to store the properties in the parent (TodoApp) and set the setter function on the child (TodoForm)




