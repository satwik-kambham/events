---
title: "Enabling Data Persistence"
description: ""
lead: ""
date: 2022-03-03T20:24:05+05:30
lastmod: 2022-03-03T20:24:05+05:30
draft: false
images: []
menu:
  docs:
    parent: ""
weight: 4
toc: true
---

At this point you might have noticed that after adding any todos, if you restart your application, the todos don't stay.

To do this we will need to store your todos to memory. There are 2-ways in which you can store data to disk.
Firstly, we can use a database. However, this involves complex setup and maintenance. The second way to do this is to store our data directly to a file.

The easiest way to do this is to use the [electron-store](https://www.npmjs.com/package/electron-store) package.

## Installing the package

To install the `electron-store` package all we need to do is run the following command:
```
npm install electron-store
```

## main.js vs index.js
You might have been wondering why we have 2 different script files (`main.js` and `index.js`). 

The reason is that `main.js` is the main script that runs even when you have multiple windows but `index.js` runs only when the `index.html` page which it is connected to is loaded.

So, if we want to have data persistance, we need to do the file handling inside `main.js` and then send it to `index.js` to display it.

## Storing and retrieving data
Now that we have the `electron-store` package installed we can import and use it in our application.

To import the library add the following lines of code at the beginning of `main.js`:
```javascript
const Store = require("electron-store");
```

Now let us create a variable from the `Store` class that we imported. Add the following lines below the code to import the library:
```javascript
const store = new Store();
```

Finally, let us create functions for storing and retrieving todos.
```javascript
// Load todos from disk and if not todos exist then return a blank array
function loadTodos() {
  return store.get("todos", []);
}

// Save the following todos
function saveTodos(todos) {
  store.set("todos", todos);
}
```

At this point you `main.js` should look like this:
```javascript
// Import from the electron library
const { app, BrowserWindow } = require("electron");
// Import electron store library
const Store = require("electron-store");

const store = new Store();

// Function to create a window
function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
  });

  // Load the html file inside the window
  win.loadFile("index.html");
}

// Create the window when the app is initialized
app.whenReady().then(() => {
  createWindow();
});

// Quit the app when the window is closed
app.on("window-all-closed", () => {
  app.quit();
});

// Load todos from disk and if not todos exist then return a blank array
function loadTodos() {
  return store.get("todos", []);
}

// Save the following todos
function saveTodos(todos) {
  store.set("todos", todos);
}
```

## Transferring data between main.js and index.js
At this point we have create function to store and retrieve data from disk. However, we have no way to send the retrieved data from `main.js` to `index.js` and updated data form `index.js` to `main.js`.

Before we do anything, we need to modify our `createWindow()` function to allow `index.js` to use node.js libraries. Update the `createWindow()` function to the following:

```javascript
function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false,
    },
  });

  // Load the html file inside the window
  win.loadFile("index.html");
}
```

### Import necessary libraries
Now that node.js libraries can be used inside all our script files we can import the necessary libraries.

Inside `main.js` modify
```javascript
// Import from the electron library
const { app, BrowserWindow } = require("electron");
```

to

```javascript
// Import from the electron library
const { app, BrowserWindow, webContents, ipcMain } = require("electron");
```

Inside `index.js` add the following line at the beginning:
```javascript
const { ipcRenderer } = require("electron");
```

### Sending todos from main.js to index.js
To load and send todos on app start, we have to modify our `createWindow()` function to the following:
```javascript
function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false,
    },
  });

  // Load the html file inside the window
  win.loadFile("index.html");

  // Send todos to index.html on the displayTodos channel
  win.webContents.on("did-finish-load", () => {
    win.webContents.send("displayTodos", loadTodos());
  });
}
```

### Receiving todos from main.js
To receive the todos add the following line to index.js:
```javascript
ipcRenderer.on("displayTodos", (event, todos) => displayTodos(todos));
```

### Sending updated todos from index.js
Since we are always going to call the `displayTodos()` function whenever our todos are updated we can add the following line to send the updated todos to `main.js`:

```javascript
ipcRenderer.send("updated-todos", Todos);
```

### Receiving updated todos from index.js
We can receive the updated todos from `index.js` and update our storage by adding the following to `main.js`:

```javascript
ipcMain.on("updated-todos", (event, todos) => {
  saveTodos(todos);
});
```

Now our app can store our todos even after the app is closed.

However there is one final thing we need to do which is to update the todo whenever a checkbox is clicked. We have already given a unique id to each checkbox so all we need to do is update the todos whenever the checkbox is clicked.

First let us create a function inside `index.js` which toggles our todos inside the variable where we store all our todos.
```javascript
function toggleTodo(i) {
  Todos[i].Done = !Todos[i].Done;
  displayTodos(Todos);
}
```

Next let us update our `displayTodos()` function to add event listeners to all our checkboxes:
```javascript
function displayTodos(todos) {
  // Get the div inside which we are going to store the todos
  todosHTML = document.getElementById("todos");

  // Clear the contents of the div
  todosHTML.innerHTML = "";

  let i = 0;
  todos.forEach((todo) => {
    todosHTML.innerHTML +=
      '<input type="checkbox" id="todo-check-' +
      i +
      '"' +
      (todo.Done ? "checked" : "") +
      "><span>" +
      todo.Description +
      "</span><br/>";
    i++;
  });

  ipcRenderer.send("updated-todos", Todos);

  for (let i = 0; i < Todos.length; i++) {
    document
      .getElementById("todo-check-" + i)
      .addEventListener("change", () => toggleTodo(i));
  }
}
```

Now the functionality for our app is done.

At the end your files should look like this:

`main.js`

```javascript
// Import from the electron library
const { app, BrowserWindow, webContents, ipcMain } = require("electron");
// Import electron store library
const Store = require("electron-store");

const store = new Store();

// Function to create a window
function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false,
    },
  });

  // Load the html file inside the window
  win.loadFile("index.html");

  // Send todos to index.html on the displayTodos channel
  win.webContents.on("did-finish-load", () => {
    win.webContents.send("displayTodos", loadTodos());
  });
}

// Create the window when the app is initialized
app.whenReady().then(() => {
  createWindow();
});

// Quit the app when the window is closed
app.on("window-all-closed", () => {
  app.quit();
});

// Load todos from disk and if not todos exist then return a blank array
function loadTodos() {
  return store.get("todos", []);
}

// Save the following todos
function saveTodos(todos) {
  store.set("todos", todos);
}

// Save updated todos
ipcMain.on("updated-todos", (event, todos) => {
  saveTodos(todos);
});
```

`index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'; img-src 'self' blob: data:;">
    <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'; img-src 'self' blob: data:;">
    <title>To-do list app</title>
  </head>
  <body>
    <h1>Todo List</h1>
    <div id="todos"></div>

    <input id="add-inp" type="text" />
    <button id="add-btn">Add</button>

    <script src="index.js"></script>
  </body>
</html>
```

`index.js`

```javascript
const { ipcRenderer } = require("electron");

let Todos = [];

function displayTodos(todos) {
  // Get the div inside which we are going to store the todos
  todosHTML = document.getElementById("todos");

  // Clear the contents of the div
  todosHTML.innerHTML = "";

  let i = 0;
  todos.forEach((todo) => {
    todosHTML.innerHTML +=
      '<input type="checkbox" id="todo-check-' +
      i +
      '"' +
      (todo.Done ? "checked" : "") +
      "><span>" +
      todo.Description +
      "</span><br/>";
    i++;
  });

  ipcRenderer.send("updated-todos", Todos);

  for (let i = 0; i < Todos.length; i++) {
    document
      .getElementById("todo-check-" + i)
      .addEventListener("change", () => toggleTodo(i));
  }
}

ipcRenderer.on("displayTodos", (event, todos) => displayTodos(todos));

function addTodo() {
  // Get the description from the text box
  let description = document.getElementById("add-inp").value;

  // Add the todo to the list
  Todos.push({
    Description: description,
    Done: false,
  });

  // Display the updated list of todos
  displayTodos(Todos);
}

// Call add todo when add button is clicked
document.getElementById("add-btn").addEventListener("click", () => addTodo());

function toggleTodo(i) {
  Todos[i].Done = !Todos[i].Done;
  displayTodos(Todos);
}
```