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

The complex units, i.e. units which can be expresses from combination of other units like `mole/litre`, `m^2`, etc can be expressed in form of `UnitsExpr` (units expression) string format.

Example:
```heta
s1 @Species { units: mole/litre };
```

## UnitsExpr with multipliers

In many cases to express the units the multiplier should be applied.

Example:
```heta
s2 @Species { units: (1e-9 mole)/litre } .= 10;
```

## UnitsComponent Array

Some complex units should be expressed in format of `UnitsComponent[]`. Using `multiplier` property one can add multiplier to the base unit.

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

User can create his own list of units and use it instead of core units. It can be done creating `UnitsDef` components.

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


