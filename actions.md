# Actions

The format of heta-based modeling platform is a kind of declarative language (scheme). Nevertheless Heta is not actually a declarative language because it sequentially updates and changes the platform.

The "action" is term used in Heta language to clarify what platform should do with a statement. Some of actions like `#include` works at file level.

Action is designated by `#` symbol before the action type and can be used in any statement.

If no statement is written the default statement is `#upsert`.

## Action list

- [insert](#insert)
- [update](#update)
- [upsert](#upsert)
- [delete](#delete)
- [include](#include)

## insert

The `insert` action adds a new component to the platform. 
If the element with the same index already exist it will be replaced by the new one. 
When applying `insert` the class should be stated directly.

### Example 1

```heta
// first insert
#insert  c1 @Compartment {title: first} := 1;

// second insert with the same index
#insert  c1 @Compartment {title: second};
```
The compartment with the index `c1` will be replaced by the another compartment.
The result of two statements will be

```json
{
    "c1" : {
        "title": "second",
        "assignment": {} 
    } 
}
```

### Example 2

```heta
#insert S { compartment: comp1 };
```

This statement should throw the error because class is not indicated.

## update

The `update` action only changes the properties of existed component without creating a new one.

If an updated property exists it will be rewritten by a new value. The current version of standard makes an exception for assignment property in `Record` instances. For these updates the subproperty will be added to the assignment dictionary.

### Example 1
```heta
// first insert
#insert  c1 @Compartment {title: first} := 1;

// second insert with the same index
#update  c1 { title: second };
```

The result of two statements
```json
[{
    "c1" : {
        "title": "second",
        "assignment": { "ode_": 1 } 
    } 
}]
```

### Example 2

```heta
#update S @Species { compartment: c1 };
```

The statement throws error because the component with index `S` has not been created before.

### Example 3

```heta
k1 @Const = 1.2 { aux: {group: one} };
k1 { aux: {human: true}};
```

This result will not include `{group: one}` because of property update rules:
```json
{
    "k1": {
        "class": "Const",
        "num": 1.2,
        "aux": { "human": true }
    }
}
```

### Example 4

```heta
p1 @Record { assignments: {start_: 0} };
p1 { assignments: {ode_: x*y}};
p1 [sw]= 0;
```
The result will include all assignments because it it an exception for assignments:

```json
{
    "p1": {
        "class": "Record",
        "assignments": {
            "start_": "0",
            "ode_": "x*y",
            "sw": "0"
        }
    }
}
```

## upsert

The `upsert` action is the default action and works as `#insert` when class is stated and as `#update` otherwice.

### Example

```heta
#upsert k1 @Const = 1; // this works as #insert

#upsert k1 = 2; // this works as #update

k1 = 3; // this acts as #upsert -> #update
```

## delete

The `delete` action erase the element with the index. If the component with the index is not exist this throws the error.

### Example

```heta
#insert k1 @Const = 1;
#delete k1; // deletes k1
```

## include

*Include action is not recommended. Use include statement instead.*

The Include action works at files level. It does not create or update the component but load the another file inside the current one.

It uses the virtual properties to set the different files and formats.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| source | string | true | | filepath | Relative or absolute path to a file. |
| type | string | | `heta` | | Type of include. Possible values are: "heta", "json", "yaml", "xlsx", "md" |
| ... | | | | | The other settings depending on `type` |

### Example

```heta
#include { source: ./pk.heta, type: heta };
#include { source: ./annotation.xlsx, type: xlsx };
```
