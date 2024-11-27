
# Units

This document specifies the syntax, core functionality, and extensions for defining and working with units in the Heta language. The `units` property is available for components of the following classes: `@Const`, `@Record`, `@Species`, `@Reaction`, `@TimeScale`, and any other class referring to the `@_Size` parent class.

**Refer to examples in the [Using Units](cases/using-units) section for practical implementations.**

## 1. Core Units

The Heta language includes a default set of core units based on the [SBML specifications](http://sbml.org/Documents/Specifications). These core units are as follows:

| Core Units |
| --- |
mole, litre, second, kilogram, katal, item, joule, metre, dimensionless, watt, volt, ampere, newton, becquerel, candela, coulomb, farad, gram, gray, henry, hertz, kelvin, lumen, lux, ohm, pascal, radian, siemens, sievert, steradian, tesla, weber, year, day, hour, minute, avogadro

## 2. Units Expressions (UnitsExpr)

Complex units, formed by combining predefined units (e.g., `mole/litre`, `m^2`, `joule/second`), can be expressed using `UnitsExpr` string format.

This format is a set of declared units separated by `/` or `*` operators. The `^` operator is used to specify exponents.

**Examples:**
```heta
s1 @Species { units: mole/litre };
k1 @Const { units: mole^2/(litre)^2/second };
k2 @Const { units: 1/second };
x1 @Record { units: metre^2*joule/second };
```

Parentheses may be used to enclose a single unit for clarity or emphasis; however, they cannot be used to group multiple units together, and nesting of parentheses is not allowed.

**Examples:**
```heta
s1 @Species { units: (mole/litre) }; // error in syntax: grouping
x2 @Record { units: ((mole)*litre) }; // errror in syntax: nesting
```

## 3. UnitsExpr with Multipliers

You can specify a multiplier for units using number before the unit. When multiplier is used the parentheses are mandatory.

**Example:**
```heta
s2 @Species { units: (1e-9 mole)/litre } .= 10;
c1 @Compartment { units: (1e-9 metre)^3 };
```

## 4. UnitsComponent Array

As an alternative to `UnitsExpr`, complex units can be specified as an array of `UnitsComponent` objects. This format allows explicit specification of multipliers, exponents, and base units.

**Example:**
```heta
s3 @Species {
    units: [
        { kind: mole, multiplier: 1e-9 },
        { kind: litre, exponent: -1 }
    ]
};
```

## 5. User-Defined Units

Users can define custom units using the [`#defineUnit`](./actions?id=defineunit) action. Custom units can be specified in either `UnitsExpr` or `UnitsComponent[]` format.

**Example:**
```heta
pM #defineUnit {
    units: [
        { kind: mole, multiplier: 1e-12 },
        { kind: litre, multiplier: 1, exponent: -1 }
    ]
};
fM #defineUnit { units: (1e-15 mole)/litre };

s1 @Species { units: pM };
```

## 6. Dimensionless Units

Dimensionless units can be represented in the following ways:

1. **Direct Use:** Specify the `dimensionless` base unit.
2. **Literal Representation:** Use the symbol `1` instead of `dimensionless`.
3. **Synonym Declaration:** Define a synonym of `dimensionless` using the `#defineUnit` action.

**Examples:**
```heta
x1 @Const { units: dimensionless } = 1.1;

x2 @Const { units: 1 } = 2.2;

#defineUnit DL { units: dimensionless };
x3 @Const { units: DL } = 3.3;
```

## 7. Units Consistency Checking

During compilation, the Heta compiler may perform units consistency checks to validate compatibility and correctness of unit definitions. The availability and implementation of this feature depend on the compiler being used.

**For more information, refer to the [Compilation](./compilation) chapter.**

This example demonstrates a simple units inconsistency resulting in a compilation error.
```heta
c1 @Compartment { units: litre } .= 1;
s1 @Species { units: mole/litre, compartment: c1 } .= 10;
r1 @Reaction { units: mole/second } := k1 * s1;
k1 @Const { units: 1/second } = 1e-3;
```
The reaction rate `r1` is calculated using `k1` and `s1` and has derived units of `mole/litre/second`. But the stated units of `r1` is `mole/second` and incompatible. Correcting such mismatches ensures the model's mathematical and dimensional integrity.
