# Units

All `@_Size` instances can have optional property `units`.

*See the examples in [using units](cases/using-units) cases.*

## Core units

There is a list of units (core units) that is included as default in Heta language. This list is based on base units in [SBML specifications](http://sbml.org/Documents/Specifications). The list is the following:

| |
| --- |
mole, litre, second, kilogram, katal,
item, joule, metre, dimensionless, watt,
volt, ampere, newton, becquerel, candela,
coulomb, farad, gram, gray, henry,
hertz, kelvin, lumen, lux, ohm,
pascal, radian, siemens, sievert, steradian,
tesla, weber, year, day, hour,
minute

## UnitsExpr

The complex units, i.e. units which can be expresses from combination of the pre-defined units like `mole/litre`, `m^2`, etc can be expressed in form of `UnitsExpr` (units expression) string format.

There is some options to use dimensionless units: 

- direct use of `dimensionless` base unit;
- create an synonym of it as declared in `qsp-units.heta` from __heta-compiler__;
- use 1 value instead of `dimensionless`.

Example 1:
```heta
s1 @Species { units: mole/litre };
```

Example 2:
```heta
k1 @Const { units: mole^2/litre^2/second };
```

Example 3:
```heta
k1 @Const { units: 1/second };
```

Example 4:
```heta
x1 @Const { units: 1 }; // dimensionless
```

## UnitsExpr with multipliers

The multiplier of units is an optional feature but if you use you must cover the component with round brackets.

Example:
```heta
s2 @Species { units: (1e-9 mole)/litre } .= 10;
```

## UnitsComponent Array

An alternative way to set complex units is to express it in format of `UnitsComponent[]`. Using `multiplier` property one can add multiplier to the base unit.

Example:
```heta
s3 @Species {
    units: [
        { kind: mole, multiplier: 1e-9 },
        { kind: litre, exponent: -1 }
    ]
};
```

## User defined units

User can create his own list of units and use it instead of core units. It can be done creating `#defineUnit` action.
The `units` property here can be expressed by `UnitsExpr` or `UnitsComponent[]`

Example:
```heta
pM #defineUnit {
    units: [
        { kind: mole, multiplier: 1e-12 },
        { kind: litre, multiplier: 1, exponent: -1 }
    ]
};

s1 @Species { units: pM };
```

## Units checking in compilation

Depending on a compiler the units may be checked for consistency.

See more information in [Compilation](./compilation) chapter.


