# Actions

Heta is not a declarative language because it sequentially updates and changes the platform.
The `action` property inside an "action statement" clarifies what platform should do with a statement.

The action shortened is designated by `#` symbol before the action name.

The closest analogue of the "action" in other programming languages is the name of function of command to execute.

If no statement is written the default statement is `#upsert` which is the equivalent of `#insert` if `class` property is stated or `#update` if not.

## Action list

- [insert](#insert)
- [forceInsert](#forceinsert)
- [update](#update)
- [upsert](#upsert)
- [delete](#delete)
- [defineUnit](#defineunit)
- [include](#include)
- [setNS](#setns)
- [deleteNS](#deletens)
- [setScenario](#setscenario)
- [importNS](#importns)
- [import](#import)
- [defineFunction](#definefunction)

## insert

`insert` action adds a new component to the platform. 
When applying `insert` the `class` and `id` properties should be stated.
If the component with the same index already exists it will be replaced by the new one. In this case the software should throw a warning. See also [forceInsert](#forceInsert) action.

The `insert` action may be skipped in statement because this is the default action when `class` property exists. 

| property | type | required | default | ref | description |
| ---------|------|----------|---------|-----|-------------|
| class | string | true | | | Class name of a component, see [classes](./classes) |
| id | ID | true | | | The unique identifier of the component in namespace |
| space | ID | |  nameless | namespace id | An identifier of a namespace. The namespace must be created before usage. |
| ... | | | | | The other keys depending on `class` associated properties. |

### Example 1

```heta
// first insert
#insert c1 @Compartment { title: first } := 1;

// second insert with the same index
#insert c1 @Compartment { title: second };
```
The compartment with the index `c1` will be replaced by the second action statement.
The result of the two statements will be equivalent to the following

```heta
{
    id: c1,
    title: second,
    class: Compartment,
    assignments: {} 
};
```

### Example 2

```heta
#insert S { compartment: comp1 };
```

This statement should throw the error because `class` property is not indicated.

### Example 3

```heta
#insert space1::x1 @Record .= 0;
```
This statement throws the error because namespace with `id` space1 has not been declared.

## forceInsert

The `forceInsert` do the same as [`insert`](#insert) but does not throw a warning if the component with the same index already exists. This action was included to clarify directly if user wants to replace the component.

### Example 1
  
```heta
c1 @Record := 1;

// second insert with the same index, no warning
#forceInsert c1 @Compartment .= 1;
```

## update

The `update` action only changes the properties of a previously created component without creating a new component.

If the updated property exists it will be rewritten by a new value. The current version of the Heta standard makes an exception for `assignments` property in `Record` instances. For these updates, the sub-property will be added to the assignments dictionary.

The `update` action cannot change its `class`. If you need so you have to create a new component using the `insert` action.

The `update` property may be skipped because this is the default action when `class` property is not declared in a statement.

| property | type | required | default | ref | description |
| ---------|------|----------|---------|-----|-------------|
| id | ID | true | | | The unique identifier of the component in namespace |
| space | ID | |  nameless | namespace id | An identifier of a namespace where the component is located. |
| ... | | | | | The other keys depending on the `class` of the component. |

### Example 1

```heta
// create a new component by #insert
#insert c1 @Compartment { title: first } := 1;

// update "title" property
#update c1 { title: second };
```

The result of two statements
```heta
{
    id: c1,
    class: Compartment,
    title: second,
    assignments: { ode_: 1 } 
};
```

### Example 2

```heta
#update S @Species { compartment: c1 };
```

The statement throws the error because the component with index `S` was not created before.

### Example 3

```heta
k1 @Const = 1.2 { aux: { group: one } };
k1 { aux: { human: true } };
```

The result will not include `{ group: one }` because new `aux` sup-property rewrites all the content
```heta
{
    id: k1,
    class: Const,
    num: 1.2,
    aux: { human: true }
};
```

### Example 4

```heta
p1 @Record { assignments: { start_: 0 } };
p1 { assignments: { ode_: x*y } };
p1 [sw]= 0;
```
The result will include all assignments because it is an exception for assignments:

```heta
{
    id: p1,
    class: Record,
    assignments: {
        start_: "0",
        ode_: "x*y",
        sw: "0"
    }
};
```

## upsert

The `upsert` action is the default action and works as `#insert` when class is stated and as `#update` otherwise.

### Example

```heta
#upsert k1 @Const = 1; // this works as #insert

#upsert k1 = 2; // this works as #update

k1 = 3; // this acts as #upsert -> #update
```

## delete

The `delete` action erases the element from the namespace. If the component with the index does not exist this throws an error.

| property | type | required | default | ref | description |
| ---------|------|----------|---------|-----|-------------|
| id | ID | true | | | The unique identifier of the component in namespace |
| space | ID | |  nameless | namespace id | An identifier of a namespace where the component is located. |

### Example

```heta
#insert k1 @Const = 1;
#delete k1; // deletes k1 from namespace
```

## defineUnit

The `defineUnit` sets a new unit of measurement which can be used inside QSP platform.

It extends the base list of units by the user defined unit.
The created unit can be then used in `units` property of `@Const`, `@Record` or to define another unit.
Units property can be `undefined` which means this is basic unit and cannot be expressed in terms of other units like meter, second, etc.

This is the action to create `UnitDefinition` class for implementation to use it inside [UnitsExpr](#unitsexpr).

See more in [Units](./units) chapter.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| id | `ID` | true | | | unique identifier of `UnitDef` |
| units | UnitsExpr *or* [UnitsComponent](./classes#unitscomponent)[] | | | | String describing components of complex units or array of complex unit components. |

### Example 1
```heta
kDa #defineUnit { units: [
    { kind: g, multiplier: 1e3, exponent: 1 },
    { kind: mole, exponent: -1 }
]};

Da #defineUnit { units: g/mole };
```

### Example 2
```heta
M #defineUnit {units: mole/litre};
uM #defineUnit {units: (1e-6 M)};
S_0 @Const {units: uM} = 1e-2;
```

## include

The `include` action is an alternative to [include statement](./syntax#include-statement).

The include action works at the modules level. It does not create or update the component but act like it loads another file inside the current one.

It uses virtual properties to set different files and formats.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| source | string | true | | filepath | Relative or absolute path to a file. |
| type | string | | `heta` | | Type of include. Possible values are: "heta", "json", "yaml", "table", "xlsx", "sbml" |
| ... | | | | | The other properties depending on [module type](./modules) |

### Example

```heta
#include { source: ./pk.heta, type: heta };
#include { source: ./annotation.xlsx, type: table, sheet: 2 };
```

## setNS

The `setNS` action initializes namespace or updates [namespaces](namespaces) properties.
The alternative to the `setNS` action is the the [namespace statement](syntax#namespace-statement).

The `setNS` action (or the `namespace` statement) must be used prior to the creation of the first component in the namespace.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| space | string | true | | | Name of created or updated namespace |
| type | string | | `concrete` | | namespace type: "concrete" or "abstract", see [namespaces](namespaces) |

## deleteNS

The `deleteNS` action deletes the namespace from the platform.
If the space does not exist it throws the error.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| space | string | true | `nameless` | | Namespace to delete |

**Example:**

```heta
  #deleteNS;              // to delete namespace nameless
  #deleteNS {space: one}; // to delete namespace one
  #deleteNS two::*;       // delete namespace two
```

## setScenario

The `setScenario` action creates a "scenario" object that describes model simulations.
Scenario describes the particular simulation conditions for a model with the updated constants, switchers, outputs and time ranges.
Using `Scenario`s one can create a series of model variants which are based on the same expressions but must be simulated separately.

Scenarios have their own identifier and do not rewrites components. You can create `scn1` scenario and `scn` `@Const` at the same time and use them independently.

The `parameters` property re-initializes the `@Const` numerical values for the particular simulation. If some parameter is not mentioned here the default value will used.

`saveat` or `tspan` properties must be set for the particular Scenario. If you set both only `saveat` will be used.

| property | type | required | default | ref | description |
| ---------|------|----------|---------|-----|-------------|
| id | ID | true | | | Unique identifier of a scenario. |
| model | ID | true | nameless | Namespace | Reference to a concrete namespace that will be used for model creation |
| parameters | object<ID,number> | | | Const | Object representing updated `@Const` numerical value. Keys inside the object are references to Const, values are the values for simulations. | 
| saveat | number[] | | | | An array of numerical values. The values states the time points for simulated output |
| tspan | number[] | | | | Two numerical values declaring time range for simulation. |
| observables | ID[] | | | Record | The property state the records that will be saved as simulation results. If not set the default observables will be used (`output: true` property in records). |
| events_active | object<ID,boolean> | | | Switcher | Object representing Switchers that will be active or inactive in the scenario. Keys are Switcher's identifiers, values are boolean values displaying if the switcher is active. |
| events_save | object<ID,array> | | | Switcher | User should define here is it necessary to save output before and after event. If not set both: before and after values will be saved. Key is switcher id, value is an array of two boolean values. |

### Example 1

```heta
// minimal set
scn1 #setScenario {tspan: [0, 120]};
```

### Example 2

```heta
scn2 #setScenario {
  model: mouse,
  parameters: {weight: 20, kel: 1e-3},
  saveat: [0, 1, 2, 4, 6, 8, 10, 12],
  observables: [A0, C1, C2],
  events_active: {sw1: false, sw2: true},
  events_save: {sw2: [true, false]}
};
```

## importNS

1. The `importNS` clones all content of the namespace into another one. This action allows us to reuse components within different namespaces.

1. When using `importNS` both namespaces declared in `space` and `fromSpace` must exist.

1. Identifiers of components can be updated using `prefix`, `suffix`, `rename` properties. If no `prefix`, `suffix`, `rename` is declared identifiers remain the same.

1. If `prefix` or `suffix` are declared (for example as `pref_`, `_suf`) the id changes follows the rules: `old` => `pref_old_suf`. `prefix` and `suffix` do not act on a component of classes: `unitDef`.

1. `rename` states the rename rule directly. If `rename` set rule for id, `suffix` and `prefix` not used for the particular id.

1. Renaming id rules updates also references.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| space | string | true | "nameless" | | Space name where to import |
| fromSpace | string | true | | | Source space name. It must not be equal to space property. |
| prefix | string | | "" | | prefix of new ids |
| suffix | string | | "" | | suffix of new ids|
| rename | Dictionary | | {} | | id matching table |

### Example 1

```heta
abstract namespace one begin
  k1 @Const = 1.1;
  k2 @Const = 3.3;
  p1 @Record := 3.3*k1*k2;
end

namespace two begin
  k1 @Const = 2.2;
end

#importNS {
  space: two,
  fromSpace: one,
  prefix: one_
};
```

This is equivalent to the following:

```heta
abstract namespace one begin
  k1 @Const = 1.1;
  k2 @Const = 3.3;
  p1 @Record := 3.3*k1*k2;
end

namespace two begin
  k1 @Const = 2.2;
  one_k1 @Const = 1.1;
  one_k2 @Const = 3.3;
  one_p1 @Record := 3.3 * one_k1 * one_k2;
end
```

### Example 2

```heta
abstract namespace one begin
  k1 @Const = 1.1;
  k2 @Const = 3.3;
  p1 @Record := 3.3*k1*k2;
end

namespace two begin
  k1 @Const = 2.2;
end

#importNS {
  space: two,
  fromSpace: one,
  prefix: one_,
  rename: { k1: k1, k2: imported_k2 }
};
```

This is equivalent to the following:

```heta
abstract namespace one begin
  k1 @Const = 1.1;
  k2 @Const = 3.3;
  p1 @Record := 3.3*k1*k2;
end

namespace two begin
  k1 @Const = 1.1; // initial k1 was replaced
  imported_k2 @Const = 3.3; // uses rule from "rename"
  one_p1 @Record := 3.3 * k1 * imported_k2; // uses rule from "rename" for the right side
end
```

## import

1. `import` clone particular component changing it's id and space (if required).

1. Renaming rules are the same as for `#importNS` excepting target component renaming rules.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| id | string | true | | | target id to import |
| fromId | string | true | | | Source id |
| space | string | true | "nameless" | | Space name where to import |
| fromSpace | string | true | | | Source space name |
| prefix | string | | "" | | prefix of new ids |
| suffix | string | | "" | | suffix of new ids|
| rename | Dictionary | | {} | | id matching table |

## defineFunction

The `defineFunction` action allows users to write a simple function in the platform and use it together with pre-defined functions [from the list](math#list-of-functions).

The `math` property of `defineFunction` may include arythmetic operators, pre-defined constants, identifiers of arguments, pre-defined functions and functions added with other `defineFunction` statements. It cannot include other identifiers like for components.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| id | `ID` | true | | | unique identifier of `FunctionDef` |
| arguments | `ID[]` | | `[]` | | arguments |
| math | `string[]` | true | | | single string representing math expression |

### Example 1

```heta
#defineFunction pow7 {
  arguments: [x],
  math: "pow(x, 7)"
};
```

### Example 2

```heta
#defineFunction f3 {
  arguments: [x1, x2, x3],
  math: sqrt(x1^2 + x2^2 + x3^2)
};
```
