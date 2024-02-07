# Math expressions

All [@Record](./classes#record) instances (Species, Process, etc.) include `assignments` property which is set of assignments for different scopes: initial assignment `start_`, rule assignment `ode_` and any number of switcher assignments. The right hand side (RHS) of assignments is string of the specific format [MathExpr](./classes#mathexpr).

[@CSwitcher](./classes#cswitcher) includes [MathExpr](./classes#mathexpr) inside property `trigger`.

The `trigger` property of [@DSwitcher](./classes#dswitcher) and [StopSwitcher](./classes#stopswitcher) also include [MathExpr](./classes#mathexpr) but this expression must return boolean result.

`MathExpr` may include: numbers, identifiers, operators, functions, parentheses.

## Numbers

All numbers in "math expression" are double. You can use any form of number: `0.001`, `1e-3`, `1E-3`, `1.0e-3`, etc.

The another possible values of double are:
- `Infinity` which means infinite value.
- `-Infinity` which negative infinite value.
- `NaN` which means infinite or negative infinite, i.e. `0/0`.

## Boolean values

Boolean constants: `true`, `false`. It is also possible to use `1` and `0` as a boolean values.

## Pre defined constants

There is a set of identifiers which can be used in `MathExpr` but they are not components of platform.
- `e` is the Euler's number, i.e. `exp(0)`.
- `pi` is &pi;=3.141592... number.

## Identifiers

The identifiers inside expressions should be interpreted as references to the values of `Record` or `Const` instances of the same namespace. 
The math expression `p1 .= 1*y` implies there is constant or record with the id `y`.

## Operators

There are 5 numeric operators in MathExpr:
`+`, `-`, `*`, `/`, `^`.

There are 6 comparison operators in MathExpr:
`>`, `>=`, `<`, `<=`, `==`, `!=`.

There are 4 boolean operators in MathExpr:
`and`, `or`, `xor`, `not`.

Heta support ternary (conditional) operator in format: `b1 < b2 ? x : y`.
Here the first operand `b1 < b2` is the boolean expression, then an expression to execute if the condition is `true`, and finally the expression to execute if the condition is `false`.

## List of functions

### Arithmetic functions

| id | explanation |
|----|-------------|
| abs(x) | The absolute value of a number.|
| add(x, y) | Add two or more values, x + y.|
| ceil(x) | Round a value towards plus. |
| cube(x) | The cube of a value, x * x * x. |
| divide(x, y)| Divide two values, x / y. |
| exp(x) | The exponent of a value. |
| floor(x) | Round a value towards minus. |
| ln(x) | Natural logarithm of a value |
| log(x) | Natural logarithm of a value |
| logbase(base, x) | The logarithm of a value with base. |
| log10(x) | The 10-base logarithm of a value. |
| log2(x) | The 2-base of a value. |
| multiply(x, y)| Multiply two or more values, x * y. |
| pow(x, y)	| The power of x to y, x ^ y. |
| sign(x) | Compute the sign of a value. |
| sqrt(x) | Calculate the square root of a value. |
| nthRoot(x) | 2d root of x |
| nthRoot(x, n) | n-th root of x |
| square(x)| Compute the square of a value, x * x. |
| subtract(x, y) | Subtract two values, x - y. |
| max(x, y) | Maximum from two numbers |
| max(x, y, z) | Maximum from 3 numbers |
| min(x, y) | Minimum from two numbers |
| min(x, y, z) | Minimum from 3 numbers |
| factorial(n) | Factorial of number |
| ifgt(x, y, z1, z2) | Equivalent to : x > y ? z1 : z2 |
| ifge(x, y, z1, z2) | Equivalent to : x >= y ? z1 : z2 |
| iflt(x, y, z1, z2) | Equivalent to : x < y ? z1 : z2 |
| ifle(x, y, z1, z2) | Equivalent to : x <= y ? z1 : z2 |
| ifeq(x, y, z1, z2) | Equivalent to : x == y ? z1 : z2 |

### Trigonometric functions

acos(x), acot(x), acsc(x), asec(x), asin(x),
atan(x), cos(x), cot(x), csc(x), sec(x), 
sin(x), tan(x)

### Hyperbolic functions

acosh(x), acoth(x), acsch(x), asech(x), asinh(x),
atanh(x), cosh(x), coth(x), csch(x), sech(x),
sinh(x), tanh(x)


### piecewise function (experimental)

`piecewise` is a special function which can be used to switch values based on conditions.
It can be applied as an extension of the ternary operator or `ifgt`-like functions for more than one condition.

The `piecewise` function can have several arguments with the following meaning.

```heta
y1 := piecewise(value1, cond1, value2, cond2, ..., otherwise)
```

`value1` is returned if `cond1` is true, `value2` is returned if `cond2` is true, etc.

If neither conditions is true, than it returns `otherwise` value.
`otherwise` value can be skipped. In that case the function returns `NaN`.

Each condition argument must be of boolean type: comparison operator, boolean value, etc.

__Example__

```heta
// 0 -> human, 1 -> monkey, 2 -> rat, default -> mouse
animal @Const = 999;
body_weight @Record .= piecewise(BW_human, animal==0, BW_monkey, animal==1, BW_rat, animal==2, BW_mouse);

// the body_weight value will be equal to BW_mouse
```
