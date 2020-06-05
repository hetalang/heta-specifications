# Units

All `@_Size` instances can have optional property `units`.

*See the examples in [using units](cases/using-units) cases.*

## Core units

There is a list of units (core units) that is included as default in Heta language. This list is based on base units in [SBML specifications](http://sbml.org/Documents/Specifications). The list is the following:

mole, litre, second, kilogram, katal,
item, joule, metre, dimensionless, watt,
volt, ampere, newton, becquerel, candela,
coulomb, farad, gram, gray, henry,
hertz, kelvin, lumen, lux, ohm,
pascal, radian, siemens, sievert, steradian,
tesla, weber, year, day, hour,
minute

## UnitsExpr

Complex units, i.e. units that can be expresses from combination of other units like `mole/liter`, `m^2`, etc can be expressed in form of `UnitsExpr` (units expression) string format.

Example:
```heta
s1 @Species { units: mole/liter };
```

## UnitsComponent Array

Some complex units should be expressed in format of `UnitsComponent[]`. Using `multiplier` property one can add multiplier to the base unit.

Example:
```heta
s2 @Species {
    units: [
        { kind: mole, multiplier: 1e-9 },
        { kind: liter, exponent: -1 }
    ]
};
```

## User defined units

User can create his own list of units and use it instead of core units. It can be done creating `UnitsDef` components.

Example:
```heta
pM @UnitsDef {
    units: [
        { kind: mole, multiplier: 1e-12 },
        { kind: liter, multiplier: 1, exponent: -1 }
    ]
};

s1 @Species { units: pM };
```

## Units checking in compilation

The current version of Heta specifications do not clarify how to check units in platform.
All checking should be performed by user.
