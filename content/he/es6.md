# ES6 on io.js

io.js is built against modern versions of [V8](https://code.google.com/p/v8/). By keeping up-to-date with the latest releases of this engine we ensure new features from the [JavaScript ECMA-262 specification](http://www.ecma-international.org/publications/standards/Ecma-262.htm) are brought to io.js developers in a timely manner, as well as continued performance and stability improvements.

Version 1.2.0 of io.js ships with V8 4.1.0.14, which includes ES6 features well beyond version 3.26.33 that will be shipped with joyent/node@0.12.x.

## No more --harmony flag

On joyent/node@0.12.x (V8 3.26), the `--harmony` runtime flag enabled all **completed**, **staged** and **in progress** ES6 features together, in bulk (with the exception of nonstandard/non-harmonious semantics for `typeof` which were hidden under `--harmony-typeof`). This meant that some really buggy or even broken features like [proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) were just as readily available for developers as [generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*), which had very little or even no known-issues. As such, it was best practice to either enable only certain features by using specific runtime harmony feature flags (e.g. `--harmony-generators`), or simply enable all of them and then use a restricted subset.

With io.js@1.x (V8 4.1+), all that complexity goes away. All harmony features are now logically split into three groups for **shipping**, **staged** and **in progress** features:

*   All **shipping** features, the ones that V8 has considered stable, like [generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*), [templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings), [new string methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla#Additions_to_the_String_object) and many others are turned **on by default on io.js** and do **NOT** require any kind of runtime flag.
*   Then there are **staged** features which are almost-completed features that haven't been completely tested or updated to the latest spec yet and therefore are not considered stable by the V8 team (e.g. there might be some edge cases left to discover). This is probably the equivalent of the state of [generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) on 3.26. These are the "use at your own risk" type of features that now require a runtime flag: `--es_staging` (or its synonym, `--harmony`).
*   Finally, all **in progress** features can be activated individually by their respective harmony flag (e.g. `--harmony_arrow_functions`), although this is highly discouraged unless for testing purposes.

## Which ES6 features ship with io.js by default (no runtime flag required)?


*   Block scoping

    *   [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

    *   [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

    *   `function`-in-blocks

    >As of v8 3.31.74.1, block-scoped declarations are [intentionally implemented with a non-compliant limitation to strict mode code](https://groups.google.com/forum/#!topic/v8-users/3UXNCkAU8Es). Developers should be aware that this will change as v8 continues towards ES6 specification compliance.

*   Collections

    *   [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

    *   [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)

    *   [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

    *   [WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)*   [Generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)

*   [Binary and Octal literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Numeric_literals)

*   [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

*   [New String methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla#Additions_to_the_String_object)

*   [Symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

*   [Template strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings)

You can view a more detailed list, including a comparison with other engines, on the [compat-table](https://kangax.github.io/compat-table/es6/) project page.

## Which ES6 features are behind the --es_staging flag?

*   [Classes](https://github.com/lukehoban/es6features#classes) (strict mode only)
*   [Object literal extensions](https://github.com/lukehoban/es6features#enhanced-object-literals)

*   [`Symbol.toStringTag`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) (user-definable results for `Object.prototype.toString`)

## I have my infrastructure set up to leverage the --harmony flag. Should I remove it?

The current behaviour of the `--harmony` flag on io.js is to enable **staged** features only. After all, it is now a synonym of `--es_staging`. As mentioned above, these are completed features that have not been considered stable yet. If you want to play safe, especially on production environments, consider removing this runtime flag until it ships by default on V8 and, consequently, on io.js. If you keep this enabled, you should be prepared for further io.js upgrades to break your code if V8 changes their semantics to more closely follow the standard.

## How do I find which version of V8 ships with a particular version of io.js?

io.js provides a simple way to list all dependencies and respective versions that ship with a specific binary through the `process` global object. In case of the V8 engine, type the following in your terminal to retrieve its version:

```sh
iojs -p process.versions.v8
```
