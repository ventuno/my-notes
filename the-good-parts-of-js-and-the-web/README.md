# My Notes On [D. Crockford](http://www.crockford.com/)'s "[The Good Parts of JavaScript and the Web](https://frontendmasters.com/courses/good-parts-javascript-web/)"

## Programming Style and Your Brain

### Two Systems
D. Kahneman (Thinking, Fast and Slow) has a metaphor for human thought based on the interaction of two systems:

* System 2 (Head): analytic, algorithmic, logical. It is sophisticated but slow. We use computers to help us with the tasks system 2 is too slow to solve.
* System 1 (Gut): intuitive, heuristic, associative. It is very fast and cannot be "turned off".

Kahneman describes the connection between these two systems. System 2 gets its working set from System 1 without being aware of it. As System 1 is approximate, it can give wrong assumptions to System 2.

Computer programs are the most complicated things people make. The complexity of writing computer programs is due to the fact that programs need to be perfect to work correctly.

We write computer programs based on the two systems. There is not fixed set of instructions that we can follow in order to write a program. Solving a problem with programming is often about intuition, there is a lot of confusion about what is the best approach.

Most of the time we solve a problem with an incomplete knowledge of what the solution is going to be. The solution is to guess, which is hard for System 2, but very natural for System 1.

### JavaScript: Good Parts & Bad Parts

