# Modules

Heta modules is the separated parts of code that can be parsed and
combined into single platform. The purposes of modular sytem is similar to other programming languages supporting modules:

- split the whole platform to subfiles for better code management;
- multiple loading of the same code in different context;
- allow whiting code in various formats.

The available list of module types are: heta, json, yaml, xlsx, sbml. 

1. **Module** in Heta means the file that can be translated to Heta format. 

1. The identifier of the module is the absolute path of file in file system. For example: `y:/the-platform/src/index.heta`.

1. Module can be loaded inside other module by [`include` statement](syntax#include-statement) or by [`include` action](./actions#include). Loading of modules does not create any new context or namespace.

1. `include` statement has `type` property. The default type is `heta` and it means the file includes just regular [Heta code](syntax).

## type heta

The default type of module. Text format in Heta syntax.

## type json

This is module in JSON format (array of objects) which can be mapped to Heta code.

This module type is a good way to import the Heta acceptable code from other tools.

Example:
```json
[
    {
        "class": "Compartment",
        "action": "insert",
        "id": "c1",
        "assignments": {"start_": "12.2"},
        "boundary": true
    },
    {
        "class": "Species",
        "action": "insert",
        "id": "s1",
        "compartment": "c1",
        "assignments": {"start_": "5.3"}
    }
]
```

This structure is equivalent of to the following Heta code:

```heta
#insert c1 @Compartment { boundary: true } .= 12.2;
#insert s1 @Species { compartment: c1 } .= 5.3;
```

## type yaml

This is one-to-one equivalent of type json but written in YAML format.

Example:
```yaml
- class: Compartment
  action: insert
  id: c1
  assignments:
    start_: 12.2
  boundary: true

- class: Species
  action: insert
  id: s1
  compartment: c1
  assignments:
    start_: 5.3
```

This structure is equivalent of to the following Heta code:

```heta
#insert c1 @Compartment { boundary: true } .= 12.2;
#insert s1 @Species { compartment: c1 } .= 5.3;
```

## type xlsx

This module type allows to write a modeling platform in Excel file format (XLSX) and use this module as another ones.
This can be useful for very large-scale models.

The XLSX file can include any number of sheets but each sheet must be included separately.
The first row must include path to property, the other rows includes the values of the properties.
The columb with header `on` can be used to turn off the import of the row. Zero value here means: "do not use this line".
`omitRows` property can be set to skip several rows between header (path row) and values.
`sheet` property clarify which sheet should be loaded starting from 1, the default value is `1`;

Path rules:

- nested properties are shown by dot (.) notation
- arrays are noted by `[]` at the end of path. Array items are splitted by `;` symbol.
- empty cells do not define any value.

Example
**file:** `table.xlsx`, sheet #1

|#| on | id | class | tags[] | assignments.start_ | boundary |
|---|---|---|---|---|---|---|
|1|
|2| 1 | comp1 | Compartment | a; b; c | 1 | true |
|3| 1 | comp2 | Compartment | a; b | comp1*2 | |

This table is equivalent to Heta code

```heta
comp1 @Compartment {tags: [a, b, c], boundary: true} .= 1;
comp2 @Compartment {tags: [a, b]} .= comp1*2;
```

**file:** `table.xlsx`, sheet #2

|#| on | id | class | compartment | assignments.start_ | boundary |
|---|---|---|---|---|---|---|
|1| 1| s1 | Species | comp1 | 10 | false |
|2| 0| s2 | Species | comp2 | 0  | |

This table is equivalent to Heta code

```heta
s1 @Species {compartment: comp1, boundary: false} .= 10;
// s2 @Species {compartment: comp2} .= 0;
```

**file:** `index.heta` (combining all together and export to JSON format)
```heta
include table.xlsx type xlsx with {sheet: 1, skipRows: 1} // skipping one row
include table.xlsx type xlsx with {sheet: 2}

#export {format: json, filepath: output};
```

## type sbml

This type of module was created to add SBML formatted models into Heta modeling platform. Currently only SBML of level 2 is supported.


Example:
**file:** `model.xml`
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<sbml 
  xmlns="http://www.sbml.org/sbml/level2/version4" 
  xmlns:xhtml="http://www.w3.org/1999/xhtml" 
  level="2" version="4"
  >
  <model>
    <listOfCompartments>
      <compartment
        id="default_comp"
        name="Default compartment"
        size="1"
        >
      </compartment>
    </listOfCompartments>
    <listOfSpecies>
      <species
        id="S"
        name="substrate"
        compartment="default_comp"
        initialAmount="10"
        >
      </species>
    <listOfSpecies>
  </model>
</sbml>
```

This module is equivalent to the following Heta code
```heta
default_comp @Compartment {boundary: true} .= 1;
S @Species 'substance' {compartment: default_comp} .= 10/default_comp;
```
