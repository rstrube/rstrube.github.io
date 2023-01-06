---
title: 'Lodash Cheat Sheet'
tags: ['development','javascript','lodash','cheat sheet']
date: 2023-01-04
---
# Lodash Cheat Sheet {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## get / set

!!! example "Get a property from an object"

    ```js
    //get property value (no fallback)
    const myProp = get(myObj, 'myProp');

    //get property value (with fallback)
    const myProp2 = get(myObj, 'myProp2', 'Fallback value');

    /*
    note: fallback will only be used if the property does not exist, or is undefined if property has a value of 'null' that will be returned.  If you wish to also fallback on 'null' use the following:
    */
    const myProp3 = get(myObj, 'myProp3') || 'Fallback value';
    ```

See also: <https://lodash.com/docs/4.17.15#get>

!!! example "Set a property on an object"

    ```js
    let myObj = {};

    //set a property value
    set(myObj, 'myProp', 'Test value');
    ```

See also: <https://lodash.com/docs/4.17.15#set>

## isEmpty
Used to check if an object, array or string value is: `[]`, `{}`, `''`, `null`, or `undefined`.

!!! danger

    `isEmpty` can only be used for objects, arrays, or strings.

!!! example

    ```js
    _.isEmpty([]);
    // => true

    _.isEmpty({});
    // => true

    _.isEmpty('');
    // => true

    _.isEmpty(null);
    // => true

    _.isEmpty(undefined);
    // => true

    _.isEmpty([1, 2, 3]);
    // => false

    _.isEmpty({ 'a': 1 });
    // => false

    _.isEmpty('Test value');
    // => false

    // Some results that might surprise you!
    // vvv

    _.isEmpty(true);
    // => true

    _.isEmpty(1);
    // => true
    ```

See also: <https://lodash.com/docs/4.17.15#isEmpty>

## reduce
Reduce is one of the most versatile functions in your lodash toolbelt.  At it's core it iterates over a data structure (typically an array or object) and then constructs a new data structure (again typically an array or object) by way of an accumulator.

You define the starting accumulator value which gets fed into the *first* execution of the iteration function.  This first execution of the iteration function can manipulate and return the accumulator, which in turn get's passed in as a new accumulator value into the *second* execution of the iteration function.  This continues until the iteration function has executed across all items in the starting data structure.

The function signature is:

```js
_.reduce(dataToIterate, (accumulator, data, /*optional*/ propKeyOrIndex) => {}, startAccumulator);
```

!!! example

    Let's take a look at an example with the following:

    * Starting data structure to iterate through is an array of integers.
    * Iteration fuction will add next iterated integer to the accumulator and pass it forward
    * The starting accumulator is = 0

    ```js
    _.reduce([1,0,0,3,8,1], (acc, data) => { return acc + data; }, 0);
    // => 15
    ```

    Explanation: we start with a accumulator value of `0`.  We then iterate through the `[1,0,0,3,8,1]` array.  For each iteration we execute a function that accepts the current accumulor, adds the current value from the data structure, and returns the value of the new accumulator.  This new acculumator value get's passed into the next execution of the iteration fuction and so on until we've iterated through all items in the array.

    The sequence would be:

    Iterated Data | Accumulator | New Accumulator
    ------------- | ----------- | ---------------
    1             | 0           | 1
    0             | 1           | 1
    0             | 1           | 1
    3             | 1           | 4
    8             | 4           | 12
    1             | 12          | 13

    The final value of the reduce function would = `13`.

!!! example

    Let's take a look at another example, but this time we're going to specifically ignore any negative numbers.

    !!! note
    
        If don't want to perform any changes to the accumulator, just return it as is.

    ```js
    const dataToIterate = [-1,0,0,-3,8,1,-1,2,1,2,9,-2];

    _.reduce(dataToIterate, (acc, data) => { 
        if(data < 0) { return acc; }

        return acc + data; 
    }, 0);
    // => 23 
    ```

    The sequence would be:

    Iterated Data | Accumulator | New Accumulator
    ------------- | ----------- | ---------------
    -1            | 0           | 0
    0             | 0           | 0
    0             | 0           | 0
    -3            | 0           | 0
    8             | 0           | 8
    1             | 8           | 9
    -1            | 9           | 9
    2             | 9           | 11
    1             | 11          | 12
    2             | 12          | 14
    9             | 14          | 23
    -2            | 23          | 23

    The final value of the reduce function would = `23`.

### Using Reduce with Objects
Reduce can also be used on objects.  When iterating through an object, reduce will call the iteration function for each of the object's properties.

!!! example

    Let's take a look at an example with the following:

    * Starting data structure to iterate through is an object
    * Iteration fuction will go through each property value, and add it an array
    * Starting accumulator is an empty array

    ```js
    const startingObj = {
        prop1: 'first',
        prop2: 2,
        prop3: 'third'
    };

    _.reduce(startingObj, (acc, propValue) => { 
        acc.push(propValue); 
        return acc;
    }, []);
    // => ['first', 2, 'third']
    ```

    The sequence would be:

    Iterated Data | Accumulator           | New Accumulator
    ------------- | --------------------- | ---------------
    'first'       | []                    | ['first']
    2             | ['first']             | ['first', 2]
    'third'       | ['first', 2, 'third'] | ['first', 2 'third']

    The final value of the reduce function would = `['first', 2, 'third']`.

!!! example

    Lets take a look an another example with the following:

    * Starting data structure to iterate through is an object
    * Iteration function will go through each property value, and if it's a string, add it as a property to the new accumulator object with the same property name
    * Starting accumulator is an empty object

    !!! note
        
        Here we are using the optional third parameter in the iteration function, we are also using the `isString()` and `set()` lodash functions

    ```js
    const startingObj = {
        firstName: 'Sam',
        lastName: 'Smith',
        age: 52
    };

    _.reduce(startingObj, (acc, propValue, propKey) => {
        if(!_.isString(propValue)) {
            return acc;
        }

        // equivalent to acc[propKey] = propValue;
        _.set(acc, propKey, propValue); 
        return acc;
    , {});
    // => {
    //      firstName: 'Sam',
    //      lastName: 'Smith'
    //    }
    ```

    The sequence would be:

    Iterated Data | Accumulator                             | New Accumulator
    ------------- | --------------------------------------- | ---------------
    'Sam'         | {}                                      | { firstName: 'Sam'}
    'Smith'       | { firstName: 'Sam'}                     | { firstName: 'Sam', lastName: 'Smith'}
    52            | { firstName: 'Sam', lastName: 'Smith'}  | { firstName: 'Sam', lastName: 'Smith'}

    The final value of the reduce function would be =

    ```js
    {
    firstName: 'Sam',
    lastName: 'Smith'
    }
    ```

Hopefully you can begin to see how versatile and powerful reduce can be in your day to day development.

See also: <https://lodash.com/docs/4.17.15#reduce>