# Heta classes

Heta classes describes hierarchical types of Heta components.

**Class list**

- [Component](#component)
- [_Size](#_size)
- [UnitDef](#unitdef)
- [Const](#const)
- [Record](#record)
- [Process](#process)
- [Compartment](#compartment)
- [Species](#species)
- [Reaction](#reaction)
- [_Switcher](#_switcher)
- [TimeSwitcher](#timeswitcher)
- [DSwitcher](#dswitcher)
- [CSwitcher](#cswitcher) (experimental)
- [Dose](#dose)
- [SimpleTask](#simpletask) (experimental)

**Simple types**

- [ID](#id)
- [Filepath](#filepath)
- [UnitsExpr](#unitsexpr)
- [MathExpr](#mathexpr)
- [ProcessExpr](#processexpr)

## UML diagram

[![Classes UML](https://raw.githubusercontent.com/hetalang/heta-specifications/v0.x/heta.uml.png)](https://raw.githubusercontent.com/hetalang/heta-specifications/v0.x/heta.uml.png ':ignore')

## About Heta classes

1. Classes define the meaning of platform (model) part. A class declares properties of their instances, their types checking rules and the default values. Trying to include the undeclarated properties is not an error but the property will be ignored.
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

## UnitDef

**Parent:** [Component](#Component)

This is class for implementation of a definition of unit to use it inside [UnitsExpr](#unitsexpr). Units property can be undefined which means this is basic unit and cannot be expressed in terms of other units like meter, second, etc.

*no specific properties*

### Example
```heta
kDa @UnitDef { units: [
    { kind: g, multiplier: 1e3, exponent: 1 },
    { kind: mole, exponent: -1 }
]};
Da @UnitDef { units: g/mole };
```

### UnitsComponent

**Parent:** no

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| kind | ID | true | | UnitDef | The reference id to UnitDef.|
| multiplier | numeric | true | 1 | | Multiplier |
| exponent | numeric | true | 1 | | Power |

## Const

**Parent:** [_Size](#_size)

This is class for numerical values which do not change in simulation time. This represents modeling inputs.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| num | number | true | | | Numerical value or starting value for identification. |
| free | boolean | | | | If true the constant is unknown and can be evaluated based on experimental data.|
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of num property |

### Example
```heta
''' Absorption constant describing transport from **GUT** to **BLOOD** '''
kabs @Const 'Constant of absorption' {
    tags: ['pk', 'human'],
    aux: { references: { 
            pmid: [ 11111111, 22222222 ], 
            wiki: ['Pharmacokinetics#Compartmental_analysis'] 
    }}
};
kabs = 1.4e-6 { free: true, units: 1/h };
```

## Record

**Parent:** [_Size](#_size)

Record instances describes the values (variables) which can change in time. Usually Record has the physical or biologycal meaning. The value changes their value by assignment at specific points (switchers) or by `Process` instances.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| assignments | Dictionary{ID, MathExpr} | | | | Dictionary of assignments where key is switcher id and value describes the MathExpr |
| boundary | boolean | | | | If `true` the value describing `Record` cannot be changed by `@Process` instances. |
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
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of process rate |

### Example

```heta
pr1 @Process { actors: p1 => 2*p2 };

/* equivalent to 
pr1 @Process { actors: [
    { target: p1, stoichiometry: -1 },
    { target: p2, stoichiometry: 2 }
]};
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

Run reassignment of records for some contions.
Switcher id can be used as key in assignments dictionary.

*no specific properties*

## TimeSwitcher

**Parent:** [_Switcher](#_switcher)

`TimeSwitcher` decribes the switching asignment depending only on time. If `period` property > 0 the switching will be periodical. `repeatCount` and `stop` work together to estimate the latest switching.

Number of switching is based on the following rules:

- `if (repeatCount < 0 OR stop < start) return 0`;
- `if (period <= 0 OR 0 <= repeatCount < 1 OR 0 <= (stop-start)/period < 1) return 1`;
- `if (period > 0 && 1 <= repeatCount && 1 <= (stop-start)/period) return min(repeatCount, (stop-start)/period) + 1`;
- `if (period > 0 && repeatCount === Infinity/undefined && stop === Infinity/undefined) return Infinity`;

Based on rules to describe **one** switching use only `start` property. To use infinite repeat use `start` and `period` properties. To use several repeats add `repeatCount` or `stop`.

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
    repeatCount: 4
};

sw2 @TimeSwitcher {
    start: starting_time, // ref to Const
    period: period,
    stop: stop_time
};
```

## DSwitcher

*This is experimental feature*

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

*This is experimental feature*

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

## Dose

**Parent:** [_Size](#_size)

`Dose` class describe non-periodic and periodic event which add some value to Species.
It substites partially usage of `TimeSwitcher` and was included to support export to Simbiology and Mrgsolve.
`amount` property is always in amount units, not concentration. 

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| start | Number/ID | | 0 | - / `Const` | time to run the first switch |
| period | Number/ID | | | - / `Const` | period of repeated switching |
| repeatCount | Number/ID | | | - / `Const` | number of dose addition after the first dose |
| target | ID | true | | Species | target species to add the dose |
| amount | Number/ID | true | | - / `Const` | amount which should be added to the reffered species |
| units | UnitsExpr *or* [UnitsComponent](#unitscomponent)[] | | | | units of `amount` |

### Example

```heta
dose1 @Dose {
  target: A,
  amount: 100,
  start: 0,
  period: 12,
  repeatCount: 4
};
```

## SimpleTask

**Parent:** [Component](#component)

*This is experimental and not supported in most exports*

`@SimpleTask` describes settings for simulation of the model based on particular namespace.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| subtasks | Subtask[] | true | | | Array of elements of type {saveat: \<number[]\>, output: \<string[]\>} |
| tspan | number[] | | [0, 100] | | start and end point of simulation |
| reassign | Dictionary | | {} | `Const` | reassignments of constants for the simulation |
| solver.alg | string | | lsode | | method |
| solver.reltol | number | | 1e-6 | | relative tolerance |
| solver.abstol | number | | 1e-6 | | absolute tolerance |
| solver.maxiters | number | | 1e5 | | maximum number of iteretions |
| solver.dt | number | | 0 | | dt value |
| solver.dtmin | number | | 0 | | dtmin value |
| solver.dtmax | number | | 0 | | dtmax value |
| solver.tstops | number[] | | [] | |time points for force stop |

### Example

```heta
st1 @SimpleTask {
    subtasks: [
        { saveat: [0, 10, 100], output: [A, B, C] }
    ],
    tspan: [0, 120],
    reassign: { p1: 10, p2: 0 },
    solver: {
        alg: lsoda,
        abstol: 1e-9
    }
};
```

## ID

ID describes the string type which is used for idexing Heta components. ID type can be used for namespaces, identifiers and references.

The rules for ID is the following:

1. First symbol should be letter or underscore.
1. Second and other elements should be letter, number or underscore.
1. Deprication: the last symbol should not be underscore.

### Example

**Corect ID:** `"x"`, `"x12"`, `"x_12"`, `"_12"`, `"x___12"`

**Wrong ID:** `"12x"`, `"x-12"`, `"x 9"`

**Depricated ID:** `"_"`, `"x12_"`

## Filepath

String representing relative or absolute path.

### Examples

`output`, `./output`, `../output`, `Y:/my-platform/src/module1/model.heta`

## UnitsExpr

UnitsExpr strings represent the complex units combined from the predefined unit IDs. Available operatators: `*`, `/`, `^`, `1/`.

See more detailes on [units](units) page.

### Example

**Correct UnitsExpr**: `mg`, `g/mole`, `1/h`, `kg/m2`, `kg/m^2`

**Wrong UnitsExpr:** `g/(mole*L)`, `5*g`, `km + kg`

## MathExpr

Mathematical expressions in string format. Available operators: `+`, `-`, `*`, `/`, `^`. See detailes in [Math expressions](./math)

## Example

**Correct MathExpr:** `x*y*pow(x,y)`

## ProcessExpr

ProcessExpr is string representing process stoichiometry. The "arrow" syntax (`->`, `<->`, `=>`, `<=>`) devided two parts: influx (left) and outflux (right). Plus symbol divide two or more actors. Stoichiometry coefficients are shown by numbers before reference. Asterix symbol is optional.

### Example

**Correct ProcessExpr**: `A->B`, `A =>`, `2A <=> 3*B + C`, `2 A <=> 3 * B + C`.
