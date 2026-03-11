### 1. What is Node.js?

**Answer:** A runtime environment that executes JavaScript outside the browser using Chrome’s V8 engine.  
**Explanation:** It enables server-side development with non-blocking I/O.

**V8** is Google’s high-performance JavaScript engine written in C++

### 2. Is Node.js single-threaded?

single thread ---> tasks run one after another [only one task at a time]
**Answer:** Yes , it is single threaded it uses event loop for handling the events  that takes long time for completing

### 3. What is event loop :

In a single threaded flow of tasks suppose a task is taking more time na then it send the task to the background [all this are done by node.js ] , and in the back ground the event loop manages the tasks [event loop checks weather the task is completed or not again and again and after the completion of the task the call back function {the  function which will be executed after the background running task} is called]

### 4. What is non-blocking I/O?

**Answer:** Operations that don’t block execution.

### 5. What is npm ?

node package manager that is used download and import libs , inside which there are pre defined codes and functions that can be readily used 

### 6. What is package.json?

**Answer:** Its is a file that contains Project metadata , dependencies , configuration [like versions]

### 7. what is the difference between dependencies and dev_dependencies:

- dependencies → needed in production [eg: express , mongoose etc..]
- devDependencies → needed in development [eg: nodemon , eslint etc..]

> [!info]+ Note:
> local : install both
> production : install only dependencies

### 8. commonjs and ESmodules:

Common js - uses require 
ESmodules - uses import

### 9.  require vs import :
#### require: 
i] synchronous 
ii] dynamic

```
### Key Characteristics

1. **Synchronous loading**
    

When Node executes:

const lib = require("./lib");

Node **loads and executes the file immediately** before continuing execution.

Flow:

line1  
require module  
execute module  
return exports  
line2 continues

Because it is synchronous, it can **block execution** if modules are heavy.

---

2. **Dynamic loading**
    

You can call `require()` anywhere.

Example:

if(condition){  
   const module = require("./module");  
}

This works because `require` runs at runtime.
```

#### import 
```
Modern JavaScript module system.

Example:

import express from "express";

Export example:

export default myFunction;

---

### Key Characteristics

#### 1. Static loading

`import` statements are **analyzed before code execution**.

Example:

import fs from "fs";

Node knows **all imports before running the file**.

This allows optimizations.

---

#### 2. Cannot be used conditionally

This is invalid:

if(condition){  
   import module from "./module";   ❌  
}

Because imports are resolved **before runtime**.

---

#### 3. Tree Shaking Support

Tree shaking = removing unused code during bundling.

Example module:

export function a(){}  
export function b(){}  
export function c(){}

Usage:

import {a} from "./lib";

Bundlers like **Webpack/Vite** include only `a`, removing `b` and `c`.

This reduces bundle size.

`require` cannot support this because it loads the entire module dynamically.
```


10. What is a Callback?

**Answer:**  
A **callback** is a function that is passed as an argument to another function and executed later.

```
function greet(name, callback) {
    console.log("Hello " + name);
    callback();
}

function sayBye() {
    console.log("Goodbye!");
}

greet("Jay", sayBye);
```

 > [!note]+ Callback incase of Async program :
> ```
> Callback Function in an Asynchronous Process
> 
> A callback function in async operations is a function that is executed after an asynchronous task finishes.
> 
> In Node.js, many operations take time (file reading, database calls, API requests). Instead of blocking the program, Node starts the task and continues executing other code.
> When the task completes, the callback function is triggered.
> 
> Example: File Reading
> const fs = require("fs");
> 
> fs.readFile("data.txt", "utf8", function(err, data) {
>     if (err) {
>         console.log(err);
>         return;
>     }
>     console.log(data);
> });
> 
> console.log("Program continues...");
> Execution Flow
> 
> fs.readFile() starts reading the file.
> 
> File reading is handled in the background (async).
> 
> Node does not wait for it to finish.
> 
> console.log("Program continues...") runs immediately.
> 
> When the file reading finishes → Node executes the callback function.
> ```

### 12. What is callback hell?

**Answer:** Nested callbacks causing unreadable code.  
**Explanation:** Solved using Promises or async/await.

### 13. What is a Promise?

**Answer:** Represents future completion of async operation.  
**Explanation:** States: pending, fulfilled, rejected.

```
const login = new Promise((resolve, reject) => {

    let passwordCorrect = false;

    if (passwordCorrect) {
        resolve("Login successful");
    } else {
        reject("Invalid password");
    }

});

login
.then(msg => console.log(msg))
.catch(err => console.log(err));
```

```
fs.promises.readFile("file1.txt", "utf8")
.then(data1 => fs.promises.readFile("file2.txt", "utf8"))
.then(data2 => fs.promises.readFile("file3.txt", "utf8"))
.then(data3 => console.log(data3))
.catch(err => console.log(err));
```


### 14. What is async/await?

**Answer:** async and await
**Explanation:** Makes async code look synchronous.

### 15. what is event emitter ? 


EventEmitter has **two main actions**.

1. **Listen for an event**
    
2. **Trigger (emit) an event**
    

Think of it like this:

Listener waits  
      ↓  
Event happens  
      ↓  
Listener function runs


```
const EventEmitter = require("events");

class Auth extends EventEmitter {}

const auth = new Auth();


// Listener 1 → log login activity
auth.on("userLogin", (username) => {
    console.log(`Activity Log: ${username} logged in`);
});


// Listener 2 → send notification
auth.on("userLogin", (username) => {
    console.log(`Notification: Welcome ${username}`);
});


// Listener 3 → update database
auth.on("userLogin", (username) => {
    console.log(`Database: updating last login for ${username}`);
});


// Simulate login
function login(username){
    console.log("User authenticated");

    auth.emit("userLogin", username);
}

login("Jay");
```

### 18. What are streams?

**Answer:** Handle large data in chunks.
**Explanation:** Types: readable, writable, duplex, transform.