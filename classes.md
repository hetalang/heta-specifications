# Heta classes

Heta classes define the hierarchical types of components within the Heta modeling language. These components form the building blocks of a model, with each class specifying the properties, validation rules, and behaviors associated with its instances.

This document is intended for developers, modelers, and researchers who want to understand and utilize the Heta language for systems biology and quantitative systems pharmacology (QSP) modeling.

The purpose of this document is to provide a detailed specification of all Heta classes, their relationships, and practical usage examples. By understanding the structure and purpose of each class, users can effectively build, modify, and validate their Heta-based models.

Key concepts covered in this document include:
- **Hierarchical Class Structure:** All Heta components inherit properties and behaviors from the root `Component` class.
- **Property Validation:** Classes enforce specific rules for required properties, data types, and default values.
- **References:** Some classes require references to other components, which are checked during model compilation.
- **Examples and Use Cases:** Each class is accompanied by examples to illustrate its application.

## Class List

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

The Heta class structure is visualized in the UML diagram below. This diagram illustrates the relationships and inheritance hierarchy between the classes.

[![Classes UML](https://raw.githubusercontent.com/hetalang/heta-specifications/master/heta.uml.png)](https://raw.githubusercontent.com/hetalang/heta-specifications/master/heta.uml.png ':ignore')

## About Heta Classes

Heta classes define the meaning and behavior of model components. Each class specifies the properties that instances of the class can have, along with their data types, validation rules, and default values. These definitions provide a structured framework for building, validating, and using models.

Key aspects of Heta classes:

1. **Property Validation:**
   - Each class has a predefined set of properties. Declaring a property that is not defined for the class is not considered an error, but the property will be ignored during compilation.
   - Example:
     ```heta
     pr1 @Process { compartment: comp1 };
     ```
     In this case, `compartment` is not a valid property of the `Process` class, so it will be ignored:
     ```heta
     pr1 @Process { };
     ```

2. **Required Properties:**
   - Some classes enforce specific required properties. These properties are validated after all components in the code have been loaded.
   - Example: The `Const` class requires the numeric property `num`:
     ```heta
     k1 @Const; // This does not throw an error
     k1 = 1; // Adding the required property completes the definition
     ```

3. **References:**
   - Certain properties must reference other components within the same namespace, and these references are validated after the entire model is loaded.
   - Example: A `Species` instance must include the `compartment` property, which refers to a `Compartment` instance:
     ```heta
     S @Species { compartment: comp1 } .= 0; // Throws an error if comp1 is not defined
     comp1 @Compartment .= 1; // Adding the compartment resolves the reference
     ```

4. **Class Hierarchy:**
   - `Component` is the root class from which all other Heta classes inherit. This hierarchical structure allows shared properties and behaviors to propagate across classes.

5. **Abstract classes**
    - Some classes are abstract and cannot be instantiated directly. They serve as base classes for other classes and provide common properties and behaviors.
    - Example: `_Size` is an abstract class that defines properties related to units. It is inherited by classes like `TimeScale`, `Const`, `Record`, etc.

6. **Syntax remarks**
    - Certain properties in Heta classes, such as `title` and `notes`, support syntax sugar for concise and readable declarations.
    - Additionally, within `{}` blocks, strings often do not require quotation marks unless they contain spaces or special characters, simplifying the syntax further.
    - See more in the [Syntax](./syntax) section.

## Component

**Parent:** *no*

## Component

**Parent:** None  

The `Component` class is the root class for all Heta components. It provides a set of general properties that are inherited by all other classes. These properties are primarily used for annotating and organizing model components.

### Properties

| Property       | Type      | Required | Default | Syntax Sugar | Description                                             |
|----------------|-----------|----------|---------|--------------|---------------------------------------------------------|
| `title`        | string    |          |         | `'Title'`   | A human-readable, non-unique identifier for the component. |
| `notes`        | string    |          |         | `'''Notes'''` | Arbitrary text used for annotation. Can optionally support Markdown for rich text. |
| `tags`         | string[]  |          | `[]`    |              | An array of tags for categorizing or grouping components. Quotation marks are not needed unless the string contains spaces or special characters. |
| `aux`          | object    |          | `{}`    |              | User-defined auxiliary data in key-value pairs. This can be used for storing custom annotations or metadata. |
| `xmlAnnotation`| string    |          |         |              | Additional annotation in XML format, compatible with SBML's `<Annotation>` tag. |

### Usage Examples

```heta
'''This is a general description.'''
comp1 @Component 'Main Component' {
    tags: [example, annotation],
    aux: {
        metadata: { creator: John Doe, created: 2024-12-01 }
    },
    xmlAnnotation: "<Annotation><info>Example XML annotation</info></Annotation>"
};
```

## _Size

**Parent:** [Component](#component)  

The `_Size` class is an abstract class that provides properties related to units. It serves as a base class for other components, such as `TimeScale`, `Const`, and `Record`, which require unit-related definitions. `_Size` cannot be instantiated directly.

More information about units can be found in the [Units](./units) section.

### Properties

| Property | Type | Required | Default  | Description |
|----------|------|----------|----------|-------------|
| `units`  | UnitsExpr or UnitsComponent[] | | | Specifies the units for the component. Can be a string describing the units or an array of `UnitsComponent` objects. |

### Example

While `_Size` itself is abstract and cannot be instantiated, its properties are inherited and utilized by concrete classes:

```heta
comp1 @Const {
    units: kg/L
};
```
or using an array of UnitsComponent:

```heta
comp1 @Const {
    units: [
        { kind: kg, exponent: 1 },
        { kind: L, exponent: -1 }
    ]
};
```

## UnitsComponent

**Parent:** None  

The `UnitsComponent` class defines a single component of a unit, such as its kind (e.g., kilogram, liter), a multiplier, and an exponent. It is primarily used in the `units` property of components derived from `_Size`.

### Properties

| Property     | Type     | Required | Default | Ref      | Description                                      |
|--------------|----------|----------|---------|----------|--------------------------------------------------|
| `kind`       | ID       | Yes      |         | UnitDef  | The reference ID to a predefined unit definition (e.g., `kg`, `L`). |
| `multiplier` | numeric  |      | 1       |          | A scaling factor applied to the unit.           |
| `exponent`   | numeric  |      | 1       |          | The power to which the unit is raised.          |

## TimeScale

**Parent:** [_Size](#_size)  

The `TimeScale` class represents an alternative time variable. It allows users to define transformations of the base time variable `t`, such as expressing time in different units or with a shifted origin.

### Properties

| Property    | Type    | Required | Default | Syntax Sugar | Description                                           |
|-------------|---------|----------|---------|--------------|-------------------------------------------------------|
| `slope`     | number  |          | 1       |              | A fixed multiplier applied to the base time variable. |
| `intercept` | number  |          | 0       |              | A fixed offset added to the base time variable.       |
| `output`    | boolean |          |         |              | If `true`, the transformed time variable is available for output (e.g., plots, tables). |

### Behavior

The `TimeScale` transformation is expressed as:
```
new_time = slope * t + intercept
```

The base time variable `t` is always available and is automatically initialized in the namespace.
The base time t cannot be deleted but its units can be customized.

### Examples

Set units for default time scale:

```heta
t {units: second};
```

Define a custom time scale with a slope and output the transformed time variable:

```heta
tih @TimeScale {
    slope: 2.78e-4,
    intercept: 0,
    output: true,
    units: hour
};
```

### Substituting TimeScale with Record

In some cases, a `Record` component can be used to achieve the same functionality as `TimeScale`:

```heta
tih @Record { output: true, units: hour } := 2.78e-4 * t;
```

This provides equivalent behavior but uses the more general-purpose `Record` class.

## Const

**Parent:** [_Size](#_size)  

The `Const` class represents constant numerical values that do not change over the course of a simulation. These constants are typically used as inputs to a model, such as parameters or coefficients.

### Properties

| Property   | Type    | Required | Default  | Syntax Sugar | Description                                                               |
|------------|---------|----------|----------|--------------|---------------------------------------------------------------------------|
| `num`      | number  | Yes      |          | = <number> | The exact numerical value of the constant or its initial value for identification. |

### Examples

```heta
k1 @Const {units: 1/h} = 1.4e-6;
```

## Record

**Parent:** [_Size](#_size)  

The `Record` class represents time-varying variables, such as states or compartments, that can change over the course of a simulation. These variables often have a physical or biological meaning and can be influenced by assignments, `Process`/`Reaction` instances, or initial conditions.

### Properties

| Property       | Type                     | Required | Default | Syntax Sugar | Description    |
|----------------|--------------------------|----------|---------|--------------|----|
| `assignments`  | Dictionary{ID, MathExpr} |          |   `{}`      |     `.=`, `:=` or `[<sw>]=`  | A dictionary where each key is a switcher ID, and each value defines an expression to assign to the variable. |
| `boundary`     | boolean                  |          | `false` |              | If `true`, the variable is under boundary condition and will not change by `Process`/`Reaction` instances. |
| `output`       | boolean                  |          |         |              | If `true`, the variable is included in simulation outputs like plots or tables. |

### Behavior

The value of a `Record` can change:
1. **By default initialization** (e.g., `p1 .= x * y`).
2. **Through assignments at specific times or conditions** using `Switcher` instances.
3. **By interactions with `Process`/`Reaction` instances**, unless marked as a boundary condition.

### Examples

#### Basic Record Declaration

```heta
p1 @Record {
    boundary: true,
    units: kg/L
};
p1 .= x * y;
```

#### Assignments by Switchers

```heta
p1 @Record { boundary: true, units: kg/L };
p1 .= x * y;
p1 [sw1] = 0; //change the value of p1 to 0 when sw1 is triggered
```

## Process

**Parent:** [Record](#record)

## Process

**Parent:** [Record](#record)  

The `Process` class represents fluxes or reactions that modify the values of other `Record` instances over time. Processes describe the dynamic interactions between components and are a key element in constructing system dynamics.

### Properties

| Property    | Type                  | Required | Default | Syntax Sugar | Description                                                             |
|-------------|-----------------------|----------|---------|--------------|-------------------------------------------------------------------------|
| `actors`    | ProcessExpr or Actor[] |          | `[]`    |              | Defines the components affected by the process and their stoichiometries. |
| `reversible`| boolean               |          | `true`  |              | Indicates whether the process is reversible.                            |

### Behavior

- **Actors:** The `actors` property specifies which `Record` instances are modified by the process and how (e.g., production, consumption, or transfer).
- **Reversibility:** By default, processes are reversible. This property can be explicitly set to `false` to model irreversible processes.

### Examples

#### Basic Process Declaration

```heta
pr1 @Process { actors: p1 => 2 * p2 };
```

#### Expanded Representation

The above example is equivalent to:

```heta
pr1 @Process {
    actors: [
        { target: p1, stoichiometry: -1 },
        { target: p2, stoichiometry: 2 }
    ],
    reversible: false
};
```

#### Using reversible process

```heta
pr2 @Process { actors: A <=> B };
```

### Notes

- The `actors` property can be expressed as a single `ProcessExpr` or an array of `Actor` objects.
- A shorthand notation `ProcessExpr` can be used (e.g., `A -> B`, `A <-> B`) is used to describe actors. 
- The reversible property is included for compatibility with SBML's and SimBio's reversible attribute.

## Effector

**Parent:** *none*

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| target | ID | true | | `Record` | Reference to record |

## Actor

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
| active | boolean | | `true` | | If it is `false` the switcher will be off. A user can turn it off/on in simulation scenarios. |
| atStart | boolean | | | | `true` if we should check the condition at `start_` scope. |

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
