
# Javascript-OOP

Fundamentals and Example of Javascript with Object Oriented Programming concept

## Objects

The simplest way to create an object is using an object literal

```javascript
const circle = {
  radius: 1,
  draw: function() {}
};
```

To create multiple objects with the same structure and behaviuor (methods), use a factory or a constructor.

```javascript
// Factory function
function createCircle(radius) {
  return {
    radius,
    draw: function() {}
  };
}

// Constructor function
function Circle(radius) {
  this.radius = radius;
  this.draw = function() {};
}
```

Every object has a "constructor" property which returns the function that was used to construct or create that object.

```javascript
const x = {};
x.constructor; // returns Object()
```

In JavaScript, functions are objects. They have properties and methods.

```javascript
Circle.name;
Circle.length;
Circle.constructor; // returns Function()
Circle.call({}, 1); // to call the Circle function
Circle.apply({}, [1]);
```

- Value types are copied by their value, reference types are copied by their reference.
- Value types in JavaScript are: String, Number, Boolean, Symbol, undefined and null
- Reference types are: Object, Function and Array

JavaScript objects are dynamic. You can add/remove properties:

```javascript
circle.location = {};
circle["location"] = {};

delete circle.location;
```

To enumerate the members in an object:

```javascript
for (let key in circle) console.log(key, circle[key]);

Object.keys(circle);
```

To see if an object has a given property

```javascript
if ('location' in circle)
```

Abstraction means hiding the complexity/details and showing only the essentials. We can hide the details by using private members. Replace "this" with "let".

```javascript
function Circle(radius) {
  // Public member
  this.radius = radius;

  // Private member
  let defaultLocation = {};
}
```

To define a getter/setter, use Object.defineProperty()

```javascript
Object.defineProperty(this, "defaultLocation", {
  get: function() {
    return defaultLocation;
  },
  set: function(value) {
    defaultLocation = value;
  }
});
```

## Prototypes

Every object (except the root object) has a prototype (parent).

```javascript
// To get the prototype of an object:
Object.getPrototypeOf(obj);
```

In Chrome, you can inspect "**proto**" property. But you should not use that in the code.

```javascript
// To get the attributes of a property:
Object.getOwnPropertyDescriptor(obj, "propertyName");

// To set the attributes for a property:
Object.defineProperty(obj, "propertyName", {
  configurable: false, // cannot be deleted
  writable: false,
  enumerable: false
});
```

Constructors have a "prototype" property. It returns the object that will be used as the prototype for objects created by the constructor.

```javascript
Object.prototype === Object.getPrototypeOf({});
Array.prototype === Object.getPrototypeOf([]);
```

All objects created with the same constructor will have the same prototype. A single instance of this prototype will be stored in the memory.

```javascript
const x = {};
const y = {};
Object.getPrototypeOf(x) === Object.getPrototypeOf(y); // returns true
```

Any changes to the prototype will be immediately visible to all objects referencing this prototype.

When dealing with large number of objects, it's better to put their methods on their prototype. This way, a single instance of the methods will be in the memory.

```javascript
Circle.prototype.draw = function() {};
```

To get the own/instance properties:

```javascript
Object.keys(obj);
```

To get all the properties (own + prototype):

```javascript
for (let key in obj) {
}
```

## Inheritance

##### Prototypical Inheritance

```javascript
function Shape() {}
function Circle() {}

// Prototypical inheritance
Circle.prototype = Object.create(Shape.prototype);
Circle.prototype.constructor = Circle;

function Rectangle(color) {
  // To call the super constructor
  Shape.call(this, color);
}
```

##### Method overriding

```javascript
Shape.prototype.draw = function() {};
Circle.prototype.draw = function() {
  // Call the base implementation
  Shape.prototype.draw.call(this);

  // Do additional stuff here
};
```

Don't create large inheritance hierarchies. One level of inheritance is fine.

Use mixins to combine multiple objects and implement composition in JavaScript.

```javascript
const canEat = {
  eat: function() {}
};

const canWalk = {
  walk: function() {}
};

function mixin(target, ...sources) {
  // Copies all the properties from all the source objects
  // to the target object.
  Object.assign(target, ...sources);
}

function Person() {}

mixin(Person.prototype, canEat, canWalk);
```

## Polymorphism

```
function HtmlElement() {
  this.click = function() {
    console.log('clicked');
  }
}

HtmlElement.prototype.focus = function(){
  console.log('focued');
}

function HtmlSelectElement(items = []) {
  this.items = items;

  this.addItem = function(item) {
    this.items.push(item);
  }

  this.removeItem = function(item) {
    this.items.splice(this.items.indexOf(item), 1);
  }

  this.render = function() {
    return `
    <select>${this.items.map(item => `
      <option>${item}</option>`).join('')}
    </select>`;
  }
}
HtmlSelectElement.prototype = new HtmlElement();
HtmlSelectElement.prototype.constructor = HtmlSelectElement;

function HtmlImageElement(src) {
  this.src = src;

  this.render = function() {
    return `<img src="${this.src}" />`
  }
}
HtmlImageElement.prototype = new HtmlElement();
HtmlImageElement.prototype.constructor = HtmlImageElement;

const elements = [
  new HtmlSelectElement([1, 2, 3]),
  new HtmlImageElement('http://')
];

for (let element of elements)
  console.log(element.render());

```

## Classes

```javascript
class Circle {
  constructor(radius) {
    this.radius = radius;
  }

  // These methods will be added to the prototype.
  draw() {}

  // This will be available on the Circle class (Circle.parse())
  static parse(str) {}
}
```

Using symbols to implement private properties and methods

```javascript
const _size = Symbol();
const _draw = Symbol();

class Square {
  constructor(size) {
    // "Kind of" private property
    this[_size] = size;
  }

  // "Kind of" private method
  [_draw]() {}

  // By "kind of" I mean: these properties and methods are essentally
  // part of the object and are accessible from the outside. But accessing
  // them is hard and awkward.
}
```

Using WeakMaps to implement private properties and methods

```javascript
const _width = new WeakMap();

class Rectangle {
  constructor(width) {
    _width.set(this, width);
  }

  draw() {
    console.log("Rectangle with width" + _width.get(this));
  }
}
```

WeakMaps give us better protection than symbols. There is no way to access private members implemented using WeakMaps from the outside of an object.

```javascript
// Inheritance
class Triangle extends Shape {
  constructor(color) {
    // To call the base constructor
    super(color);
  }

  draw() {
    // Call the base method
    super.draw();

    // Do some other stuff here
  }
}
```

## Modules

#### Module formats

- AMD / Asynchronous Module Definition (Browser)
- CommonJS (Node)
- UMD / Universal Module Definition (Browser + Node)
- ES6 Modules

#### CommonJS (Used in Node)

```javascript
// Exporting
module.exports.Cirlce = Circle;
// Importing
const Circle = require("./circle");
```

#### ES6 Modules (Used in Browser)

```javascript
// Exporting
export class Square {}
// Importing
import { Square } from "./square";
```

We use Babel to transpile our modern JavaScript code into code that browsers can understand (typically ES5).

We use Webpack to combine our JavaScript files into a bundle.

