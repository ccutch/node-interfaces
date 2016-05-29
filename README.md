# Node Interfaces
This is a library written in node with the purpose of providing utilities to check a class's methods against an interface class. Interfaces are useful for designing applications, working with teams in large code bases and getting expected results from your code. There are two methods of implementing an interface implicit (ie: golang) or explicit (ie: java). This library is going to attempt to fulfil both of these implementations as we are not limited by the language itself. This library is being made with consideration for future features of node that are only supported with transformers like babel for right now.


## Definition of an interface
An interface, as applies to this library, is a class with method that can serve as a model for other implementation classes to follow. This means that this library will check both static and prototype: methods, getters, and setters. This will not check for properties and does not check for typing. If you would like the use typing and properties a precompiled language such as typescript would be recommended at this time. Also a constructor can not be defined as a required method. The following example may be confusing but is here to show all methods that would be checked.

```javascript
// node6 and es2016 example
import {Interface} from 'node-interfaces'
// or const {Interface} = require('node-interfaces')

class ExampleInterface extends Interface {

  static get name () {
    return `
      Get the name {String} of this example.
    `
  }

  static set name (value) {
    return `
      Set the name {String} of this example to a new {String} value
    `
  }

  static displayName () {
    return `
      Display name of this example as 'Example: this.name' or how ever you see fit.
    `
  }

  get next () {
    return `
      Get next example.
      @return {ExampleInterface}
    `
  }

  set next (other) {
    return `
      Set next example after this one.
      @param {ExampleInterface} other
    `
  }

  displayNext () {
    return `
      Display the next interface with its static display name function.
    `
  }
}
```



## Creating an interface
Provided with this library is an Interface class. the purpose of this class is to make sure that the class extending the Interface class is not instantiated. The methods of the interface should return a string that will be displayed for the user when the method is missing or does not have the correct parameters. In the example below a jsdoc string was returned.

```javascript
// node6 example
const {Interface} = require('node-interfaces')

exports.ModelInterface = class ModelInterface extends Interface {
  save (force) {
    return `
      Save model to database.
      @param {Boolean} force
      @return {Promise}
    `
  }
}

// es2016 example
import {Interface} from 'node-interfaces'

export class ModelInterface extends Interface {
  async save (force) {
    return `
      Save model to database.
      @param {Boolean} force
      @return {Promise}
    `
  }
}
```

## Explicit implementation
Once you have made an interface class you can explicitly implement that interface on another class that you are creating much like the `implements` keywork in java. This will check that the class fulfils the interface on starting your application. Node interfaces provides a function `implements` that can be used as a class decorator (which is prefered) or alone.

```javascript
// node6 example
const {implements} = require('node-interfaces')
const {ModelInterface} = require('./model_interface')

exports.Model = class Model {
  save (force) {
    return new Promise((resolve, reject) => {
      // ...
    })
  }
}

implements(ModelInterface)(Model)

// es2016 example
import {implements} from 'node-interfaces'
import {ModelInterface} from './model_interface'

@implements(ModelInterface)
export class Model {
  async save (force) {
    // ...
  }
}

```

## Implicit implementation
When you have created an interface but would like to check that it matches the interface when the a function is called you can use the `hasImplemented` function provided. This will check the methods of the class at runtime. The use of this function in node6 and es2016 is almost identical which is why this would probably be the prefered way of using interfaces in node6, though comes with the caveat of only checking the class against the interface when the function is called.


```javascript
// node6 example
const {hasImplemented} = require('node-interfaces')
const {ModelInterface} = require('./model_interface')

class Person {
  save (force) {
    // ...
  }
}

exports.PersonCreator = class PersonCreator {
  create ({name, age}) {
    hasImplemented(Person, ModelInterface)
    const p = new Person(name, age)
    return p.save()
  }
}

hasImplemented(ModelInterface)(Model)

// es2016 example
import {hasImplemented} from 'node-interfaces'
import {ModelInterface} from './model_interface'

class Person {
  async save (force) {
    // ...
  }
}

export class PersonCreator {
  create ({name, age}) {
    hasImplemented(Person, ModelInterface)
    const p = new Person(name, age)
    return p.save()
  }
}
```