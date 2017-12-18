# json-diff-patch

A function that compares two JavaScript-objects and returns an array of JSON Patch-operations representing the difference between the two objects.

## Install

```bash
npm install json-diff-patch
```

## Usage

To use the function, simply ```require``` it and pass two objects you want to compare to it:

```javascript
const jsonDiffPatch = require('json-diff-patch');

jsonDiffPatch({ foo: 'bar' }, { foo: 'baz' });
// => [{ op: 'replace', path: '/foo', value: 'baz' }]

jsonDiffPatch({}, { foo: 'bar' });
// => [{ op: 'add', path: '/foo', value: 'bar' }]

jsonDiffPatch({ foo: 'bar' }, {});
// => [{ op: 'remove', path: '/foo' }]

jsonDiffPatch([1, 2, 3], [1, 2, 4]);
// => [{ op: 'replace', path: '/2', value: 4}]

jsonDiffPatch([1, 2, 3], [1, 2, 3, 4]);
// => [{ op: 'add', path: '/3', value: 4}]

jsonDiffPatch([1, 2, 3], [1, 2]);
// => [{ op: 'remove', path: '/2'}]
```

You can pass objects with arbitrary complexity:

```javascript

let objA = {
    name: 'Bob',
    age: 25,
    address: {
        street: '468 Ash Street',
        city: 'Bedias, South Carolina',
        zipCode: '1669'
    },
    phoneNumbers: [
        '+1 (908) 493-3564',
        '+1 (858) 591-3751'
    ]
};

let objB = {
    name: 'Bobby',
    age: 26,
    address: {
        street: '468 Ash Street',
        city: 'Bedias, South Carolina',
        zipCode: '1669'
    },
    phoneNumbers: [
        '+1 (908) 493-3564'
    ],
    email: 'bob@gmail.com'
}

jsonDiffPatch(objA, objB);
/*
    => [
        { op: 'replace', path: '/name', value: 'Bobby' },
        { op: 'replace', path: '/age', value: 26 },
        { op: 'remove', path: '/phoneNumbers/1' },
        { op: 'add', path: '/email', value: 'bob@gmail.com' }
    ]
*/
```

The function validates the input arguments and throws a ```NoValidInputJSONError``` if at lest one of the input arguments...

* ...is ```null``` or ```undefined```
* ...is of a primitive type (string, boolean, number)
* ...is an object that is not valid JSON (i.e. a function)
* ...contains a circular reference
* ...does not have the same type as the other input argument