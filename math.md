# Math expressions

All [@Record](./classes#record) instances includes `assimptions` property which is set of assignments for different cases: initial assignment `start_`, ode assignment `ode_` and any number of switcher assignments.

The right hand side (RHS) of assignments is string of specific format [MathExpr](./classes#mathexpr).

Expression may include: numbers, identifiers, operators, functions, parentheses.

## Numbers

All numbers in "math expression" are double. You can use any form of number: `0.001`, `1e-3`, `1E-3`, `1.0e-3`, etc.

## Identifiers

The identifiers inside expressions should be interpreted as references to the values of `Record` or `Const` instances. 
The math expression `p1 .= 1*y` implies there is constant or record with the id `y`.

## Operators

There are 5 operators in MathExpr: `+`, `-`, `*`, `/`, `^`.

## Functions

Additional functions can be added by user. See [@FunctionDefinition](./classes#functiondefinition)

### Arithmetic function list

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
| log(x, base) | The logarithm of a value with base. |
| log10(x) | The 10-base logarithm of a value. |
| log2(x) | The 2-base of a value. |
| multiply(x, y)| Multiply two or more values, x * y. |
| pow(x, y)	| The power of x to y, x ^ y. |
| sign(x) | Compute the sign of a value. |
| sqrt(x) | Calculate the square root of a value. |
| square(x)| Compute the square of a value, x * x. |
| subtract(x, y) | Subtract two values, x - y. |
| max(x, y) | Maximum from two numbers |
| max(x, y, z) | Maximum from 3 numbers |
| min(x, y) | Minimum from two numbers |
| min(x, y, z) | Minimum from 3 numbers |
| ifg(x, y1, y2) | If grater, if x>0 than y1 else y2 |
| ife(x, y1, y2) | If equal, if x==0 than y1 else y2 |
| ifge(x, y1, y2) | If grater or equal, if x>=0 than y1 else y2 |

## Trigonometric functions

acos(x), acot(x), acsc(x), asec(x), asin(x),
atan(x), cos(x), cot(x), csc(x), sec(x), 
sin(x), tan(x)

## Hyperbolic fucntions

acosh(x), acoth(x), acsch(x), asech(x), asinh(x),
atanh(x), cosh(x), coth(x), csch(x), sech(x),
sinh(x), tanh(x)
