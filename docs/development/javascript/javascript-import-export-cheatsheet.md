---
title: 'Javascript Import Export Cheatsheet'
tags: ['development','javascript','cheatsheet']
date: 2023-01-04
---
# Javascript Import Export Cheatsheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

There are two main approaches of exposing values, objects and functions from one JS file to another:

1. Common JS
2. ES6

## Common JS
Common JS is the syntax built into node.js to support importing and exporting logic.

!!! example "Exposing logic from a JS file"

    Use `module.exports` to define what members to expose as an object:

    ```js
    //myModule.js
    //----------------
    'use strict';
    const privFunc = (value) => {
        console.log('value: ', inputValue);
    };

    const pubDefaultValue = 10;

    const pubFunc = (value) => {
        privFunc(value);
        return value + 1;
    };
    
    //expose the following members as an object:
    //1. pubDefaultValue
    //2. pubFunc
    module.exports = {
        pubDefaultValue,
        pubFunc
    };
    ```

!!! example "Importing the exposed logic as an object"

    ```js
    //importExample.js
    //----------------
    'use strict';
    const myModule = require('./path/myModule'); //import full object

    const run = () => {
        // reference each exposed member via the exposed object
        return myModule.publicFunc(myModule.pubDefaultValue);
    };
    ```

!!! example "Importing specific members from the JS file using object deconstruction"

    ```js
    //importExample2.js
    //----------------
    'use strict';
    const { pubFunc } = require('./path/myModule'); //import specific members

    const run2 = () => {
        //reference the exposed member directly
        return pubFunc(10);
    };
    ```

## ES6
ECMAScript 6 supports a more modern way of importing and exporting logic.

!!! example "Exposing logic from a JS file"

    Use `export` keyword to define what members to expose:

    ```js
    //myModule.js
    //----------------
    const privFunc = (value) => {
        console.log('value: ', inputValue);
    };

    export const pubDefaultValue = 10;

    export const pubFunc = (value) => {
        privFunc(value);
        return value + 1;
    };
    ```

    You can also `export` an `default` function or object.

    ```js
    //myModule.js
    //----------------
    const privFunc = (value) => {
        console.log('value: ', inputValue);
    };

    export const pubDefaultValue = 10;

    const pubFunc = (value) => {
        privFunc(value);
        return value + 1;
    };

    //export the exposedFunc by default
    export default pubFunc;
    ```

!!! example "Importing the default export from a JS file"

    ```js
    //importExample.js
    //----------------
    import myModule from './path/myModule'; //import default export

    const run = () => {
        return myModule(10); // myModule == pubFunc
    };
    ```

!!! example "Importing specific exports from a JS file using object deconstruction"

    ```js
    //importExample2.js
    //----------------
    import { pubFunc } from './path/myModule'; //specific exports

    const run2 = () => {
        return pubFunc(10);
    };
    ```

!!! example "Importing all exports from a JS file and put them behind a "namespace""

    ```js
    //importExample3.js
    //----------------
    import * as myModule from './path/myModule'; //all named members behind namespace

    const run3 = () => {
        return myModule.pubFunc(10);
    };
    ```

See also:

* <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export>
* <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import>