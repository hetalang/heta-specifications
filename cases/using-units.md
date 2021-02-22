# Using units

Usage of core units in Heta code, see [units page](../units)

```heta
k1 @Const = 1.1; // no units stated

// Heta includes list of defaut units which can be used without declaration
comp1 @Compartment .= 0.1 { units: liter };

// Units can be complex (UnitExpr)
s1 @Species .= 1e-9 { units: mole/liter };

// Units can be in long format with multipliers
s2 @Species .= 1e-1 {
    units: [
        { kind: mole, multiplier: 1e-6 },
        { kind: liter, exponent: -1 }
    ]
};
```

User defined units.
```heta
/* user can create units using #defineUnit action */

// create unit alias
L #defineUnit {
    units: liter
};
// use new unit in a usual way
comp1 @Compartment .= 0.1 { units: L };

// creating complex unit
M #defineUnit {
    units: mole/liter
};

// creating new unit with multipliers
uM #defineUnit {
    units: [
        { kind: mole, multiplier: 1e-6 },
        { kind: liter, exponent: -1 }
    ]
};
s1 @Species .= 0 { units: uM };
```
