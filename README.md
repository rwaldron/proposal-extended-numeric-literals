# Extended Numeric Literal

Stage 1 (Alternative Proposal)

Champion: Rick Waldron, Leo Balter

## Motivation



### Generalizing BigInt

(unchanged from original proposal)

Currently, JavaScript contains just one numeric type, [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number). A second type, [BigInt](https://github.com/tc39/proposal-bigint), is proposed. To differentiate the literal syntaxes, BigInts are written ending with an `n`, e.g., `19824359823509831298352352n`. In the case of BigInt, this is a one-off change to JavaScript grammar.

Other numeric types which may be added, either in JavaScript library code or eventually as a built-in:
- IEEE 754-2008 64-bit decimals
- Arbitrary-precision decimals
- Rationals
- Complex numbers

One side to all of these is operator overloading, and another side is the literal syntax. In some small code samples, it seems like using extended literals, together with methods for arithmetic operators, might not be such a bad middle point, until more is worked out.

### Use Cases

#### CSS Typed OM Level 1: Numeric Values

[CSS Typed OM Level 1](https://drafts.css-houdini.org/css-typed-om/) specifies [Numeric Values](https://drafts.css-houdini.org/css-typed-om/#numeric-objects) and provides [Numeric Factory Functions](https://drafts.css-houdini.org/css-typed-om/#numeric-factory) to create objects that represent a quantity (usually a "length" or "distance") in a variety of useful units. The current API for creating these objects is either `new CSSUnitValue(1, "px")` or a shorthand: `CSS.px(1)`. This proposal aims to provide support in the JavaScript language for creating custom Numeric Literal Extensions that allow userland code to express numbers in meaningful representations.

```js
Number.extensions.set("px", CSS.px);

document.querySelector("#foo").style.fontSize = 3~px;
```

#### 



## Proposed syntax

See: [ExtendedNumericLiteral](https://rwaldron.github.io/proposal-extended-numeric-literals/#prod-ExtendedNumericLiteral)

## Semantics

Similarly to template literals, extended numeric literals desugar into passing an object literal as an argument to a function. The object has two own properties:
- `string`: a String Value consisting of the code units of _NumericLiteral_
- `number`: the MV of _NumericLiteral_

Example:

`3~px` desugars into `Number.extensions.get("px")(Object.freeze({number: 3, string: 3}))`

### Caching

The object which is passed into the function is cached such that multiple executions of the same code will have the same object passed into the extended literal function. This can be useful for literals which require an expensive calculation to parse: the object can be used as a key in a WeakMap associating it with a pre-calculated value.

New semantics are under consideration for template string literals ([PR](https://github.com/tc39/ecma262/pull/890)) and would make sense for numeric literals as well. While under development, this proposal matches the main specification's logic, caching by the string value of the NumericLiteral rather than source position.

