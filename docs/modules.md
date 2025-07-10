# Modules

**Heta module** is the separate part of modeling platform (file or file part) which can be interpreted and
included into single platform. The purposes of modular system is to organize working on large models:

- split the whole platform to sub-files for better code management;
- multiple loading of the same code in different context;
- allow whiting code in various formats.

The available list of module types are: heta, json, yaml, table (xlsx), sbml. 

1. Any **Module** in Heta can be translated to Heta code. 

1. The unique identifier of the module is the absolute path of file in file system. For example: `y:/the-platform/src/index.heta`.

1. Module can be loaded into other module with [include statement](syntax#include-statement) or with [include action](./actions#include). Loading of modules do not create any new namespace or component.

1. `include` statement has `type` property. The default type value is `heta` and it means the file includes just regular [Heta code](syntax).

## heta module

The default type of module. Text file in Heta syntax.

**Example 1:**

```heta
// addition of module (with include statement) with the relative file path

include ./my-module.heta;
```

**Example 2:**

```heta
// addition of module (with include action) with the relative file path

#include { source: ./my-module.heta };

```

## json module

This is module in JSON format (array of objects) which can be mapped to Heta code.

This module type is a possible way to import the Heta code from other tools.

**Example:**

Content of file "model.json"
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

The module can be loaded into heta platform by the code

```heta
#include {source: ./model.json, type: json};
```

## yaml module

This is one-to-one equivalent of type json but written in YAML format.

**Example:**

Content of file "model.yml"
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

The module can be loaded into heta platform by the code

```heta
#include {source: ./model.yml, type: yaml};
```

## table module

_See also the [Tabular format](tabular-format) chapter._

This module type allows to write a model's components in table file format: xlsx, csv, etc.
This might be useful for very large-scale models.

The first row must include property identifiers, the other rows includes the values of the properties.
The column with reserved header `on` can be used to turn off the import of the row. Zero or empty value here means: "do not compile this line".

Additional properties of `#include` action:

- `omitRows` property can be set to skip several rows between header (first row) and component rows, the default value is `0`; 
  This is useful for formats like XLSX where the first row can be used for comments or other information.
- `sheet` property clarifies which sheet should be loaded starting from `0`, the default value is `0`; 
  This is useful for formats supporting multiple sheets like XLSX. For this case file can include any number of sheets but each sheet must be included separately by a separate `#include` statement.
- `transpose` property can be set to `true` to transpose the table, i.e. to swap rows and columns. If set, each column starting from 2 will be treated as a separate component. The default value is `false`.

**Example:**

file: "table.xlsx", sheet #0

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

file: "table.xlsx", sheet #1

|#| on | id | class | compartment | assignments.start_ | boundary |
|---|---|---|---|---|---|---|
|1| 1| s1 | Species | comp1 | 10 | false |
|2| 0| s2 | Species | comp2 | 0  | |

This table is equivalent to Heta code

```heta
s1 @Species {compartment: comp1, boundary: false} .= 10;
// s2 @Species {compartment: comp2} .= 0;
```

file: "index.heta" (combining all together)
```heta
#include {source: ./table.xlsx, type: table, sheet: 0, omitRows: 1}; // skipping one row between header and content
#include {source: table.xlsx, type: table, sheet: 2};

```

## sbml module

This type of module was created to adopt SBML formatted models into Heta modeling platform. Currently SBML of levels 2 and 3 are supported.

**Example:**

file: "model.xml"
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

This model can be loaded into the platform with the following code:

```heta
#include { source: ./model.xml, type: sbml };
```
