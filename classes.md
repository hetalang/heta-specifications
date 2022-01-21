# Heta classes

Heta classes describes hierarchical types of the Heta components.
Components are parts of the model which refer to a particular namespace. 

**Class list**

- [Component](#component)
- [_Size](#_size)
- [TimeScale](#timescale)
- [Const](#const)
- [Record](#record)
- [Process](#process)
- [Compartment](#compartment)
- [Species](#species)
- [Reaction](#reaction)
- [_Switcher](#_switcher)
- [TimeSwitcher](#timeswitcher)
- [DSwitcher](#dswitcher)
- [CSwitcher](#cswitcher)
- [StopSwitcher](#stopswitcher) (experimental)

## UML diagram

[![Classes UML](https://raw.githubusercontent.com/hetalang/heta-specifications/master/heta.uml.png)](https://raw.githubusercontent.com/hetalang/heta-specifications/master/heta.uml.png ':ignore')

## About Heta classes

1. Classes define the meaning of platform (model) components. A class declares properties of their instances, their types, checking rules and the default values.

1. Trying to include the undeclarated properties is not an error but the property will be ignored.
    ```heta
    pr1 @Process { compartment: comp1 };
    ```
    will be interpreted as 
    ```heta
    pr1 @Process { };
    ```
    because `compartment` property is not declared for `Process` class.

1. There are some specific checking rules in classes, e.g. required properties. They will be checked only after all components from the code is loaded, see [compilation](./compilation) steps. For example, `Const` class has required numeric property `num`:
    ```heta
    k1 @Const; // thes case doesn't throw error
    k1 { num: 1 };    // because num property is set here
    ```

1. Some properties of the components are the references to other components, furthermore they should be referenced to instances of specific classes. The checking of references will be after loading all the code. For example a `Species` instance must have the property `compartment` which is the reference to a `Compartment` instance.
    ```heta
    S @Species { compartment: comp1 } .= 0;
    ```
    The code above throws an error because there is no element with id `comp1`.
    ```heta
    S @Species { compartment: comp1 } .= 0;
    comp1 @Compartment .= 1;
    ```
    The code above is OK because for the moment of reference checking the compartment has already been loaded.

1. `Component` is the top class, i.e. all other Heta components inherits from `Component`. 

## Component

**Parent:** *no*

This is the top class for all Heta components. Includes properties for component's annotation.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| title | string | | | | Additional non-unique human readable identifier for a component. |
| notes | string | | | | Arbitrary text for component annotation. It can potentially support markdown for text decoration. |
| tags | string[] | | [ ] | | Array of strings tagging components. Can be used for grouping components. |
| aux | object | | { } | | User defined auxilary structures { key: value pair } with any complexity. This can be used to store additional information and annotation. |

## _Size

**Parent:** [Component](#component)

This is a class for all components which can have units. See more detailes on [units](units) page.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | String describing components of complex units or array of complex unit components. |

### UnitsComponent

**Parent:** no

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| kind | ID | true | | UnitDef | The reference id to UnitDef.|
| multiplier | numeric | true | 1 | | Multiplier |
| exponent | numeric | true | 1 | | Power |

## TimeScale

**Parent:** [_Size](#_size)

This component type is used if someone need to include an alternative time variable.
For example the base time unit is `second` but it is required to add `tih` which means time in hours. It can be expressed via the base time `t`.

```tih = 1 / 3600 * t + 0```

The general form of the expression:

```new_time = slope * t + intercept```

Base time `t` is also a component of the `@TimeScale` class but it is inserted as default after initialization of a namespace. It cannot be deleted but a user can set units of it.

```heta
t {units: second};
```

In many cases the usage of `@TimeScale` is not required because it can be substituted by `@Record` component, see the examples.


| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| slope | number | | 1 | | Fixed value of multiplier for calculation of the value`. |
| intercept | number | | 0 | | Fixed value to add to the time variable. |
| output | boolean | | | | If `true` the value will be available for output: plots, tables, etc. |

__Examples__

```heta
tih @TimeScale {slope: 2.78e-4, output: true, units: hour};
```

`@Record` with the same meaning

```heta
tih @Record {output: true, units: hour} := 2.78e-4 * t;
```

## Const

**Parent:** [_Size](#_size)

This is a class for numerical values which do not change in simulation time. This represents numeric modeling inputs.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| num | number | true | | | Exact value or starting value for identification. |
| free | boolean | | | | If `true` the constant is unknown and can be evaluated based on experimental data.|
| scale | string | | direct | | To clarify the scale for solving of optimization problem. Possible values: `direct`, `log`, `logit`. If not set this is the same as direct. If it is `log` then the `num` property should be > . If it is `logit` the `num` propery should be `0<num<1`|
| lower | number | | | | To set the lowest possible value. `num` value should be larger or equal to `lower` |
| upper | number | | | | To set the largest possible value. `num` value should be lower or equal to then `upper` |
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of num property |

### Examples

```heta
''' Absorption constant describing transport from **GUT** to **BLOOD** '''
kabs @Const 'Constant of absorption' {
    tags: ['pk', 'human'],
    aux: { references: { 
            pmid: [ 11111111, 22222222 ], 
            wiki: ['Pharmacokinetics#Compartmental_analysis'] 
    }}
};
kabs = 1.4e-6 { free: true, scale: log, lower: 1e-6, upper: 1e6, units: 1/h };
```

## Record

**Parent:** [_Size](#_size)

Record instances describes the values (variables) which can change in time. Usually Record has the physical or biologycal meaning. The value changes their value by assignment at specific points (switchers) or by `Process` instances.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| assignments | Dictionary{ID, MathExpr} | | | | Dictionary of assignments where key is switcher id and value describes the MathExpr |
| boundary | boolean | | | | If `true` the value describing `Record` cannot be changed by `@Process` instances. |
| output | boolean | | | | If `true` the value will be available for output: plots, tables, etc. |
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of assignments part |

### Example

```heta
p1 @Record { boundary: true, units: kg/L };
p1 .= x*y;
p1 [sw1]= 0;

// equivalent to this code
/*
p1 {
    class: Record.
    boundary: true,
    output: false,
    units: kg/L,
    assignments: {
        start_: x*y,
        sw: 0
    }
};
*/
```

## Process

**Parent:** [Record](#record)

Process instances changes the other `Record` instances indirectly through the rate of Record change. Process is like flux that increase or decrease values over time.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| actors | ProcessExpr/Actor[] | | [] | | [ProcessExpr](#processexpr) or array of objects of format.  { target: \<ID\>, stoichiometry: \<number\> }. The rules for dynamic components which describes how they will be included to ODE including stoichiometry. |
| reversible | boolean | | `true` | | This property usually doesn't cause the changes in a dynamic system. It describes if the `@Process` is reversible. It has been included to support SBML's and Simbio's the `reversible` attribute. It can also be set in `ProcessExpr` where `<->` and `<=>` stand for the reversible process.|
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of process rate |

### Example

```heta
pr1 @Process { actors: p1 => 2*p2 };

/* equivalent to 
pr1 @Process {
    actors: [
        { target: p1, stoichiometry: -1 },
        { target: p2, stoichiometry: 2 }
    ],
    reversible: false
};
*/
```

### Effector

**Parent:** *none*

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| target | ID | true | | `Record` | Reference to record |

### Actor

**Parent:** [Effector](#effector)

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| stoichiometry| number | | 1 |  | Stoichiometry of flux. |

## Compartment

**Parent:** [Record](#record)

`Compartment` is class describing volumes where `Species` instances are located. The variable means the volume size.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of compartment volume |

### Example
```heta
comp1 @Compartment = 5.3 { units: L };
```

## Species

**Parent:** [Record](#record)

`Species` is class describing molecules or other items in some location (volume). The variable value can mean amount of molecules or concentration depending on `isAmount` property.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| compartment | ID | true | | `Compartment` | Reference to compartment id where the molecule is located. |
| isAmount | boolean | | | | If `true` the value is an amount not concentration. |
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of species |

### Example

```heta
S @Species {
    compartment: comp1,
    isAmount: false
};
S .= 10;
```

## Reaction

**Parent:** [Process](#process)

The same as Process, but all target references should be Species. The numerical value must be expressed in amount rates per time unit.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| actors | ProcessExpr/Reactant[] | | [] | | [ProcessExpr](#processexpr) or array of objects in format { target: \<ID\>, stoichiometry: \<number\> where target is the reference to `Species`} |
| modifiers | ID[]/Modifiers[]  | | [] | | List of references to `Species`. Array of references or array of objects in format {target: \<ID\>}. set of components which are not included to ODE but can affect to it. |
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of reaction rate |

### Example

```heta
r1 @Reaction {
    actors: S -> P,
    modifiers: [ E ]
};
r1 := k1*A*comp1;
```

## _Switcher

**Parent:** [Component](#component)

Switcher instances state the assignment at specific points and conditions when to start the assignment. 
id of switcher introduces a new key in assignments dictionary, see [Record](#record).

| property | type | required | default | ref | description |
| ---------|------|----------|---------|-----|-------------|
| active | Boolean | | `true` | | If it is `false` the switcher will be off. A user can turn it off/on in simulation scenarios. |
| atStart | Boolean | | | | `true` if we should check the condition at `start_` scope. |

## TimeSwitcher

**Parent:** [_Switcher](#_switcher)

`TimeSwitcher` describes the switching assignment depending only on time. If `period` property > 0 the switching will be periodical.

Number of switching is based on the following rules:

- `if (stop < start) return 0`;
- `if (period <= 0 OR 0 <= (stop-start)/period < 1) return 1`;
- `if (period > 0 AND 1 <= (stop-start)/period) return (stop-start)/period + 1`;
- `if (period > 0 AND (stop === Infinity OR stop === undefined)) return Infinity`;

Based on rules to describe **one** switching use only `start` property. To use infinite repeat use `start` and `period` properties. To use several repeats add the `stop`property.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| start | Number/ID | | 0 | - / `Const` | time to run the first switch |
| period | Number/ID | | | - / `Const` | period of repeated switching |
| stop | Number/ID | | | - / `Const` | time to turn off switch |

### Example

```heta
sw1 @TimeSwitcher {
    start: 0,
    period: 24,
    active: true
};

sw2 @TimeSwitcher {
    start: starting_time, // ref to Const
    period: period,
    stop: stop_time,
    active: false
};
```

## DSwitcher

*This is an experimental feature*

**Parent:** [_Switcher](#_switcher)

`DSwitcher` (discrete switcher) switches based on boolean expression. The switcher is triggered when the value switches from false to true.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| trigger | MathExpr | true | | | Boolean MathExpr to turn on events. |

### Example

```heta
sw2 @DSwitcher {
    trigger: (x > 1) and (y != x)
};
```

## CSwitcher

**Parent:** [_Switcher](#_switcher)

`CSwitcher` (continuous switcher) switches based on expression in `trigger` property. The switcher is triggered when the value of the record hits 0 changing from negative to positive value.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| trigger | MathExpr | true | | | Expression to estimate the moment of switching |

### Example

```heta
sw2 @CSwitcher {
    trigger: x * y
};
```

## StopSwitcher

*This is an experimental feature*

**Parent:** [_Switcher](#_switcher)

`StopSwitcher` stops ODE simulations according to the boolean expression in `trigger`.
Unlike other `_Switcher` instances it does not creates a new assignment scope.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| trigger | MathExpr | true | | | Expression to estimate the ODE stop condition |

### Example

```heta
ss1 @StopSwitcher {
    trigger: p1 > p2
};
```
