# Actions

The format of modeling platform (or model) which is developed with Heta code is a kind of declarative language (scheme). Nevertheless Heta is not actually a declarative language because it sequentially updates and changes the platform.

The "action" is term used in Heta language to clarify what platform should do with a statement. Some of actions like `#import` works at modules level.

Action is designated by `#` symbol before the action type and can be used in any statement.

In no statement is written the default statement is `#upsert`.

## Action list

- [insert](#insert)
- [update](#update)
- [upsert](#upsert)
- [delete](#delete)
- [import](#import)

## insert

Insert adds a new component to the platform. 
If the element with the same index already exist it will be replaced by the new one. 
When applying `insert` the class should be stated directly.

### Example 1

```heta
// first insert
#insert  one::c1 @Compartment {title: first} := 1;

// second insert with the same index
#insert  one::c1 @Compartment {title: second};
```
The compartment with the title `one::c1` will be replaced by the another compartment.
The result of two statements will be

```json
{
    "one::c1" : {
        "title": "second",
        "assignment": {} 
    } 
}
```

### Example 2

```heta
#insert one::S { compartment: comp1 };
```

This statement should throw the error because class is unknown.

## update

The update only change the properties of existed component without creating a new one.

### Example 1
```heta
// first insert
#insert  one::c1 @Compartment {title: first} := 1;

// second insert with the same index
#update  one::c1 { title: second };
```

The result of two statements
```json
[{
    "one::c1" : {
        "title": "second",
        "assignment": { "ode_": 1 } 
    } 
}]
```

### Example 2

```heta
#update one::S @Species { compartment: c1 };
```

The statement throws error because the component with index one::S has not been created before.

## upsert

The upsert statement is the default action and acts as `#insert` when class is stated and as `#update` otherwice.

### Example

```heta
#upsert k1 @Const = 1; // this works as #insert

#upsert k1 = 2; // this works as #update

k1 = 3; // this acts as #upsert -> #update
```

## delete

The delete action erase the element with the index. If the component with the index is not exist this throws the error.

### Example

```heta
#delete k1; // deletes k1 if it is exist
```

## import

The Import action works at [module](./modules) level. It does not create or update the component but load the another module insude the current one.

It uses the virtual properties to set the different files and formats.

| property | type | required | default | ref | description | 
| ---------|------|----------|---------|-----|-------------|
| source | string | true | | filepath | Relative or absolute path to a file. |
| type | string | | heta | | Type of module. Possible values are: "heta", "json", "yaml", "xlsx", "md" |
| ... | | | | | The other settings depending on `type` |

### Example

```heta
#import { source: ./pk.heta, type: heta };
#import { source: ./annotation.xlsx, type: xlsx };
```
