# es6-cheatsheet

A cheatsheet containing ES2015 [ES6] tips, tricks, best practices and code
snippet examples for your day to day workflow. Contributions are welcome!

## Table of Contents

- [var versus let / const](#var-versus-let--const)
- [Replacing IIFEs with Blocks](#replacing-iifes-with-blocks)
- [Arrow Functions](#arrow-functions)
- [Strings](#strings)
- [Destructuring](#destructuring)
- [Modules](#modules)
- [Parameters](#parameters)
- [Classes](#classes)
- [Symbols](#symbols)
- [Maps](#maps)
- [WeakMaps](#weakmaps)
- [Promises](#promises)
- [Generators](#generators)
- [Async Await](#async-await)
- [Getter/Setter functions](#getter-and-setter-functions)
- [License](#license)

## var versus let / const

> Besides `var`, we now have access to two new identifiers for storing values
—`let` and `const`. Unlike `var`, `let` and `const` statements are not hoisted
to the top of their enclosing scope.

An example of using `var`:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

However, observe what happens when we replace `var` using `let`:

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

This change in behavior highlights that we need to be careful when refactoring
legacy code which uses `var`. Blindly replacing instances of `var` with `let`
may lead to unexpected behavior.

> **Note**: `let` and `const` are block scoped. Therefore, referencing
block-scoped identifiers before they are defined will produce
a `ReferenceError`.

```javascript
console.log(x); // ReferenceError: x is not defined

let x = 'hi';
```

> **Best Practice**: Leave `var` declarations inside of legacy code to denote
that it needs to be carefully refactored. When working on a new codebase, use
`let` for variables that will change their value over time, and `const` for
variables which cannot be reassigned.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Replacing IIFEs with Blocks

> A common use of **Immediately Invoked Function Expressions** is to enclose
values within its scope. In ES6, we now have the ability to create block-based
scopes and therefore are not limited purely to function-based scope.

```javascript
(function () {
    var food = 'Meow Mix';
}());

console.log(food); // Reference Error
```

Using ES6 Blocks:

```javascript
{
    let food = 'Meow Mix';
};

console.log(food); // Reference Error
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Arrow Functions

Often times we have nested functions in which we would like to preserve the
context of `this` from its lexical scope. An example is shown below:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined
    });
};
```

One common solution to this problem is to store the context of `this` using
a variable:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // Store the context of this
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

We can also pass in the proper context of `this`:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this);
};
```

As well as bind the context:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this));
};
```

Using **Arrow Functions**, the lexical value of `this` isn't shadowed and we
can re-write the above as shown:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **Best Practice**: Use **Arrow Functions** whenever you need to preserve the
lexical value of `this`.

Arrow Functions are also more concise when used in function expressions which
simply return a value:

```javascript
var squares = arr.map(function (x) { return x * x }); // Function Expression
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **Best Practice**: Use **Arrow Functions** in place of function expressions
when possible.

### ES5 => ES6 Examples

**Basic Syntax with Multiple Parameters**
(param1, param2, paramN) => expression

```
// ES5
var multiplyES5 = function(x, y) {
  return x * y;
};

// ES6
const multiplyES6 = (x, y) => { return x * y };

// Curly brackets aren’t required if only one expression is present
const multiplyES6 = (x, y) => x * y;
```

**Basic Syntax with One Parameter**

```
//ES5
var phraseSplitterEs5 = function phraseSplitter(phrase) {
  return phrase.split(' ');
};

//ES6
const phraseSplitterEs6 = phrase => phrase.split(" ");

console.log(phraseSplitterEs6("ES6 Awesomeness"));  // ["ES6", "Awesomeness"]
```

**No Parameters**
Parentheses are required when no parameters are present.

```
//ES5
var docLogEs5 = function docLog() {
    console.log(document);
};

//ES6
var docLogEs6 = () => { console.log(document); };
docLogEs6(); // #document... <html> ….
```

**Object Literal Syntax**
Arrow functions, like function expressions, can be used to return an object literal expression. The only caveat is that the body needs to be wrapped in parentheses, in order to distinguish between a block and an object (both of which use curly brackets).

```
//ES5
var setNameIdsEs5 = function setNameIds(id, name) {
  return {
    id: id,
    name: name
  };
};

// ES6
var setNameIdsEs6 = (id, name) => ({ id: id, name: name });
console.log(setNameIdsEs6 (4, "Kyle"));   // Object {id: 4, name: "Kyle"}
```

### Use Cases for Arrow Functions

**Example #01**

```
const smartPhones = [
  { name:'iphone', price:649 },
  { name:'Galaxy S6', price:576 },
  { name:'Galaxy Note 5', price:489 }
];

// ES5
var prices = smartPhones.map(function(smartPhone) {
  return smartPhone.price;
});

console.log(prices); // [649, 576, 489]

// ES6
const prices = smartPhones.map(smartPhone => smartPhone.price);
console.log(prices); // [649, 576, 489]
```

**Example #02**

```
const array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];

// ES5
var divisibleByThrreeES5 = array.filter(function (v){
  return v % 3 === 0;
});

// ES6
const divisibleByThrreeES6 = array.filter(v => v % 3 === 0);

console.log(divisibleByThrreeES6); // [3, 6, 9, 12, 15]
```

###Promises and Callbacks

```
// ES5
aAsync().then(function() {
  returnbAsync();
}).then(function() {
  returncAsync();
}).done(function() {
  finish();
});

// ES6
aAsync().then(() => bAsync()).then(() => cAsync()).done(() => finish);
```

**promise**

```
// ES5
API.prototype.get = function(resource) {
  var self = this;
  return new Promise(function(resolve, reject) {
    http.get(self.uri + resource, function(data) {
      resolve(data);
    });
  });
};

// ES6
API.prototype.get = function(resource) {
  return new Promise((resolve, reject) => {
    http.get(this.uri + resource, function(data) {
      resolve(data);
    });
  });
};
```
<sup>[(back to table of contents)](#table-of-contents)</sup>

## Strings

With ES6, the standard library has grown immensely. Along with these changes
are new methods which can be used on strings, such as `.includes()` and
`.repeat()`.

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

Instead of checking for a return value `> -1` to denote string containment,
we can simply use `.includes()` which will return a boolean:

```javascript
const string = 'food';
const substring = 'foo';

console.log(string.includes(substring)); // true
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```

In ES6, we now have access to a terser implementation:

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Template Literals

Using **Template Literals**, we can now construct strings that have special
characters in them without needing to escape them explicitly.

```javascript
var text = "This string contains \"double quotes\" which are escaped.";
```

```javascript
let text = `This string contains "double quotes" which don't need to be escaped anymore.`;
```

**Template Literals** also support interpolation, which makes the task of
concatenating strings and values:

```javascript
var name = 'Tiger';
var age = 13;

console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

Much simpler:

```javascript
const name = 'Tiger';
const age = 13;

console.log(`My cat is named ${name} and is ${age} years old.`);
```

In ES5, we handled new lines as follows:

```javascript
var text = (
    'cat\n' +
    'dog\n' +
    'nickelodeon'
);
```

Or:

```javascript
var text = [
    'cat',
    'dog',
    'nickelodeon'
].join('\n');
```

**Template Literals** will preserve new lines for us without having to
explicitly place them in:

```javascript
let text = ( `cat
dog
nickelodeon`
);
```

**Template Literals** can accept expressions, as well:

```javascript
let today = new Date();
let text = `The time and date is ${today.toLocaleString()}`;
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Destructuring (Destructuring Assignment)

Destructuring allows us to extract values from arrays and objects (even deeply
nested) and store them in variables with a more convenient syntax.

### Destructuring Arrays
**ES5**

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

**ES6**

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

**ES6 destructuring permits a simpler and less error-prone alternative:**

```javascript
const [one, two, three] = arr;

// one = '1', two = '2', three = '3'
```

**You can ignore certain values, e.g.**

```javascript
const [one, , three] = arr;

// one = 1, three = 3
```

**or use the rest operator (...) to extract remaining elements:**

```javascript
const [one, ...two] = arr;

// one = 1, two = [2, 3]
```

### Destructuring Objects

**ES5**

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

**ES6**

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

**More complex nested objects can also be referenced, e.g.**

```
const meta = {
  title: 'Destructuring Assignment',
  authors: [
    {
      firstname: 'Craig',
      lastname: 'Buckler'
    }
  ],
  publisher: {
    name: 'SitePoint',
    url: 'http://www.sitepoint.com/'
  }
};

const {
    title: doc,
    authors: [{ firstname: name }],
    publisher: { url: web }
  } = meta;

/*
  doc   = 'Destructuring Assignment'
  name  = 'Craig'
  web   = 'http://www.sitepoint.com/'
*/
```

This appears a little complicated but remember that in all destructuring assignments:

* the left-hand side of the assignment is the **destructuring target** — the pattern which defines the variables being assigned
* the right-hand side of the assignment is the **destructuring source — the array or object which holds the data being extracted.

There are a number of other caveats. First, you can’t start a statement with a curly brace, because it looks like a code block, e.g.

```
// THIS FAILS
{ a, b, c } = myObject;
``

You must either declare the variables, e.g.

// THIS WORKS
const { a, b, c } = myObject;

// or use parentheses if variables are already declared, e.g.
// THIS WORKS
({ a, b, c } = myObject);
```

*You should also be wary of mixing declared and undeclared variables, e.g.*

```
// THIS FAILS
let a;
let { a, b, c } = myObject;

// THIS WORKS
let a, b, c;
({ a, b, c } = myObject);
```

####Use cases for deconstructing
#####Easier Declaration
Variables can be declared without explicitly defining each value, e.g.
```
// ES5
var a = 'one', b = 'two', c = 'three';

// ES6
const [a, b, c] = ['one', 'two', 'three'];
```

Admittedly, the destructured version is longer. It’s a little easier to read, although that may not be the case with more items.

#####Variable Value Swapping
Swapping values in ES5 requires a temporary third variable, but it’s far simpler with destructuring:

```
var a = 1, b = 2;

// ES5 swap
var temp = a;
a = b;
b = temp;

// a = 2, b = 1

// ES6 swap back
[a, b] = [b, a];

// a = 1, b = 2
```
You’re not limited to two variables; any number of items can be rearranged, e.g.

```
// rotate left
[b, c, d, e, a] = [a, b, c, d, e];
```
#####Default Function Parameters
Presume we had a function to output our `meta` object:

```
var meta = {
  title: 'Destructuring Assignment',
  authors: [
    {
      firstname: 'Craig',
      lastname: 'Buckler'
    }
  ],
  publisher: {
    name: 'SitePoint',
    url: 'http://www.sitepoint.com/'
  }
};

prettyPrint(meta);
```
In ES5, it’s necessary to parse this object to ensure appropriate defaults are available, e.g.
```
// ES5 default values
function prettyPrint(param) {
  param = param || {};
  var
    pubTitle = param.title || 'No title',
    pubName = (param.publisher &amp;&amp; param.publisher.name) || 'No publisher';

  return pubTitle + ', ' + pubName;
}
```

In ES6 we can assign a default value to any parameter, e.g.

```
// ES6 default value
function prettyPrint(param = {}) {
```

but we can then use destructuring to extract values and assign defaults where necessary:

```
// ES6 destructured default value
function prettyPrint(
  {
    title: pubTitle = 'No title',
    publisher: { name: pubName = 'No publisher' }
  } = {}
) {
  return pubTitle + ', ' + pubName;
}
```

#####Returning Multiple Values from a Function
Functions can only return one value, but that can be a complex object or multi-dimensional array. Destructuring assignment makes this more practical, e.g.

```
function f() {
  return [1, 2, 3];
}

const [a, b, c] = f();

// a = 1, b = 2, c = 3
```

#####For-of Iteration
Consider an array of book information:

```
const books = [
  {
    title: 'Full Stack JavaScript',
    author: 'Colin Ihrig and Adam Bretz',
    url: 'http://www.sitepoint.com/store/full-stack-javascript-development-mean/'
  },
  {
    title: 'JavaScript: Novice to Ninja',
    author: 'Darren Jones',
    url: 'http://www.sitepoint.com/store/leaern-javascript-novice-to-ninja/'
  },
  {
    title: 'Jump Start CSS',
    author: 'Louis Lazaris',
    url: 'http://www.sitepoint.com/store/jump-start-css/'
  },
];
```

The ES6 for-of is similar to for-in, except that it extracts each value rather than the index/key, e.g.

```
for (const b of books) {
  console.log(b.title + ' by ' + b.author + ': ' + b.url);
}
```
Destructuring assignment provides further enhancements, e.g.

```
for (const {title, author, url} of books) {
  console.log(title + ' by ' + author + ': ' + url);
}
```

#####Regular Expression Handling
Regular expressions functions such as match return an array of matched items, which can form the source of a destructuring assignment:

```
const [a, b, c, d] = 'one two three'.match(/\w+/g);

// a = 'one', b = 'two', c = 'three', d = undefined
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Modules

Prior to ES6, we used libraries such as [Browserify](http://browserify.org/)
to create modules on the client-side, and [require](https://nodejs.org/api/modules.html#modules_module_require_id)
in **Node.js**. With ES6, we can now directly use modules of all types
(AMD and CommonJS).

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### Exporting in ES6

With ES6, we have various flavors of exporting. We can perform
**Named Exports**:

```javascript
export let name = 'David';
export let age  = 25;​​
```

As well as **exporting a list** of objects:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

We can also export functions, objects and values (etc.) simply by using the `export` keyword:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

And lastly, we can **export default bindings**:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;

/* Which is the same as
 * export { api as default };
 */
```

> **Best Practices**: Always use the `export default` method at **the end** of
the module. It makes it clear what is being exported, and saves time by having
to figure out what name a value was exported as. More so, the common practice
in CommonJS modules is to export a single value or object. By sticking to this
paradigm, we make our code easily readable and allow ourselves to interpolate
between CommonJS and ES6 modules.

### Importing in ES6

ES6 provides us with various flavors of importing. We can import an entire file:

```javascript
import 'underscore';
```

> It is important to note that simply **importing an entire file will execute
all code at the top level of that file**.

Similar to Python, we have named imports:

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

We can also rename the named imports:

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

In addition, we can **import all the things** (also called namespace import):

```javascript
import * as util from 'math/addition';
```

Lastly, we can import a list of values from a module:

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```
Importing from the default binding like this:

```javascript
import api from 'math/addition';
// Same as: import { default as api } from 'math/addition';
```

While it is better to keep the exports simple, but we can sometimes mix default import and mixed import if needed.
When we are exporting like this:

```javascript
// foos.js
export { foo as default, foo1, foo2 };
```

We can import them like the following:

```javascript
import foo, { foo1, foo2 } from 'foos';
```

When importing a module exported using commonjs syntax (such as React) we can do:

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

This can also be simplified further, using:

```javascript
import React, { Component, PropTypes } from 'react';
```

> **Note**: Values that are exported are **bindings**, not references.
Therefore, changing the binding of a variable in one module will affect the
value within the exported module. Avoid changing the public interface of these
exported values.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Parameters

In ES5, we had varying ways to handle functions which needed **default values**,
**indefinite arguments**, and **named parameters**. With ES6, we can accomplish
all of this and more using more concise syntax.

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

In ES6, we can simply supply default values for parameters in a function:

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

####Real-World example

**ES5**
```
// Without default parameters it looks quite bloated and unnecessary large.
function createElement (tag, config) {
  tag = tag || 'div';
  config = config || {};

  const element = document.createElement(tag);
  const content = config.content || 'Very default';
  const text = document.createTextNode(content);
  let classNames = config.classNames;

  if (classNames === undefined) {
    classNames = ['module-text', 'default'];
  }

  element.classList.add(...classNames);
  element.appendChild(text);

  return element;
}
```

**ES6**

```
// Default all the things
function createElement (tag = 'div', {
  content = 'Very default',
  classNames = ['module-text', 'special']
} = {}) {
  const element = document.createElement(tag);
  const text = document.createTextNode(content);

  element.classList.add(...classNames);
  element.appendChild(text);

  return element;
}
```

**Explain part of ES6**

```
// What exactly happens here?
function createElement ({
  content = 'Very default',
  classNames = ['module-text', 'special']
} = {}) {
  // function body
}
```
We not only declare a default object parameter, but also default object properties. This makes it more obvious what the default configuration is supposed to look like, rather than only declaring a default object (e.g. config = {}) and later setting default properties. It might take some additional time to get used to it, but in the end it improves your workflow.

Of course, we could still argue with larger configurations that it might create more overhead and it’d be simpler to just keep the default handling inside of the function body.

####ES6 Property Shorthands
If a method accepts large configuration objects as an argument, your code can become quite large. It’s common to prepare some variables and add them to said object. Property shorthands are syntactic sugar to make this step shorter and more readable:

```
const a = 'foo', b = 42, c = function () {};

// Previously we would use these constants like this.
const alphabet = {
  a: a,
  b: b,
  c: c
};

// But with the new shorthand we can actually do this now,
// which is equivalent to the above.
const alphabet = { a, b, c };
```

####Shorten Your API
Okay, back to another, more common example. The following function takes some data, mutates it and calls another method:

```
function updateSomething (data = {}) {
  const target = data.target;
  const veryLongProperty = data.veryLongProperty;
  let willChange = data.willChange;

  if (willChange === 'unwantedValue') {
    willChange = 'wayBetter';
  }

  // Do more.

  useDataSomewhereElse({
    target: target,
    property: veryLongProperty,
    willChange: willChange,
    // .. more
  });
}
```

It often happens that we name variables and object property names the same. Using the property shorthand, combined with destructuring, we actually can shorten our code quite a bit:

```
function updateSomething (data = {}) {
  // Here we use destructuring to store the constants from the data object.
  const { target, veryLongProperty: property } = data;
  let { willChange } = data;

  if (willChange === 'unwantedValue') {
    willChange = 'wayBetter';
  }

  // Do more.

  useDataSomewhereElse({ target, property, willChange });
}
```

Again, this might take a while to get used to. In the end, it’s one of those new features in JavaScript which helped me write code faster and work with cleaner function bodies.

But wait, there’s more! Property shorthands can also be applied to method definitions inside an object:

```
// Instead of writing the function keyword everytime,
const module = {
  foo: 42,
  bar: function (value) {
    // do something
  }
};

// we can just omit it and have shorter declarations
const module = {
  foo: 42,
  bar (value) {
    // do something
  }
};
```


### Rest Parameters

In ES5, we handled an indefinite number of arguments like so:

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

Using the **rest** operator, we can pass in an indefinite amount of arguments:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### Named Parameters

One of the patterns in ES5 to handle named parameters was to use the **options
object** pattern, adopted from jQuery.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

We can achieve the same functionality using destructuring as a formal parameter
to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // Use variables height, width, lineStroke here
    }
```

If we want to make the entire value optional, we can do so by destructuring an
empty object:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### Spread Operator

In ES5, we could find the max of values in an array by using the `apply` method on `Math.max` like this:
```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

In ES6, we can now use the spread operator to pass an array of values to be used as
parameters to a function:

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

We can concat array literals easily with this intuitive syntax:

```javascript
let cities = ['San Francisco', 'Los Angeles'];
let places = ['Miami', ...cities, 'Chicago']; // ['Miami', 'San Francisco', 'Los Angeles', 'Chicago']
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Classes

Prior to ES6, we implemented Classes by creating a constructor function and
adding properties by extending the prototype:

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

And created extended classes by the following:

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    Person.prototype.incrementAge.call(this);
    this.age += 20;
    console.log(this.age);
};
```

ES6 provides much needed syntactic sugar for doing this under the hood. We can
create Classes directly:

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

And extend them using the `extends` keyword:

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
        super(name, age, gender);
        this.occupation = occupation;
        this.hobby = hobby;
    }

    incrementAge() {
        super.incrementAge();
        this.age += 20;
        console.log(this.age);
    }
}
```

> **Best Practice**: While the syntax for creating classes in ES6 obscures how
implementation and prototypes work under the hood, it is a good feature for
beginners and allows us to write cleaner code.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Symbols

Symbols have existed prior to ES6, but now we have a public interface to using
them directly. Symbols are immutable and unique and can be used as keys in any hash.

### Symbol( )

Calling `Symbol()` or `Symbol(description)` will create a unique symbol that cannot be looked up
globally. A Use case for `Symbol()` is to patch objects or namespaces from third parties with your own
logic, but be confident that you won't collide with updates to that library. For example,
if you wanted to add a method `refreshComponent` to the `React.Component` class, and be certain that
you didn't trample a method they add in a later update:

```javascript
const refreshComponent = Symbol();

React.Component.prototype[refreshComponent] = () => {
    // do something
}
```


### Symbol.for(key)

`Symbol.for(key)` will create a Symbol that is still immutable and unique, but can be looked up globally.
Two identical calls to `Symbol.for(key)` will return the same Symbol instance. NOTE: This is not true for
`Symbol(description)`:

```javascript
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```

A common use case for Symbols, and in particular with `Symbol.for(key)` is for interoperability. This can be
achieved by having your code look for a Symbol member on object arguments from third parties that contain some
known interface. For example:

```javascript
function reader(obj) {
    const specialRead = Symbol.for('specialRead');
    if (obj[specialRead]) {
        const reader = obj[specialRead]();
        // do something with reader
    } else {
        throw new TypeError('object cannot be read');
    }
}
```

And then in another library:

```javascript
const specialRead = Symbol.for('specialRead');

class SomeReadableType {
    [specialRead]() {
        const reader = createSomeReaderFrom(this);
        return reader;
    }
}
```

> A notable example of Symbol use for interoperability is `Symbol.iterator` which exists on all iterable
types in ES6: Arrays, strings, generators, etc. When called as a method it returns an object with an Iterator
interface.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Maps

**Maps** is a much needed data structure in JavaScript. Prior to ES6, we created
**hash** maps through objects:

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

However, this does not protect us from accidentally overriding functions with
specific property names:

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

Actual **Maps** allow us to `set`, `get` and `search` for values (and much more).

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

The most amazing part of Maps is that we are no longer limited to just using
strings. We can now use any type as a key, and it will not be type-cast to
a string.

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

> **Note**: Using non-primitive values such as functions or objects won't work
when testing equality using methods such as `map.get()`. As such, stick to
primitive values such as Strings, Booleans and Numbers.

We can also iterate over maps using `.entries()`:

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## WeakMaps

In order to store private data versions < ES6, we had various ways of doing this.
One such method was using naming conventions:

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
        this._age += 1;
    }
}
```

But naming conventions can cause confusion in a codebase and are not always
going to be upheld. Instead, we can use WeakMaps to store our values:

```javascript
let _age = new WeakMap();
class Person {
    constructor(age) {
        _age.set(this, age);
    }

    incrementAge() {
        let age = _age.get(this) + 1;
        _age.set(this, age);
        if (age > 50) {
            console.log('Midlife crisis');
        }
    }
}
```

The cool thing about using WeakMaps to store our private data is that their
keys do not give away the property names, which can be seen by using
`Reflect.ownKeys()`:

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

A more practical example of using WeakMaps is to store data which is associated
to a DOM element without having to pollute the DOM itself:

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// Store a weak reference to the element with a key
map.set(el, 'reference');

// Access the value of the element
let value = map.get(el); // 'reference'

// Remove the reference
el.parentNode.removeChild(el);
el = null;

// map is empty, since the element is destroyed
```

As shown above, once the object is destroyed by the garbage collector,
the WeakMap will automatically remove the key-value pair which was identified
by that object.

> **Note**: To further illustrate the usefulness of this example, consider how
jQuery stores a cache of objects corresponding to DOM elements which have
references. Using WeakMaps, jQuery can automatically free up any memory that
was associated with a particular DOM element once it has been removed from the
document. In general, WeakMaps are very useful for any library that wraps DOM
elements.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Promises

Promises allow us to turn our horizontal code (callback hell):

```javascript
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

Into vertical code:

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

Prior to ES6, we used [bluebird](https://github.com/petkaantonov/bluebird) or
[Q](https://github.com/kriskowal/q). Now we have Promises natively:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

Where we have two handlers, **resolve** (a function called when the Promise is
**fulfilled**) and **reject** (a function called when the Promise is **rejected**).

> **Benefits of Promises**: Error Handling using a bunch of nested callbacks
can get chaotic. Using Promises, we have a clear path to bubbling errors up
and handling them appropriately. Moreover, the value of a Promise after it has
been resolved/rejected is immutable - it will never change.

Here is a practical example of using Promises:

```javascript
var request = require('request');

return new Promise((resolve, reject) => {
  request.get(url, (error, response, body) => {
    if (body) {
        resolve(JSON.parse(body));
      } else {
        resolve({});
      }
  });
});
```

We can also **parallelize** Promises to handle an array of asynchronous
operations by using `Promise.all()`:

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Generators

Similar to how [Promises](https://github.com/DrkSephy/es6-cheatsheet#promises) allow us to avoid
[callback hell](http://callbackhell.com/), Generators allow us to flatten our code - giving our
asynchronous code a synchronous feel. Generators are essentially functions which we can
[pause their execution](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)
and subsequently return the value of an expression.

A simple example of using generators is shown below:

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
> console.log(generator.next()); // { value: 1, done: false }
> console.log(generator.next()); // { value: 2, done: false }
> console.log(generator.next()); // { value: 3, done: false }
> console.log(generator.next()); // { value: 4, done: false }
```

Where [next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
will allow us to push our generator forward and evaluate a new expression. While the above example is extremely
contrived, we can utilize Generators to write asynchronous code in a synchronous manner:

```javascript
// Hiding asynchronousity with Generators

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

And here we write a generator function that will return our data:

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

By the power of `yield`, we are guaranteed that `entry1` will have the data needed to be parsed and stored
in `data1`.

While generators allow us to write asynchronous code in a synchronous manner, there is no clear
and easy path for error propagation. As such, as we can augment our generator with Promises:

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

And we write a function which will step through our generator using `next` which in turn will utilize our
`request` method above to yield a Promise:

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    (function iterate(val) {
        var ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

By augmenting our Generator with Promises, we have a clear way of propagating errors through the use of our
Promise `.catch` and `reject`. To use our newly augmented Generator, it is as simple as before:

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

We were able to reuse our implementation to use our Generator as before, which shows their power. While Generators
and Promises allow us to write asynchronous code in a synchronous manner while retaining the ability to propagate
errors in a nice way, we can actually begin to utilize a simpler construction that provides the same benefits:
[async-await](https://github.com/DrkSephy/es6-cheatsheet#async-await).

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Async Await

While this is actually an upcoming ES2016 feature, `async await` allows us to perform the same thing we accomplished
using Generators and Promises with less effort:

```javascript
var request = require('request');

function getJSON(url) {
  return new Promise(function(resolve, reject) {
    request(url, function(error, response, body) {
      resolve(body);
    });
  });
}

async function main() {
  var data = await getJSON();
  console.log(data); // NOT undefined!
}

main();
```

Under the hood, it performs similarly to Generators. I highly recommend using them over Generators + Promises. A great resource
for getting up and running with ES7 and Babel can be found [here](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html).

<sup>[(back to table of contents)](#table-of-contents)</sup>
## Getter and setter functions

ES6 has started supporting getter and setter functions within classes. Using the following example:

```javascript
class Employee {

    constructor(name) {
        this._name = name;
    }

    get name() {
      if(this._name) {
        return 'Mr. ' + this._name.toUpperCase();
      } else {
        return undefined;
      }
    }

    set name(newName) {
      if (newName == this._name) {
        console.log('I already have this name.');
      } else if (newName) {
        this._name = newName;
      } else {
        return false;
      }
    }
}

var emp = new Employee("James Bond");

// uses the get method in the background
if (emp.name) {
  console.log(emp.name);  // Mr. JAMES BOND
}

// uses the setter in the background
emp.name = "Bond 007";
console.log(emp.name);  // Mr. BOND 007
```

Latest browsers are also supporting getter/setter functions in Objects and we can use them for computed properties, adding listeners and preprocessing before setting/getting:

```javascript
var person = {
  firstName: 'James',
  lastName: 'Bond',
  get fullName() {
      console.log('Getting FullName');
      return this.firstName + ' ' + this.lastName;
  },
  set fullName (name) {
      console.log('Setting FullName');
      var words = name.toString().split(' ');
      this.firstName = words[0] || '';
      this.lastName = words[1] || '';
  }
}

person.fullName; // James Bond
person.fullName = 'Bond 007';
person.fullName; // Bond 007
```
<sup>[(back to table of contents)](#table-of-contents)</sup>

## License

The MIT License (MIT)

Copyright (c) 2015 David Leonard

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

<sup>[(back to table of contents)](#table-of-contents)</sup>