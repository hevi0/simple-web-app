# Setup

## Prerequisites
- Node.js 14+

## Install node packages
```sh
npm install
```

## Running the application

```sh
npm run start
```

# Data model
![Data Model](https://github.com/hevi0/simple-web-app/blob/main/datamodel.png)

This web application is responsible for managing content in the form of rotating banners (or carousel). In the database, they're called **slide_module**. The **slide_module** also contains children called **slide**. For now, the only content supported is text.

You should consider the **slide_module** as the owner of its **slides**. In other words, a **slide** won't be shared with other **slide_modules**.

## slide_module properties
- name: a human-friendly string for identifying the module
- transition_delay_secs: an integer that controls the time between transition to the next slide on the frontend

## slide properties
- module_id: an integer reference to the parent **slide_module**
- content: text to be displayed to the viewer

# How to write queries with sqlite3

Here are some examples of how to use the sqlite3 node library. More API calls can be found at https://github.com/TryGhost/node-sqlite3 

## Example: running a statement multiple times

See https://github.com/TryGhost/node-sqlite3/wiki/API#statement for more about using statements
```javascript
const stmt = db.prepare('INSERT INTO person(name, age) VALUES (?, ?)')

people.forEach((person) => {
    stmt.run([person.name, person.age]) // can also use `stmt.get(...)`, `stmt.all(...)`, etc.
})
stmt.finalize()
```

## Example: retrieving a single row
```javascript
db.get('SELECT * FROM person WHERE name = ?', ['John'], (err, person) => {
    console.log(person)
})
```

## Example: retrieving all rows
```javascript
db.all('SELECT * FROM person', (err, rows) => {
    rows.forEach(r => {
        // do something
    })
})
```

## Example: sequence db calls
More about serialize/parallelize at https://github.com/tryghost/node-sqlite3/wiki/Control-Flow

In this example we are setting the commands to run one after the other, otherwise they are run
in parallel
```javascript
db.serialize(() => {
    db.run('UPDATE person SET to_delete = 1 WHERE name = ?', ['Biff'])
    db.run('DELETE FROM person WHERE to_delete = 1')
})
```

# Tasks

Currently there are not many routes in this application. There is one that demonstrates a working connection to the postgres database. So, let's add some.

## Task 1: Retrieving a module
A route should be created that responds with a JSON object. The only parameter it should accept is an `id` for the **slide_module** to be returned. If an `id` is not passed, it should return all the **slide_modules**.

The JSON object should contain the properties of the **slide_module** and a list of its **slides** with their properties.

The response should look something like this:
```json
{
    "id": 2,
    "name": "module name",
    "transition_delay_secs": 5,
    "slides": [
        {
            "id": 4,
            "content": "Slide 1"
        },
        {
            "id": 11,
            "content": "Slide 2"
        }
    ]
}
```

## Task 2: Creating a module
This route should accept a JSON payload that contains the necessary properties of a **slide_module** and also a list of its **slides** and their properties. As a side-effect, this route should create the **slide_module** and **slides** in the database.

Assume the payload will look something like this:
```json
{
    "name": "module name",
    "transition_delay_secs": 5,
    "slides": [
        {
            "content": "Slide 1"
        },
        {
            "content": "Slide 2"
        }
    ]
}
```
