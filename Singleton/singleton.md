JavaScript Singleton Design Pattern

The Singleton Pattern ensures that only one instance of an object/class exists and provides a way to access that same instance throughout the application.

1. Singleton Using IIFE

A traditional JavaScript implementation uses an IIFE (Immediately Invoked Function Expression) and a closure.

const Singleton = (function () {
    let instance;

    function createInstance() {
        const object = new Object("I am the instance");
        return object;
    }

    return {
        getInstance: function () {
            if (!instance) {
                instance = createInstance();
            }

            return instance;
        }
    };
})();

Usage
const a = Singleton.getInstance();
const b = Singleton.getInstance();

console.log(a === b); // true


Both variables point to the same object.

How it works
Singleton
   |
   └── getInstance()
          |
          └── private instance


The instance variable is private because it is inside the IIFE.

The first call creates the object.

Later calls return the already-created object.

2. Singleton Using a Class

A Singleton can also be implemented using a JavaScript class.

class Singleton {
    constructor() {
        if (Singleton.instance) {
            return Singleton.instance;
        }

        this.message = "I am the instance";

        Singleton.instance = this;
    }

    getMessage() {
        return this.message;
    }
}

Usage
const a = new Singleton();
const b = new Singleton();

console.log(a === b); // true

console.log(a.getMessage());
// I am the instance


The first call creates the instance.

The second call returns the existing instance.

What happens?

First:

const a = new Singleton();


No instance exists, so a new object is created.

Singleton.instance
        |
        v
     Object A


Then:

const b = new Singleton();


An instance already exists, so the constructor returns it.

a ──────┐
        |
        v
     Object A
        ^
        |
b ──────┘


Therefore:

console.log(a === b); // true

3. Singleton Using static

A more explicit class-based implementation uses a static property and a static getInstance() method.

class Singleton {
    static instance;

    constructor() {
        if (Singleton.instance) {
            return Singleton.instance;
        }

        this.message = "I am the instance";

        Singleton.instance = this;
    }

    static getInstance() {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
        }

        return Singleton.instance;
    }

    getMessage() {
        return this.message;
    }
}

Usage
const a = Singleton.getInstance();
const b = Singleton.getInstance();

console.log(a === b); // true


The intended way to access the instance is:

Singleton.getInstance();


instead of:

new Singleton();

Flow
Singleton.getInstance()
          |
          v
   Is instance created?
       /        \
     No          Yes
     |            |
     v            v
Create instance  Return
     |           existing
     v           instance
Store instance
     |
     v
Return instance

4. Singleton Using ES Modules

Modern JavaScript modules make Singleton implementations much simpler.

Singleton.js
class Singleton {
    constructor() {
        this.message = "I am the instance";
    }

    getMessage() {
        return this.message;
    }
}

const instance = new Singleton();

export default instance;

app.js
import singleton from "./Singleton.js";

console.log(singleton.getMessage());
// I am the instance


Another file can import the same instance.

another.js
import singleton from "./Singleton.js";

console.log(singleton.getMessage());


Both files receive the same exported instance.

Diagram
Singleton.js
     |
     v
const instance
     |
     +--------> app.js
     |
     +--------> another.js
                   |
                   v
             Same instance

5. Module + Class + getInstance()

If you want the familiar getInstance() API, you can combine ES modules with a class.

Singleton.js
class Singleton {
    constructor() {
        this.message = "I am the instance";
    }

    getMessage() {
        return this.message;
    }
}

let instance;

export function getInstance() {
    if (!instance) {
        instance = new Singleton();
    }

    return instance;
}

app.js
import { getInstance } from "./Singleton.js";

const a = getInstance();
const b = getInstance();

console.log(a === b); // true


Here:

let instance;


is private to the module.

Only the exported function is available outside:

getInstance();

6. Recommended Modern Approach

For modern JavaScript applications, you often don't need to explicitly implement the Singleton pattern.

You can simply create one instance inside a module and export it.

Database.js
class Database {
    connect() {
        console.log("Connected to database");
    }

    disconnect() {
        console.log("Disconnected from database");
    }
}

const database = new Database();

export default database;


Then anywhere in the application:

import database from "./Database.js";

database.connect();


Another file:

import database from "./Database.js";

database.connect();


Both imports refer to the same module instance.

7. Why Does This Work?

ES modules are evaluated once and their exports are reused.

For example:

// Database.js

const database = new Database();

export default database;


The module doesn't create a completely new database object every time another file imports it.

Instead:

Database.js
     |
     | creates instance once
     v
  database
     |
     +------> app.js
     |
     +------> service.js
     |
     +------> controller.js
     |
     +------> another.js


All of them use the same instance.

8. Comparison
Approach	Private Instance	getInstance()	Modern
IIFE	Yes	Yes	No
Class	No / Partially	Optional	Yes
Class + Static	No / Partially	Yes	Yes
ES Module	Yes	Optional	Yes
Module + Class	Yes	Yes	Yes
9. Quick Interview Definition

Singleton is a design pattern that ensures a class or object has only one instance and provides a global access point to that instance.

Short Version

One instance + shared access = Singleton

10. Interview Example

A common real-world example is a database connection manager.

Without Singleton
const db1 = new Database();
const db2 = new Database();
const db3 = new Database();


This can create multiple database manager instances.

With Singleton
const db1 = Database.getInstance();
const db2 = Database.getInstance();
const db3 = Database.getInstance();

console.log(db1 === db2); // true
console.log(db2 === db3); // true


All parts of the application share the same instance.

11. Core Singleton Logic

The fundamental idea is:

let instance;

function getInstance() {
    if (!instance) {
        instance = new Something();
    }

    return instance;
}

First Call
getInstance()
     |
     v
instance doesn't exist
     |
     v
create instance
     |
     v
store instance
     |
     v
return instance

Later Calls
getInstance()
     |
     v
instance already exists
     |
     v
return existing instance


So:

const a = getInstance();
const b = getInstance();

console.log(a === b); // true

Key Concept

One instance + shared access = Singleton