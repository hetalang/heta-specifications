# Actions

The format of Heta-based modeling platform is a kind of declarative language. Nevertheless Heta is not actually a declarative language because it sequentially updates and changes the platform.

The "action" is term used in Heta language to clarify what platform should do with a statement. Some of actions like `#include` works at module level.

Action is designated by `#` symbol before the action name and can be used in any statement.

If no statement is written the default statement is `#upsert`.

## Action list

- [insert](#insert)
- [update](#update)
- [upsert](#upsert)
- [delete](#delete)
- [include](#include)
- [setNS](#setns)
- [importNS](#importns)
- [import](#import)
- [export](#export)

## insert

`insert` action adds a new component to the platform. 
If the component with the same index already exists it will be replaced by the new one. 
When applying `insert` the class should be stated directly.

### Example 1

```heta
// first insert
#insert c1 @Compartment { title: first } := 1;

// second insert with the same index
#insert c1 @Compartment { title: second };
```
The compartment with the index `c1` will be replaced by the another compartment.
The result of two statements will be equivalent to the following

```heta
{
    id: c1,
    title: second,
    class: Compartment,
    assignment: {} 
};
```

### Example 2

```heta
#insert S { compartment: comp1 };
```

This statement should throw the error because class is not indicated.

## update

`update` action changes only the properties of existed component without creating a new one.

If an updated property exists it will be rewritten by a new value. The current version of standard makes an exception for assignment property in `Record` instances. For these updates the subproperty will be added to the assignment dictionary.

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

The statement throws error because the component with index `S` was not created before.

### Example 3

```heta
k1 @Const = 1.2 { aux: { group: one } };
k1 { aux: { human: true } };
```

This result will not include `{ group: one }` because new "aux" property rewrites all the content
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
The result will include all assignments because it it an exception for assignments:

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

`upsert` action is the default action and works as `#insert` when class is stated and as `#update` otherwice.

### Example

```heta
#upsert k1 @Const = 1; // this works as #insert

#upsert k1 = 2; // this works as #update

k1 = 3; // this acts as #upsert (default) -> #update
```

## delete

`delete` action erases the element with the index. If the component with the index is not exist this will throw an error.

### Example

```heta
#insert k1 @Const = 1;
#delete k1; // deletes k1 from namespace
```

## include

*Include **action** is an alternative to [include statement](./syntax?id=include-statement).*

The Include action works at modules level. It does not create or update the component but load the another file inside the current one.

It uses the virtual properties to set the different files and formats.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| source | string | true | | filepath | Relative or absolute path to a file. |
| type | string | | `heta` | | Type of include. Possible values are: "heta", "json", "yaml", "xlsx", "sbml" |
| ... | | | | | The other settings depending on `type` |

### Example

```heta
#include { source: ./pk.heta, type: heta };
#include { source: ./annotation.xlsx, type: xlsx, sheet: 2 };
```

## setNS

`#setNS` action initializes namespace or updates [namespaces](namespaces) properties.

Before the first use of a component in some namespace `#setNS` must be used or alternatively `namespace` statement can be applied, see [namespace statement](syntax#namespace-block-statement).

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| space | string | true | | | Name of created or updated namespace |
| type | string | | `concrete` | | namespace type |

## importNS

1. `importNS` clones all content of the namespace to the another one. This action allows sharing components between different namespaces.

1. When using `importNS` both namespaces declared in `space` and `fromSpace` must exist.

1. The ids of components can be changed using `prefix`, `suffix`, `rename` properties. If no `prefix`, `suffix`, `rename` is declared the ids remain the same.

1. If `prefix` or `suffix` are declared (for example as `pref_`, `_suf`) the id updates follows the rules: `old_id` => `pref_old_id_suf`. `prefix` and `suffix` do not work for component of classes: `unitDef`.

1. `rename` states the rename rule directly. If `rename` set rule for id, `suffix` and `prefix` not used for the particular id.

1. Renaming id rules changes references.

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

## export

1. `#export` action describes what and how the output files will be created.

1. List of formats and available options depends on the compiler of Heta code. Read the documentaion of a compiler.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| format | string | true | | | one of declared formats: 'JSON', 'SBML', etc. |
| filepath | Filepath | true | | | path to target file to create |
| ... | | | | | other options depending of format |

**Example**
The example of exporting the platform content to SBML available in [Heta compiler](https://hetalang.github.io/#/heta-compiler/)

```heta
comp1 @Compartment .= 5.5;
s1 @Species {compartment: comp1} .= 0;

// export "nameless" namespace to SBML format
#export { format: SBML, filepath: sbml_output };
```
