---
id: types
title: Data Types and Values
---

### Statically Typed
Leo is a **statically typed language**, which means we must know the type of each variable before executing a circuit.

### Explicit Types Required
There is no `undefined` or `null` value in Leo. When assigning a new variable, **the type of the value must be explicitly stated**.
The exception to this rule is when a new variable inherits its type from a previous variable.

### Pass by Value
Variables in Leo are always **passed by value**, which means they are always copied when they are used as function inputs or in assignments.

## Booleans
Leo supports the traditional `true` or `false` boolean values. Adding the explicit `bool` type for booleans in statements is optional.

```leo
let a = true;
let b: bool = false;
```

## Integers
Leo supports signed integers `i8`, `i16`, `i32`, `i64`, `i128` 
and unsigned integers `u8`, `u16`, `u32`, `u64`, `u128`; 

```leo
let a = 1u8;
let b: u8 = 5;
```

:::info
Higher bit length integers generate more constraints in the circuit, which can slow down computation time.
:::

### A Note on Leo Integers
Leo will not default to an integer type. The definition of a integer **must** include an explicit type.
After assignment, you can choose to explicitly add the type or let the compiler interpret implicitly.
**Type casting is not yet supported.**

```leo
let a = 2u8; // explicit type    
let b: u8 = 2; // explicit type
let c = b - 1; // implicit type
```

## Field Elements

Leo supports a `field` type for native field elements as unsigned numbers up to the modulus length of the field.
```leo
let a = 1field; 
let b: field = 21888242871839275222246405745257275088548364400416034343698204186575808495617;
```

## Char type

Leo has a `char` type for using characters. Each `char` contains 1 Unicode code point.
```leo
let c = 'c';
let d: char = '¢';
let e = '😉'; // emojis are also supported
```

It is also possible to define characters in a Rust-like manner; some allowed escapes are:
```leo
let unicode_escape = '\u{11FA}';
let ascii = '\x1F';
let escape = '\\';
```

A more comprehensive specification is described in [Leo RFC 1](https://github.com/AleoHQ/leo/blob/master/docs/rfc/001-initial-strings.md).

:::warning
When a user-perceived character is represented by more than one Unicode code point,
it cannot be represented by a single Leo `char`.  Furthermore, since the length of an
array must be declared, you will need to know how many Unicode code points are used
when you want a literal string containing such a character.
Here are some examples:
 - `אֶ` // Hebrew alef with segol
 - `у́` // Cyrillic u with accent
 - `🤷🏿‍♀️` // shrug with various modifiers

If you are not sure how many code points a given user-perceived character requires,
you can use a tool such as [Richard Cook's Namelist tool](http://linguistics.berkeley.edu/~rscook/cgi/nameslistsearch.html)
or [r12a's List characters](https://r12a.github.io/app-listcharacters/).
:::

Examples of Unicode sequences:
```leo
let c: char = 'у́'; // illegal
let c: [1; char] = "у́"; // illegal
let c: [2; char] = "у́"; // correct, same as "\u{443}\u{301}"

let e: char = '🤷🏿‍♀️'; // illegal
let e: [char; 5] = "🤷🏿‍♀️"; // correct, same as "\u{1F937}\u{1F3FF}\u{200D}\u{2640}\u{FE0F}"
```

### Character escapes

- *Unicode escapes* - use `\u{}` escape with 1-6 HEX digits in curly braces for unicode character numbers.
- *ASCII HEX escapes* - use `\x` escape followed by 2 HEX digits with max value of 127 (`\x7F`).
- *Simple escapes* - the following symbols can be escaped: `\0` (meaning `\x00`), `\\`, `\"`, `\'`, `\n`, `\r` and `\t`.


## Group Elements
The set of affine points on the elliptic curve passed into the Leo compiler forms a group.
Leo supports this set as a primitive data type. Group elements are special since their values can be defined as 
coordinate pairs  
`(x, y)group`. The group type keyword `group` must be used when specifying a pair of group coordinates since implicit 
syntax would collide with normal tuple `(a, b)` values. 

```leo
let b = 0group; // the zero of the group

let a = 1group; // the group generator

let c = 2group; // 2 * the group generator

let d = (0, 1)group; // coordinate notation
```

### Recovery From Coordinates
Leo also supports recovery of group values from a single field value coordinate
* `(x, +)group` for given x and recovery with sign_high.
* `(x, -)group` for given x and recovery with sign_low.
* `(x, _)group` for given x and recovery with inferred y.
* `(+, y)group` for given y and recovery with sign_high.
* `(-, y)group` for given y and recovery with sign_low.
* `(_, y)group` for given y and recovery with inferred x.

Recovery with an inferred value will try both sign low and sign high recovery.

```leo
let a = (0, +)group;
let b = (_, 1)group;
```



## Addresses

Addresses are defined to enable compiler-optimized routines for parsing and operating over addresses. 
These semantics will be accompanied by a standard library in a future sprint.

```leo
function main(owner: address) {
    let sender = aleo1qnr4dkkvkgfqph0vzc3y6z2eu975wnpz2925ntjccd5cfqxtyu8sta57j8;
    let receiver: address = aleo1qnr4dkkvkgfqph0vzc3y6z2eu975wnpz2925ntjccd5cfqxtyu8sta57j8;
}
```