There are many good and bad parts to JavaScript. [JSLint](http://www.jslint.com/) is a tool that defines a professional subset of JavaScript, warning a programmer when he uses some of the bad parts.

The argument on aligning curly brackets to the left or the right does not have an answer. In JavaScript, brackets should always be on the right:

```
return
{
    ok: false
};
// Silent error: the function returns undefined because the compiler automatically adds a semicolon after the return statement.

return {
    ok: true
};
// Works as expected: returns the object.
```

The code:

```
return
{
    ok: false
};
```

is translated at compile time to:

```
return; // automatic semicolon insertion.
// unreachable statement
{ // beginning of a block
    ok: false; // "ok" is a label. JavaScript sytax allows statements to have labels. "false" is an empty expression.
}; // end of block and empty expression.
```

### Programming Style
A good style can help produce better programs. Style should not be about personal preference and self-expression. Good programming style is about preferring forms that are error resistant. Good use of style can help reduce the occurrence of errors.

Programs must communicate clearly to people, through good style.

### Composition
Use elements of good composition where applicable.

* Use a space after a comma, not before. Unless we have evidence of the opposite.
* Use spaces to disambiguate parenthesis.
  - No space between a function name and `(`.
  - One space between all other names and `(`.
```
// Wrong:
foo (bar);
return(a+b);
if(a=== 0) {...}
function foo (b) {...}
function(x) {...}
```
* Neatness counts.
```
// Wrap an Immediately Invocable Function expression.
(function () {
    ...
}());
```
* Never rely on automatic semicolon insertion.
```
// As the semicolon is missing, the compile is not going to assume that we are assigning y to x. Instead, it is going to assume that we are calling the function "y" and assigning its result to x.
x = y
(function () {
    ...
}());
```
* Do not use the `with` statement. It is confusing.
```
// The following code can be interpreted as:
// o.foo = koda;
// o.foo = o.koda;
// foo = koda;
// foo = o.koda;
with (o) {
    foo = koda;
}
```
* Always use `===`, never `==`.
```
0 == '' // true
0 == '0' // true
'' == '0' // false
false == 'false' // false
false == '0' // true
" \t\r\n " == 0 // true
```
* If there's a feature of a language that is sometimes problematic and if it can be replaced with another feature that is more reliable, then always use the more reliable feature.
* Avoid forms that are difficult to distinguish from common errors.
```
// Do not use multiline string literals, they break indentation and generate a syntax error if we have a space after the `\`
var long_line_1 = "This is a \
long line"; // ok
// There is a space after the "\".
var long_line_2 = "This is a \ 
long line"; // syntax error
```
* Make your programs look like what they do.
```
// In JavaScript we can have assignments inside a condition.
if (a = b) {...}

// It appears to be doing
if (a === b) {...}
// It really does
a = b;
if (a) {...}
```
* Write in a way that clearly communicates your intent.
```
if (a) b(); c();
// Looks like
if (a) {b(); c();}
// But it really does
if (a) {b();} c();
```
* As our processes become more agile, our coding must be more resilient.

### Scope
In JavaScript, we do not have block scope, only function scope.

#### The `var` statement
The `var` statement gets split in two parts:

* The declaration part gets hoisted to the top of the function, initializing with `undefined`.
* The initialization part turns into an ordinary assignment.
```
// For example
function foo() {
    ...
    var myVar = 0, myOtherVar;
}
// Expands into
function foo() {
    var myVar = undefined,
        myOtherVar = undefined;
    ...
    myVar = 0;
}

// The statement
var a = b = 0;
// Looks like
var a = 0, b = 0;
// Really does
// b = 0; // b is a global variable
var a = b;
```

Based on this strange behavior of the `var` statement:

* Declare all variables at the top of the function.
* Declare all functions before you call them.

In a `for` statement, the variable `i` is not scoped to the loop.  In ES6, the `let` statement will respect block scope.

#### Global variables

* Global variables are evil.
* Avoid global variables.
* When using global variables, be explicit: use `UPPER_CASE`.
* Global variables should be as rare as hens teeth and stick out like a sore thumb.

#### `new` prefix
Forgetting a `new` causes a constructor to clobber global variables without warning (fixed in ES5/strict). Instead of creating a new object it will clobber global variables that happen to have the same name as the instance variables you are trying to initialize.

Constructor functions should be named with `InitialCaps`.

### Bad Style

#### `++`
`++` is bad because it makes you want to write one-liners, which make code hard to maintain and debug. Use `x += 1` instead. It also can be confused with `++x`. We don't really need a different syntax to add 1 to a value.

> For no cost, by adopting a more rigorous style, many classes of errors can be automatically avoided. 

Bad stylists:
* Under educated.
* Old school.
* Thrill seeker.
* Exhibitionist.

### Code Written for Performance
Performance.

* Performance specific code is usually crufty.
* Clean code is easier to reason about.
* Premature optimization is the root of all evil (Donald Knuth).
* Most of the code has a negligible impact on performance. Only optimize the code that is taking the time.
* Algorithm replacement is vastly more effective than code fiddling.

Programming is the most complicated thing that humans do. Computer programs must be perfect. Humans are not good at perfect.
Designing a programming style demands discipline. It is not selecting features because they are liked, or pretty, or familiar. It is because it helps you to reduce your error rate. The JSLint style was driven by the need to automatically detect defects. Forms that can hide defect are considered defective.

## And Then There Was JavaScript

### Objects
An object is a dynamic collection of properties. Every property as a key string that is unique within that object.

#### `get`, `set` and `delete`

* `get`
    `object.name`
    `object[expression]`
* `set`
    `object.name = value;`
    `object[expression] = value;`
* `delete`
    `delete object.name`
    `delete object[expression]`

`[keys]` must be strings (there's automatic type coercion). It would have been better if they could have been any value.

#### Object literals
An expressive notation for creating objects.
```
var my_object = {
    foo: bar,
    name: "value",
    "@#$%*": "grawlix"
};
```

#### Classes v Prototypes
Working with prototypes:

* Make an object that you like.
* Create new instances that inherit from that object.
* Customize the new objects.
* Classification and taxonomy are not necessary.

The last point is a huge win of the language. In a classical system you start off by classifying all the objects, but we are doing it at a point (the beginning of a project)in which we have the least understanding of how the system is actually going to work. Which means that we are probably going to get it wrong. Getting rid of classes is a good idea because it helps reduce coupling (cf.: Head First Design Principles). 

#### Delegation (Differential Inheritance)
Objects in JavaScript delegate other objects to do the work they are not able to do.

#### Prototypal Inheritance
Objects are created using the primitive `Object.create([prototype])`. The prototype, is the object we want to inherit from.
```
// mother (any object literal) has an implicit pointer to Object.prototype
var mother = {
    a: 1,
    b: 2
};
// daugher has an implicit pointer to mother.prototype
var daughter = Object.create(mother);
daughter.b += 2; // daughter.b === 4; because reading operation (can) go all the way down to the prototype chain. Storing operations go into the topmost object.
```
We can create objects that do not inherit from other objects by using `Object.create(null)`. Objects created with this construct will not inherit from `Object.prototype` and will look much more like a hash table.

### `Number`s
Numbers are objects in JavaScript. There is only one number type, it is based on 64-bit binary floating point. We don't have integer types.
* Associative Law does not hold: `(a + b) + c ==== a + (b + c)` produces `false` for some values of `a`, `b`, `c`. Integers under 9 quadrillion are ok.
* Decimal fractions are approximate `a + b === c` can be `false` for `a = 0.10`, `b = 0.20`, `c = 0.30`.

#### `NaN`
* Special number: Not a Number.
* Result of undefined or erroneous operations.
* Toxic: any arithmetic operation with `NaN` as an input will have `NaN` as a result.
* `NaN` is not equal to anything, including `NaN`:
  - `NaN === NaN` is `false`.
  - `NaN !== NaN` is `true`.
  - `isNaN(NaN)` is `true`.

### `String`s
* A sequence of 0 or more 16-bit Unicode characters.
* No separate character type (characters are strings of length 1).
* Strings are immutable.

### `null` and `undefined`
Values that are not objects. It is recommended to use just one of them (e.g.: `undefined` as it is the one used by the language).

### `typeof` operator
type | `typeof`
-----|--------
object | 'object'   
function | 'function' 
array | 'object'   
number | 'number'   
string | 'string'   
boolean | 'boolean'  
`null` | 'object'   
`undefined` | 'undefined'

## Function the Ultimate

### Functions
It does the same work as Methods, Classes, Constructors and Modules. It inherits from `Function.prototype`. `var` and `function` statements are hoisted at the top of the function.

#### Scope
With the `var` statement, {blocks} do not have scope. Only functions have scope.

* Declare all variables at the top of the function.
* Declare all functions before you call them.
* The language provides mechanisms that allow you to ignore this advice, but they are problematic.


### Function Best Practices

#### `return` statement
`return [expression];` or `return;`.

* If there is no expression, the return value is `undefined`.
* Except for constructors, whose default return value is `this`.

#### Don't make functions in a loop

* It can be wasteful because a new function object is created on every iteration.
* It can be confusing because the new function closes over the loop's variables, not over their current values.

```
// Wrong/unexpected behavior
for (var i ...) {
    div_id = divs[i].id;
    divs[i].onclick = function () {
        alert(div_id); // Every function will alert the same div_id (the last one).
    }
}

// Correct behavior
divs.forEach(function (div) {
    div.onclick = function () {
        alert(div.id);
    }
}
```

#### Two pseudo parameters
`arguments` and `this`, neither is recommended. 

##### `arguments`

* When a function is invoked in addition to its parameters, it also gets  a special parameter called `arguments`.
* It contains all the arguments from the invocation.
* It is an array-like object (not an array, so it does not inherit any of the array methods).
* `arguments.length` is the number of arguments passed.
* Weird interaction with parameters (if parameter1 changes arguments[0] changes, if arguments[1] changes parameter2 changes).

##### `this`

* The `this` parameter contains a reference to the object of invocation.
* `this` allows a method to know what object it is concerned with.
* `this` allows a single function object to service many objects.
* `this` is a key to prototypal inheritance.

#### Function Invocation
There are four ways to call a function:

* Function form
```
functionObject([arguments)
```
* Method form
```
thisObject.methodName([arguments])
thisObject["methodName"]([arguments])
```
* Constructor form
```
new FunctionObject([arguments])
```
* Apply form
```
functionObject.apply(thisObject, [[arguments]])
functionObject.call(thisObject, [arguments...])
```

##### Method form

* When a function is called in the method form, `this` is set to thisObject, the object containing the function.
* This allows methods to have reference to the object of interest.

##### Function form

* When a function is called in the function form, `this` is set to the global object (that is not very useful and was fixed in ES5/Strict, where `this` is `undefined`).
* An inner function does not get access to the outer `this` (in the outer function defined `var that = this;` and access `that` from the inner function).

##### Constructor form

* When a function is created with the `new` operator a new object is created and assigned to `this`.
* If there is not an explicit return value, then `this` will be returned.
* Used in Pseudoclassical style.

##### Apply form

* A function's `apply` or `call` method allows for calling the function, explicitly specifying thisObject.
* It can also take an array of parameters or a sequence of parameters.

##### `this`

* `this` is a bonus parameter. Its value depends on the calling form.
* `this` gives methods access to their objects.
* `this` is bound at invocation time.

Invocation form | `this`
----------------|-------
function | the global object or `undefined`
method | the object
constructor | the new object
apply | argument

### Closure (Lexical Scoping/Static Scoping)
Functions can nest. Functions are values.

* The context of an inner function includes the scope of the outer function.
* An inner function enjoys that context even after the parent function have returned.
* Function scope works like block scope.

```
var digit_name = (function () {
    var names = ['zero', 'one', 'two'];

    return function (n) {
        return names[n];
    }
}());
```

### Pseudoclassical Inheritance
```
function Gizmo(id) {
    this.id = id;
}

Gizmo.prototype.toString = function () {
    return "gizmo " + this.id;
}
```

Possible implementation of the `new` operator as a method.
```
Function.prototype.new = function new() {
    var that = Object.create(this.prototype),
    result = this.apply(that, arguments);
    return (
        typeof result === 'object' &&
        result !== null
    )
        ? result
        : that;
};
```

If we replace the original ptototype object, then we can inherit another object's stuff.
```
function Hoozit(id) {
    this.id = id;
}
// This way, Hoozit inherits from Gizmo.
Hoozit.prototype = new Gizmo();
Hoozit.prototype.test = function (id) {
    return this.id === id;
}
```
### Module Pattern
A better way to create objects in JavaScript.

#### Singleton
```
var singleton = (function () {
    var privateVariable;
    function privateFunction(x) {
        ...privateVariable...
    }
    return {
        firstMethod: function (a, b) {
            ...privateVariable...
        },
        secondMethod: function (c) {
            ...privateFunction()...
        }
    };
}());
```

#### Power Constructors
Module pattern is easily transformed into a powerful constructor pattern.

1. Make an object.
    * Object literal
    * `new`
    * `Object.create`
    * call another power constructor
2. Defined some variables and functions.
    * These become private members.
3. Augment the object with privileged methods.
4. Return the object.

```
// We use the lowercase "c", because we don't need the new keyword to create a new instance of this object.
// The spec object is an object literal which allows us to pass named parameters to our constructor.
function constructor(spec) {
    // Step 1
    var that = otherMaker(spec),
        // Step 2
        member, // This is a private variable
        // Step 3
        method = function () {
            // access to spec, member, method without using "this"
        };
    that.method = method; // A public method
    // Step 4
    return that;
}

// ES6 version
function constructor(spec) {
    // Curly braces indicate that member = spec.member.
    let {member} = spec;
    const {other} = other_constructor(spec);
    const method = function () {
        // spec, member, other, method
    };
    return Object.freeze({
        // Shorthand for "method: method" and "other: other".
        method,
        other
    });
}
```

### Pseudoclassical Inheritance vs Functional Inheritance

Same example as before, using the Power Constructor. Note: this technique uses more memory than the prototypal inheritance.

```
function gizmo(id) {
    return {
        toString: function () {
            return "gizmo " + id;
        }
    };
}

function hoozit(id) {
    var that = gizmo(id);
    that.test = function (testid) {
        return testid === id;
    }
    return that;
}
```

## The Metamorphosis of Ajax

### The Browser
How a browser works:
1. URL is fetched by the Fetch engine.
2. The fetched content is stored into a Cache.
3. The cached content is parsed.
4. The parsing produces a Tree.
5. The Tree is given to the Flow/Layout engine that creates a Display list.
6. The Display List is given to the Paint engine that produces Pixels that are painted on the screen.

In order to be able to display images, when the Parse engine finds an image, it goes back to the Fetch engine to retrieve the image.

### Scripted Browser (the event loop)
When scripting was added to the browser, an event loop is part of the browser. The event loops listens for events on the UI (mouse move, keyboard typing, timeout, ...): this causes a script to run. Once the script has finished running (it is guaranteed that script won't be interrupted by the next event), it may cause a new Flow/Paint.

1. Flow.
2. Paint.
3. Event.
4. Script.
5. Back to step 1.

### DOM Performance

* Touching a node has a cost.
* Styling can have a big cost.
* Reflow can have a big cost.
* Repaint can have a big cost.
* Random things like nodelist can have a big cost.
* In most applications, JavaScript has a small cost.

### JavaScript Libraries

* Portability
* Correction
* Model
* Widgets

A good idea on how to pick a library (as we have so many choices) is to subject all of them to JSLint.

## ES5: The New Parts

### Meta Object API
Two kinds of properties: data properties and accessor properties.

* `Object.definedProperty(object, key, descriptor)`
* `Object.definedProperties(object, object_of_descriptors)`
* `Object.getOwnPropertyDescriptor(object, key)`

#### Property Attributes
We have objects in JavaScript. Each object is composed of properties and each property is composed of attributes. Every property has four attributes. For a data property the attributes are: `value`, `writeable`, `enumerable` and `configurable`. For an accessor property the attributes are: `enumerable`, `configurable`, `get` and `set`.

* `value`: any JavaScript value. The actual value of the property.
* `writeable`: boolean. Determines if the property can be changed.
* `enumerable`: boolean. If `true`, it will show up in for loops or in `Object.keys`.
* `configurable`: boolean. The property can be turned into a data or accessor property.
* `get`: `function () {... return value;}`. A function that will be called when a property is read (e.g.: `object.property1;`).
* `set`: `function (value) {...}`. A function that will be called when a property is set (e.g.: `object.property1 = 2;`).

```
// Object literals in ES3
var my_object = {foo: bar};

// Object literal in ES5
var my_object = Object.create(Object.prototype);
Object.defineProperty(my_object, 'foo', {
    value: bar,
    writeable: true,
    enumerable: true,
    configurable: true
});

// Accessor property
Object.definedProperty(my_object, 'inch', {
    get: function () {
        return this.mm / 25.4;
    },
    set: function (value) {
        this.mm = value * 25.4;
    },
    configurable: false
});
```

#### Security Libraries

* `Object.getOwnPropertyNames(object)`
* `Object.getPrototypeOf(object)`

Best not to use the methods above. They have been introduced for security frameworks that run before anything else in the browser and lock anything that the original designer left unlocked so that third party code can be run safely. The security framework would use these methods and finally destroy them.

```
// This code makes possible to make a copy of an object and let it inherit another prototype.
function replace_prototype(object, prototype) {
    var result = Object.create(prototype);
    Object
        .getOwnPropertyNames(object)
        .forEach(function (key) {
            Object.defineProperty(result, key, Object.getOwnPropertyDescriptor(object, key));
    });
    return result;
}
```
#### Object Extensibility

* `Object.preventExtensions(object)`: the object won't accept new properties.
* `Object.seal(object)`
* `Object.freeze(object)`: prevents extensions and makes every object read-only and non-configurable. It's a key for future optimizations because the compiler knows that a frozen object cannot change.
* `Object.isExtensible(object)`
* `Object.isSealed(object)`
* `Object.isFrozen(object)`

### Strict Mode
Fixes design issues introduce in previous versions of JavaScript. `'use strict';` as the first line of a file or a function puts that file or function into strict mode. Better to avoid the file form because usually files will be merged into one big file.

* No more implied global variables withing functions.
* `this` is not bound to the global object by function form. It will be bound to `undefined` instead.
* `apply` and `call` do not default to the global object.
* No `with` statement.
* Setting a writeable: `false` property will throw. Instead of failing silently.
* Deleting a configurable: `false` property will throw.
* Restrictions on `eval`.
* `eval` and `arguments` are reserved.
* `arguments` not linked to parameters.
* No more `arguments.caller` or arguments.callee`.
* No more octal literals (e.g.: `"08"` won't be interpreted anymore as a number in base 8.
* Duplicate names in an object literal or function parameters are a syntax error.
* Forgetting to use the `new` prefix in strict mode will throw an exception, not silently clobber the global object.

```
// Functions to know if stric mode is on and/or implemented.
function in_strict_mode() {
    return (function () {
        return !this;
    }());
}

function strict_mode_implemented() {
    return (function () {
        'use strict';
        return !this;
    }());
}
```

## Fun With Functions

### Problem 1
Write three binary functions, `add`, `sub` and `mul`, that take two numbers and return their sum, difference and product.

```
add(3, 4) // 7
sub(3, 4) // -1
mul(3, 4) // 12
```

```
function add(first, second) {
    return first + second;
}

function sub(first, second) {
    return first - second;
}

function mul(first, second) {
    return first * second;
}
```

### Problem 2
Write a function `identityf` that takes an argument and returns a function that returns that argument.

```
var three = identityf(3);
three() // 3
```

```
function identityf(x) {

    return function () {
        return x;
    };

}
```

### Problem 3
Write a function `addf` that adds from two invocations.

```
addf(3)(4) // 7
```

```
function addf(first) {
    return function (second) {
        return first + secondy;
    };
}
```

### Problem 4
Write a function `liftf` that takes a binary function and makes it callable with two invocations.

```
var addf = liftf(add);
addf(3)(4) // 7
liftf(mul)(5)(6) // 30
```

```
// This is an higher order function: which receives a function as parameters and returns other functions as results.
function liftf(binary) {
    return function (first) {
        return function (second) {
            return binary(first, second);
        };
    };
}
```

### Problem 5
Write a function `curry` that takes a binary function and an argument and returns a function that can take a second argument.

```
var add3 = curry(add, 3);
add3(4) // 7
curry(mul, 5)(6) // 30
```

```
// Taking a function that takes multiple arguments and turning it into a function that takes a single argument is called currying.
function curry(binary, first) {
    return function (second) {
        return binary(first, second);
    };
}

function curry(binary, first) {
    return liftf(binary)(first);
}

// A version of the curry function that takes an arbitrary number of values.
function curry(func) {
    var slice = Array.prototype.slice,
    args = slice.call(arguments, 1);
    return function () {
        return func.apply(
            null,
            args.concat(slice.call(arguments, 0))
        );
    };
}

// ES6 version
function curry(func, ...first) {
    return function (...second) {
        return func(...first, ...second);
    };
}
```

### Problem 6
Without writing any new functions, show three ways to create the inc function.

```
var inc = _ _ _;
inc(5) // 6
inc(inc(5)) // 7
```

```
inc = addf(1);
inc = liftf(add)(1);
inc = curry(add, 1);
``` 
### Problem 7
Write a function `twice` that takes a binary function and returns a unary function that passes its argument to the binary function twice.

```
add(11, 11) // 22
var double = twice(add);
double(11) // 22
var square = twice(mul);
square(11) // 121
```

```
function twice(binary) {
    return function (a) {
        return binary(a, a);
    };
}
```

### Problem 8
Write `reverse`, a function that reverses the arguments of a binary function.

```
var bus = reverse(sub);
bus(3, 2) // -1
```

```
// This could be an example of a wrapper function used to make two incompatible APIs interoperable.
function reverse (binary) {
    return function (first, second) {
        return binary(second, first);
    };
}

// ES6 version
function reverse (func) {
    return function (...args) {
        return binary(...args.reverse());
    };
}
```

### Problem 9
Write a function `composeu` that takes two unary functions and returns a unary function that calls them both.

```
composeu(doubl, square)(5) // 100
```

```
function composeu(f, g) {
    return function (a) {
        return g(f(a));
    };
}
```

### Problem 10
Write a function `composeb` that takes two binary functions and calls them both.

```
composeb(add, mult)(2, 3, 7) // 35
```

```
function composeb(f, g) {
    return function (a, b, c) {
        return g(f(a, b), c);
    };
}
```

### Problem 11
Write a `limit` function that allows a binary function to be called a limited number of times.

```
var add_ltd = limit(add, 1);
add_ltd(3, 4) // 7
add_ltd(3, 5) // undefined
```

```
function limit(binary, count) {
    return function (a, b) {
        if (count >= 1) {
            count -= 1;
            return binary(a, b);
        }
        return undefined;
    };
}
```

### Problem 12
Write a `from` function that produces a generator that will produce a series of values.

```
var index = from(0);
index() // 0
index() // 1
index() // 2
```

```
function from(start) {
    return function () {
        var next = start;
        start += 1;
        return next;
    }
}
```

### Problem 13
Write a `to` function that takes a generator and an end value, and returns a generator that will produce numbers up to that limit.

```
var index = to(from(1), 3);
index() // 1
index() // 2
index() // undefined
```

```
function to(gen, end) {
    return function () {
        var value = gen();
        if (value < end) {
            return value;
        }
        return undefined;
    }
}
```

### Problem 14
Write a `fromTo` function that produces a generator that will produce values in a range.

```
var index = fromTo(0, 3);
index() // 0
index() // 1
index() // 2
index() // undefined
```

```
function fromTo(start, end) {
    return to(
        from(start-1), end),
        end
    );
}
```

### Problem 15
Write an `element` function that takes an array and a generator and returns a generator that will produce elements from that array.

```
var ele = element(['a', 'b', 'c', 'd'], fromTo(1, 3));
ele() // 'b'
ele() // 'c'
ele() // unefined
```

```
function element(array, gen) {
    return function () {
        var index = gen();
        if (index !== undefined) {
            return array[index];
        }
    }
}
```

### Problem 16
Modify the `element` function so that the generator argument is optional. If a generator is not provided, then each of the elements of the array will be produced.

```
var ele = element(['a', 'b', 'c', 'd']);
ele() // 'a'
ele() // 'b'
ele() // 'c'
ele() // 'd'
ele() // unefined
```


```
function element(array, gen) {
    // Alternative 1) typeof if gen === 'function', this approach will tend to succeed more than gen === undefined.
    // Alternative 2) do a boolish check, e.g.: if !gen. The problem is that the function will behave differently if we pass 0 or 1 as gen.
    if (gen === undefined) {
        gen = fromTo(0, array.length);
    }
    return function () {
        var index = gen();
        if (index !== undefined) {
            return array[index];
        }
    }
}
```

### Problem 17
Write a `collect` function that takes a generator and an array and produces a function that will collect the results in the array.

```
var array = [],
col = collect(fromTo(0, 2), array);
col() // 0
col() // 1
col() // undefined
array // [0, 1]
```

```
function collect(gen, array) {
    return function () {
        var value = gen();
        if (value !== undefined) {
            array.push(value);
        }
        return value;
    };
}
```

### Problem 18
Write a `filter` function that takes a generator and a predicate and produces a generator that produces only the values approved by the predicate.

```
var fil = filter(fromTo(0, 5),
    function third(value) {
        return (value % 3) === 0;
    });
fil() // 0
fil() // 3
fil() // undefined
```

```
function filter(gen, predicate) {
    return function () {
        var value;
        do {
            value = gen();
        } while (
            value !== undefined &&
            !predicate(value)
        ); 
        return value;
    }
}

// In ES6, this will be optimized to use tail recursion
function filter(gen, predicate) {
    return function recur() {
        var value = gen();
        if (
            value === undefined
            || predicate(value)
        ) {
            return value;
        }
        return recur();
    };
}
```

### Problem 19
Write a `concat` function that takes two generators and produces a generator that combines the sequences.

```
var con = concat(fromTo(0, 3), fromTo(0, 2));
con() // 0
con() // 1
con() // 2
con() // 0
con() // 1
con() // undefined
```

```
function concat(gen1, gen2) {
    var gen = gen1;
    return function () {
        var value = gen();
        if (value !== undefined) {
            return value;
        }
        gen = gen2;
        return gen();
    };
}

// ES6 version
function concat(...gens) {
    var next = element(gens),
    gen = next();
    return function recur() {
        var value = gen();
        if (value === undefined) {
            gen = next();
            if (gen !== undefined) {
                return recur();
            }
        }
        return value;
    };
}
```

### Problem 20
Make a function `gensymf` that makes a function that generates unique symbols.

```
var geng = gensymf("G"),
    genh = gensymf("H");
geng() // "G1"
genh() // "H1"
geng() // "G2"
genh() // "H2"
```

```
function gensymf(prefix) {
    var number = 0;
    return function () {
        number += 1;
        return prefix + number;
    };
}
```

### Problem 21
Write a function `gensymff` that takes a unary function and a seed an returns a `gensymf`.

```
var gensymf = gensymff(inc, 0),
    geng = gensymf("G"),
    genh = gensymf("H");
geng() // "G1"
genh() // "H1"
geng() // "G2"
genh() // "H2"
```

```
function gensymff(unary, seed) {
    // If we moved the declaration of the variable number here, we would generate "G1", "H2", "G3", "H4", ..., thus making it visible to every generator. 
    return function (seed) {
        var number = seed;
        return function () {
            number = unary(number);
            return prefix + number;
        };
    };
}
```

### Problem 22
Make a function `fibonaccif` that returns a generator that will retrun the next fibonacci number.

```
var fib = fibonaccif(0, 1);
fib() // 0
fib() // 1
fib() // 1
fib() // 2
fib() // 3
fib() // 5
```

```
function fibonaccif(a, b) {
    var i = 0;
    return function () {
        var next;
        switch (i) {
        case 0:
            i = 1;
            return a;
        case 1:
            i = 2;
            return b;
        default:
            next = a + b;
            a = b;
            b = next;
            return next;
        }
    };
}

function fibonaccif(a, b) {
    return function () {
        var next = a;
        a = b;
        b += next;
        return next;
    };
}

function fibonaccif(a, b) {
    return concat(
            concat(
                limit(identityf(a), 1)
                limit(identityf(b), 1)
            ), function fibonacci() {
                    var next = a + b;
                    a = b;
                    b = next;
                    return next;
                }
            );
}

function fibonaccif(a, b) {
    return concat(
        element([a, b]),
        function fibonaccif(a, b) {
            var next = a + b;
            a = b;
            b = next;
            return next;
        }
    );
}
```

### Problem 23
Write a `counter` function that returns an object containing two functions that implement an up/down counter, hiding the counter.

```
var object = counter(10),
    up = object.up,
    down = object.down;
up() // 11
down() // 10
down() // 9
up() // 10
```

```
function counter(value) {
    return {
        up: function () {
            value += 1;
            return value;
        },
        down: function () {
            value -= 1;
            return value;
        }
    };
}
```

### Problem 24
Make a `revocable` function that takes a binary function and returns an object containing an `invoke` function that can invoke the binary binary function and a `revoke` function that disables the `invoke` function.

```
var rev = revocable(add),
    add_rev = rev.invoke;
add_rev(3, 4); // 7
rev.revoke();
add_rev(5, 7); // undefined
```

```
function revocable(binary) {
    return {
        invoke: function (first, second) {
            if (binary !== undefined) {
                return binary(first, second);
            }
        },
        revoke: function () {
            binary = undefined;
        }
    };
}
```

### Problem 25
Write a function `m` that takes a value and an optional source string and returns them in an object.

```
JSON.stringify(m(1)) // {"value": 1, "source": "1"}
JSON.stringify(m(Math.PI, "pi")) // {"value": 3,14159, "source": "pi"}
```

```
function m(value, source) {
    return {
        value: value,
        source: (typeof source === 'string')
            ? source
            : String(value)
    };
}
```

### Problem 26
Write a function `addm` that takes two `m` objects and return an `m` object.

```
JSON.stringify(addm(m(3), m(4))) // {"value": 7, "source": "(3+4)"}
JSON.stringify(addm(m(1), m(Math.PI, "pi"))) // {"value": 4.13159, "source": "(1+pi)"}
```

```
function addm(a, b) {
    return m(
        a.value + b.value,
        "(" + a.source + "+" + b.source + ")"
    );
}
```

### Problem 27
Write a function `liftm` that takes a binary function and a string and returns a function that acts on m objects.

```
var addm = liftm(add, "+");
JSON.stringify(addm(m(3), m(4))) // {"value": 7, "source": "(3+4)"}
JSON.stringify(liftm(mul, "*")(m(3), m(4))) // {"value": 12, "source": "(3*4)"}
```

```
// This is a monad
function liftm(binary, op) {
    return function (a, b) {
        return m(
            binary(a.value, b.value),
            "(" + a.source + op + b.source + ")"
        );
    };
}
```

### Problem 28
Modify function `liftm` so that the functions it produces can accept arguments that are either numebers or m objects.

```
var addm = liftm(add, "+");
JSON.stringify(addm(3, 4)) // {"value": 7, "source": "(3+4)"}
```

```
function liftm(binary, op) {
    return function (a, b) {
        if (typeof a === 'number') {
            a = m(a);
        }
        if (typeof b === 'number') {
            b = m(b);
        }
        return m(
            binary(a.value, b.value),
            "(" + a.source + op + b.source + ")"
        );
    };
}
```

### Problem 29
Write a function `exp` that evaluates simple array expressions.

```
var sae = [mul, 5, 11];
exp(sae) // 55
exp(42) // 42
```

```
function exp(value) {
    return (Array.isArray(value))
        ? value[0](value[1], value[2])
        : value;
}
```

### Problem 30
Modify `exp` to evaluate nested array expressions.

```
var nae = [
    Math.sqrt,
    [
        add,
        [square, 3],
        [square, 4]
    ]
];
exp(nae) // 5
```

```
function exp(sae) {
    return (Array.isArray(value))
        ? value[0](exp(value[1]), exp(value[2]))
        : value;
}
```

### Problem 31
Write a function `addg` that adds from many invocations that adds from many invocations, until it sees an empty invocation.

```
addg() // undefined
addg(2)() // 2
addg(2)(7)() // 9
addg(3)(0)(4)() // 7
addg(1)(2)(4)(8)() // 15
```

```
// retursion: a function returns itself
function addg(first) {
    function more(next) {
        if (next === undefined) {
            return first;
        }
        first += next;
        return more;
    }
    if (first !== undefined) {
        return more;
    }
}
```

### Problem 32
Write a function `liftg` that will take a binary funtion and apply it to many invocations.

```
liftg(mul)() // undefined
liftg(mul)(3)() // 3
liftg(mul)(3)(0)(4)() // 0
liftg(mul)(1)(2)(4)(8)() // 64
```

```
function liftg(binary) {
    return function (first) {
        if (first === undefined) {
            return first;
        }
        return function more(next) {
            if (next === undefined) {
                return first;
            }
            first = binary(first, next);
            return more;
        };
    };
}
```

### Problem 33
Write a function `arrayg` that will build an array from many invocations.

```
arrayg() // []
arrayg(3)() // [3]
arrayg(3)(4)(5)() // [3, 4, 5]
```

```
function arrayg(first) {
    var array = [];
    function more(next) {
        if (next === undefined) {
            return array;
        }
        array.push(next);
        return more;
    }
    return more(first);
}

// Using liftg
function arrayg(first) {
    if (first === undefined) {
        return [];
    }
    return liftg(
        function (array, value) {
            array.push(value);
            return array;
        }
    )([first]);
}
```

### Problem 34
Make a function `continuize` that takes a unary function and returns a function that takes a callback and an argument.

```
sqrtc = continuize(Math.sqrt);
sqrtc(alert, 81) // 9
```

```
function continuize(unary) {
    return function (callback, arg) {
        return callback(unary(arg));
    }
}

// ES6 version
function continuize(any) {
    return function (callback, ...x) {
        return callback(any(...x));
    }
}

```

### Problem 35
Make an array `wrapper` object with methods `get`, `store` and `append`, such that an attacker cannot get access to the private array.

```
myvector = vector();
myvector.append(7);
myvector.store(1, 8);
myvector.get(0) // 7
myvector.get(1) // 8
```

```
function vector() {
    var array = [];

    return {
        get: function get(i) {
            return array[i];
        },
        store: function store(i, v) {
            array[i] = v;
        },
        append: function append(v) {
            array.push(v);
        }
    };
}

// A security flaw would let an attacker gain access to "array" with the code below.
var stash;
myvector.store('push', function () {
    stash = this;
})'
myvector.append(); // stash is array.

// The code below fixes the security issue described above.
function vector() {
    var array = [];

    return {
        get: function get(i) {
            return array[+i];
        },
        store: function store(i, v) {
            // Force i to be a number or NaN.
            array[+i] = v;
        },
        append: function append(v) {
            // Alternatively, don't use push.
            array[array.length] = v;
        }
    };
}
```

### Problem 36
Make a function that makes a publish/subscribe object. It will reliably deliver all publications to all subscribers in the right order.

```
my_pubsub = pubsub();
my_pubsub.subscribe(log);
my_pubsub.publish("It works!"); // log("It works!")
```

```
function pubsub() {
    var subscribers = [];
    return {
        subscribe: function (subscriber) {
            subscribers.push(subscriber);
        },
        publish: function (publication) {
            var i, length = subscribers.length;
            for (i = 0; i < length; i += 1) {
                subscribers[i](publication);
            }
        }
    };
}

// The code above has several security flaws:
// 1. Prevent subscribers from receiving messages.
// The attack is to call my_pubsub.subscribe(), this will generate an exception during when publish is called. The fix is to wrap the call to subscribers[i] in a try/catch.
// An alternative attack, is to set my_pubsub.publish = undefined. The solution is to freeze the object.
// Another attack is to use my_pubsub.subscribe(function () { this.length = 0; }). subscribers[i](publication) is a method call on the subscribers array itself so "this" in our function is the actual array. The fix is to assign subscribers[i] to a local variable, use Function.apply/Function.call or use forEach instead.
// 2. Allow the notification of subscribers out of order.
// Using my_pubsub.subscribe(limit(function () { my_pubsub.publish("Out of order"); }, 1)), this way as our method calls the publish function itself subscribers get notified out of order. One way to fix it is to disable the publish method while we are notifying subscribers. Alternatively we can use an asynchronous solution by using setTimeout. An attacker, could always cancel the setTimeout by guessing the number returned by setTimeout.

// The following code fixes the security issues described above.
function pubsub() {
    var subscribers = [];
    return Object.freeze({
        subscribe: function (subscriber) {
            subscribers.push(subscriber);
        },
        publish: function (publication) {
            var i, length = subscribers.length;
            subscribers.forEach(function (s) {
                setTimeout(function () {
                    s(publication);
                }, 0);
            });
        }
    });
}
```

## Managing Asynchronicity

### RQ
A JavaScript library for managing asynchronicity in server applications.

#### `RQ.sequence(requestors)`
Takes an array of requestor functions, calls them one at a time, passing the result of the previous requestor to the next requestor.

```
getNav = RQ.sequence([
    read_file(filename),
    getPreference,
    getCustomNav
]);
```

#### `RQ.parallel(requestors)`
Takes an array of requestor functions, calls them all at once and gives an array of results.

```
getStuff = RQ.parallel([
    getNav,
    getAds,
    getMessageOfTheDay
]);
```

Also, takes an optional array of optional requestors. Their results will be included if they can be obtained before the required requestors finish.

```
getStuff = RQ.parallel([
    getNav,
    getAds,
    getMessageOfTheDay
], [
    getHoroscope,
    getGossip
]);
```

#### `RQ.race(requestors)`
Takes an array of requestors, calls them all at once and gives the result of the first success.

```
getAds = RQ.race([
    getAd(adnet.klikHaus),
    getAd(adnet.inUFace),
    getAd(adnet.trackPipe)
]);
```

#### `RQ.fallback(requestors)
Takes an array of requestors and gives the result of the first success.

```
getWeather = RQ.fallback([
    fetch("weather", localCache),
    fetch("weather", localDB),
    fetch("weather", remoteDB)
]);
```

RQ | All at once | One at a time
---|-------------|--------------
All | `parallel` | `sequence`
One | `race` | `fallback`

Every RQ API takes a timeout. The requests are failed if they do not complete in the given time in milliseconds. Requests can be canceled, but their cancellation is not guaranteed.

#### RQ Types

* `requestor`: A function that can execute a request.

```
requestor(
    callback(success, failure),
    value
) -> cancel(reason)
```

A requestor takes a callback and can take a value which is the result of a previous requestor. It can return a cancel function.

* `callback`: A continuation function that will be passed to a requestor.

```
callback (success, failure)
```

Takes two functions: one to handle the successful case and another one to handle failures.

* `factory`: A function that takes arguments and returns a requestor function.


```
factory(arguments...) ->
    requestor(
        callback(success, failure),
        value
    ) -> cancel(reason)
```

Factories generate requestors.

* `cancel`: A function returned by a requestor that may be used to cancel a request.

## The Better Parts

### New Good Parts in ES6

* Proper tail calls: `return func();`.
* Ellipsis operator

```
function curry(func, ...first) {
   return function (...second) {
        return func(...first, ...second);
    }; 
}

function curry(func) {
    var slice = Array.prototype.slice, args = slice.call(arguments, 1);
    return function () {
        return func.apply(null, args.concat(slice.call(arguments, 0)));
    };
}
```

* Modules.
* `let` and `const`

Beware of the difference between `const` and `freeze`. You could declare an object as `const` but still mutate its properties. `const` is for variables, `freeze` is for values.
```
const fax;
fax = faz; // bad
fax.fay = faz; // ok
```

* Destructuring

```
let {that, other} = some_object;
// is equivalent to:
let that = some_object.that, other = some_object.other;
```

* WeakMap: any JavaScript value and use it as a key (e.g.: values are not automatically converted to strings anymore). If an object exists only as the key of a property in a WeakMap, it will be garbage collected automatically.
* Megastring literal: strings can be organized in multiple lines using the \` operator.
* Fat arrow functions

```
// This will not return an object literal as expected, but will return undefined.
(name) => {id: name}
```

### Bad Parts in ES6

* `class`: it is just syntactic sugar on top of the prototypal model. It is not going to work the way people might expect, also keeps the developer trapped in the mindset of the classical model, making it impossible to learn the functional model.

### Good Parts Reconsidered

* I stopped using `new` years ago. Use `Object.create` instead. In the end, if we stop using `this`, we do not even need `Object.create` anymore.
* I stopped using `null`.
* I stopped using falsiness. Comparisons need to be as explicit as possible.
* I do not use `for`. Use `array.forEach`.
* I do not use `for in`. It never worked right because it goes up the prototype chain. Use `Object.keys(object).forEach`.
* Once ES6 will have proper tail calls, I will stop using `while`

```
// With proper tail recursion, both functions run at the same speed and consume the same amount of memory.
function repeat(func) {
    while (func() !== undefined) {
    }
}

function repeat(func) {
    if (func() !== undefined) {
        return repeat(func);
    }
}
```

### Evolution of Inheritance

#### Systems Languages vs Application Languages
System languages are used to write low level programs like kernels, memory managers, .... All the rest, is written using application languages.

#### Classical Inheritance vs  Prototypal Inheritance
Classical inheritance has the problem is classification taxonomy described above. Extension in classical inheritance creates coupling. Protoypal systems eliminate the coupling introduced by extension.

##### Prototypal Inheritance

* Memory conservation is one of the advantages, but it makes less sense nowadays. It introduces the confusion between own and inherited properties.
* Retroactive heredity: we can change what an object inherits after it is constructed. This might inhibit performances.

##### Class Free Object Oriented Programming
Is the programming model that we create with JavaScript.

* Block Scope.
* Function Scope.
* Closure.
* When a function returns another function, the inner survives the outer.

Recommended model for doing class free object oriented programming using functions in JavaScript.

```
// The constructor takes a specification object.
function constructor(spec) {
    let
        // Instance variables can be initialized from the specification object.
        {member} = spec,
        // Other methods we get from other constructors. Idea: we can have as many "other_constructor" as we want.
        {other} = other_constructor(spec),
        method = function () {
            // can access: member, other, method, spec
        };
    // Public method go into the returned object. We obtain a hard incorruptible interface.
    return Object.freeze({
        method,
        other
    });
}
```
