# SBML conversion

This document specifies the conversion of an SBML module into Heta declarations.
SBML modules of levels 2 and 3 are supported. An SBML module is included with
`type: sbml`; see [Modules](modules#sbml-module).

```heta
#include { source: ./model.xml, type: sbml };
```

## 1. General rules

1. The compiler shall read the supported contents of the SBML document and create
   the corresponding Heta declarations.

1. The compiler shall preserve the meaning of supported model contents where this
   is possible. It is not required to preserve the original XML, MathML text,
   identifiers of generated declarations, or the original arrangement of contents.

1. The compiler may replace an SBML construct with one or more Heta declarations
   when this is required to represent its meaning.

1. A supported SBML expression shall be converted to Heta [MathExpr](math).
   Function names and expression forms may be changed to the Heta form.

1. If the document contains an unsupported construct, the compiler shall report
   an import error. It shall not silently omit that construct.

Conversion is not required to be reversible. In particular, an SBML document
exported after import may have a different structure while describing the same
model.

## 2. Conversion of model contents

The following table defines the conversion of supported SBML contents.

| SBML content | Heta result | Conversion rule |
|---|---|---|
| Unit definition | Unit declaration | The unit definition is converted to a Heta unit expression. |
| Model `timeUnits` | Units of `t` | The units of the Heta time symbol `t` are set from `timeUnits`. |
| Function definition | `#defineFunction` | Lambda arguments become `arguments`; its body becomes a Heta expression. |
| Compartment | `@Compartment` | `size`, when present, becomes `start_`; compartment units are converted to Heta units. `compartmentType` is stored in `tags`. |
| Species | `@Species` | Initial amount or concentration becomes `start_`; the compartment reference and units are converted. |
| Parameter | `@Const` or `@Record` | A constant parameter becomes `@Const`. A changing parameter, or a parameter with an initial assignment, becomes `@Record`. |
| Reaction | `@Reaction` | The kinetic law becomes the reaction expression. Reactants and products become actors; modifiers become reaction modifiers. |
| Initial assignment | `start_` assignment | The assignment is applied to its target as the initial value. |
| Assignment rule | `ode_` assignment | The rule expression is assigned to `ode_` of its target. |
| Rate rule | `@Process` | A generated process changes the rule target at the specified rate. The target is made non-boundary. |
| Event | `@DSwitcher` | Event assignments become assignments associated with the switcher. An import option may use `@CSwitcher` instead. |

### 2.1 Units

SBML base units that are valid in SBML shall be mapped to the corresponding Heta
base units. Custom unit definitions shall be converted to Heta unit expressions.

For a species represented as a concentration, its amount units shall be divided
by the units of its compartment. A species is treated as an amount when
`hasOnlySubstanceUnits` is true or when its compartment has zero dimensions.

### 2.2 Compartments and species

The values of `boundaryCondition` and `constant` shall be used to set the Heta
`boundary` property of a species. The `initialAmount` and
`initialConcentration` attributes are normalized to one `start_` expression.

### 2.3 Parameters and reactions

For SBML Level 2, an omitted `constant` attribute of a parameter shall be treated
as `true`.

Local parameters of a reaction shall be created as generated Heta constants. Their
identifiers may be changed so that they do not conflict with other declarations.
The stoichiometry of a reactant shall be negative and the stoichiometry of a
product shall be positive in the generated reaction actors.

### 2.4 Rules and events

An `algebraicRule` is not supported. An event with a `delay` is not supported.
When an event is imported as a `@CSwitcher`, equality and inequality conditions
are not supported; boolean conditions are converted to a numeric trigger.

## 3. Conversion of mathematical expressions

MathML expressions shall be converted to Heta expression syntax. The following
table lists direct conversions.

| SBML MathML element | Heta expression |
|---|---|
| `plus` | `a + b + ...` |
| `minus` | `-a` or `a - b` |
| `times` | `a * b * ...` |
| `divide` | `a / b` |
| `power` | `pow(a, b)` |
| `root` without degree | `sqrt(x)` |
| `root` with degree `n` | `pow(x, 1.0 / n)` |
| `ln` | `ln(x)` |
| `ceiling` | `ceil(x)` |
| `gt`, `geq`, `lt`, `leq` | `>`, `>=`, `<`, `<=` |
| `eq`, `neq` | `==`, `!=` |
| `and`, `or`, `xor`, `not` | `and`, `or`, `xor`, `not` |
| SBML time `csymbol` | `t` |
| SBML Avogadro `csymbol` | `6.02214076e23` |
| `exponentiale`, `pi` | `exponentiale`, `pi` |
| `infinity`, `notanumber` | `Infinity`, `NaN` |

An application of a named SBML function shall become an ordinary Heta function
call. Thus, a call of `f` with arguments becomes `f(...)`, whether `f` is a
built-in function or an SBML function definition.

### 3.1 Logarithms

The `log` element shall be converted according to its base.

| SBML form | Heta expression |
|---|---|
| `log(x)` without `logbase` | `log10(x)` |
| `log(x)` with base 2 | `log2(x)` |
| `log(x)` with another base `b` | `logbase(x, b)` |

### 3.2 Conditional expressions

An SBML `piecewise` expression shall be converted as follows.

| SBML form | Heta expression |
|---|---|
| One `piece` and `otherwise` | `condition ? value : otherwise` |
| Any other supported form | `piecewise(value1, condition1, value2, condition2, ..., otherwise)` |

The generated conditions shall remain associated with the values that precede
them. If no `otherwise` value is present, the resulting `piecewise` call may omit
its last argument, as defined in [Math expressions](math#piecewise-function).

### 3.3 Function names

Some standard SBML function names shall be normalized to names used in Heta.

| SBML name | Heta name |
|---|---|
| `arcsin`, `arccos`, `arctan` | `asin`, `acos`, `atan` |
| `arccot`, `arccsc`, `arcsec` | `acot`, `acsc`, `asec` |
| `arcsinh`, `arccosh`, `arctanh` | `asinh`, `acosh`, `atanh` |
| `arccoth`, `arcsech`, `arccsch` | `acoth`, `asech`, `acsch` |

## 4. Unsupported contents

The compiler shall report an import error for the following contents:

| SBML content | Restriction |
|---|---|
| `algebraicRule` | Not supported. |
| Reaction with `fast="true"` | Not supported. |
| `stoichiometryMath` | Not supported. |
| `delay` symbol in an expression | Not supported. |
| Event `delay` | Not supported. |
| Unknown `csymbol` URL | Not supported. |

Other SBML contents not covered by this document are not required to be
converted.

