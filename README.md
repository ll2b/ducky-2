
Ducky &mdash; http://duckyjs.com/
=================================

**Duck-Typed Value Handling for JavaScript** 

<p/>
<img src="https://nodei.co/npm/ducky.png?downloads=true&stars=true" alt=""/>

<p/>
<img src="https://david-dm.org/rse/ducky.png" alt=""/>

Abstract
--------

This is a JavaScript library for Duck-Testing values,
for use in [Node.js](http://nodejs.org/) based server and browser based client
environments.

Getting Ducky
----------------

You can conveniently get Ducky in various ways:

- Git: directly clone the official repository

  `$ git clone https://github.com/rse/ducky.git`

- Bower: install as client component via the Bower component manager:

  `$ bower install ducky`

- cURL: downloading only the main file from the repository

  `$ curl -O https://raw.github.com/rse/ducky/master/lib/ducky.js`

API
---

Ducky provides the following functions:

#### select(object: Object, path: String, value?: Object): Object

Dereference into (and this way subset) `object` according to the
`path` specification and either return the dereferenced value or
set a new `value`. Object has to be a hash or array object. The
`path` argument has to follow the following grammar (which is a
direct JavaScript dereferencing syntax):

LHS          |     | RHS                    
------------ | --- | -----------------------------
path         | ::= | segment segment\*       
segment      | ::= | bybareword &#124; bykey  
bybareword   | ::= | `"."`? identifier         
bykey        | ::= | `"["` key `"]"`            
identifier   | ::= | `/[_a-zA-Z$][_a-zA-Z$0-9]*>/`
key          | ::= | number &#124; squote &#124; dquote
number       | ::= | `/[0-9]+/`
dquote       | ::= | `/"(?:\\"|.)*?"/`
squote       | ::= | `/'(?:\\'|.)*?'/`

Setting the `value` to `undefined` effectively removes the
dereferenced value. If the dereferenced parent object is a hash, this
means the value is `delete`'ed from it. If the dereferenced parent
object is an array, this means the value is `splice`'ed out of it.

    ducky.select({ foo: { bar: { baz: [ 42, 7, "Quux" ] } } },
        "foo['bar'].baz[2]") // &rarr; "Quux"

#### validate(object: Object, spec: String): Boolean

Validate an arbitrary nested JavaScript object `object` against the
specification `spec`. The specification `spec` has to be either
a `RegExp` object for `String` validation, a validation function of signature
`spec(Object): Boolean` or a string following the following grammar (which
is a mixture of JSON-like structure and RegExp-like quantifiers):

LHS          |     | RHS                    
------------ | --- | -----------------------------
spec         | ::= | not &#124; alt &#124; hash &#124; array &#124; any &#124; primary &#124; class
not          | ::= | `"!"` spec
alt          | ::= | `"("` spec (`"&#124;"` spec)\* `")"`
hash         | ::= | `"{"` (key arity? `":"` spec (`","` key arity? `":"` spec)\*)? `"}"`
array        | ::= | `"["` (spec arity? (`","` spec arity?)\*)? `"]"`
arity        | ::= | `"?"` &#124; `"*"` &#124; `"+"` &#124; `"{"` number `","` (number &#124; `"oo"`) `"}"`
number       | ::= | `/^[0-9]+$/`
key          | ::= | `/^[_a-zA-Z$][_a-zA-Z$0-9]*$/ &#124; `"@"`
any          | ::= | `"any"`
primary      | ::= | `/^(?:null&#124;undefined&#124;boolean&#124;number&#124;string&#124;function&#124;object)$/`
class        | ::= | `/^[A-Z][_a-zA-Z$0-9]\*$/`

The special key `@` can be used to match an arbitrary hash element key.

    validate({ foo: "Foo", bar: "Bar", baz: [ 42, 7, "Quux" ] },
        "{ foo: string, bar: any, baz: [ number+, string* ], quux?: any }") // &arr; true

#### params(P<name>: T<String>, P<args>: T<Object[]>, P<spec>: T<Object>): T<Object>

  Handle positional and named function parameters by processing
  a function's C<arguments> array. Parameter P<name> is the name
  of the function for use in exceptions in case of invalid parameters.
  Parameter P<args> usually is the JavaScript C<arguments> pseudo-array of
  a function. Parameter P<spec> is the parameter specification: each key
  is the name of a parameter and the value has to be an T<Object> with
  the following possible fields: P<pos> for the optional position in case
  of positional usage, P<def> for the default value (of not required
  and hence optional parameters), P<req> to indicate whether the
  parameter is required and P<valid> for type validation (either
  a string accepted by the M<validate>() method,
  or a valid regular expression C</.../> object
  for validating a T<String> against it or an arbitrary validation callback function
  of signature "P<valid>(T<Object>): T<Boolean>".

  | function config () {
  |     var params = $cs.params("config", arguments, {
  |         scope: { pos: 0, req: true,      valid: "boolean"           },
  |         key:   { pos: 1, req: true,      valid: /^[a-z][a-z0-9_]*$/ },
  |         value: { pos: 2, def: undefined, valid: "object"            },
  |         force: {         def: false,     valid: "boolean"           }
  |     });
  |     var result = db_get(params.scope, params.key);
  |     if (typeof params.value !== "undefined")
  |         db_set(params.scope, params.key, params.value, params.force);
  |     return result;
  | }
  | var value = config("foo", "bar");
  | config("foo", "bar", "quux");
  | config({ scope: "foo", key: "bar", value: "quux", force: true });


License
-------

Copyright (c) 2010-2013 Ralf S. Engelschall (http://engelschall.com/)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

