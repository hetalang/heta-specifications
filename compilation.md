# Compilation steps

Heta code is transformed by the following steps:

1. **Parsing.** **Heta modules** are translated to the collection of components. 

    Parsing starts from single *index* file (it can have any name), then other files are added recursively based on `#include` actions. Each file represents one module.

    The errors which happen at the stage will be of types: `FileSystemError`, `ParsingError`.

1. **Files integration.** Collection of Heta modules are combined into a single structure **queue** which is sequence of queries for platform storage based on [include](include) statement. The include must not have circular references.

    The errors which happen at the stage will be of type `ModuleError`.

1. **Translation.** Queue is translated sequentially to create diclarative Heta structure (key-value format). The keys are the indexes of components, values is the components. The properties must satisfy the requirements for its classes.

    The errors which happen at the stage will be of types: `QueueError`, `ValidationError`.

1. **Binding.** Binding Heta components based on internal cross references. Checking references. Checking required properties.

    The errors which happen at the stage will be of types: `BindingError`.

1. **Other steps.** The next steps depends on components in storage and setting of **Heta compiler**. For example it is expected that `_Export` classes induce the creation of code for export.

    The errors which happen at the stage will be of types: `ExportError`.

## Example

Structures in example are shown in Heta and JSON notation.

**Structure 1. Heta language code**

file: `index.heta`
```heta
s1 @Species { compartment: comp0 };

include ./addon.heta type heta
```

file: `addon.heta`
```
s1 'Species number one';

sbml @SBMLExport;
```

**Structure 2. Collection of modules**

```json
{
    "Y:/platform/src/index.heta" : [
        {
            "action": "upsert",
            "class": "Species",
            "id": "s1",
            "compartment": "comp0"
        },
        {
            "action": "import",
            "source": "Y:/platform/src/addon.heta",
            "type": "heta"
        }
    ],
    "Y:/platform/src/addon.heta" : [
        {
            "action": "upsert",
            "id": "s1",
            "title": "Species number one"
        },
        {
            "action": "upsert",
            "class": "SBMLExport",
            "id": "sbml"
        }
    ]
}
```

**Structure 3. Queue**

```json
[
    {
        "action": "upsert",
        "class": "Species",
        "id": "s1",
        "compartment": "comp0"
    },
    {
        "action": "upsert",
        "id": "s1",
        "title": "Species number one"
    },
    {
        "action": "upsert",
        "class": "SBMLExport",
        "id": "sbml"
    }
]
```

**Structure 4. Declarative Heta structure**

```json
{
    ...
    "s1": {
        "class": "Species",
        "compartment": "comp0",
        "title": "Species number one"
    },
    "sbml": {
        "class": "SBMLExport"
    }
}
```

**Structure 5. Declarative Heta (bound)**

```json
{
    ...
    "s1": {
        "class": "Species",
        "compartment": "comp0",
        "title": "Species number one",
        "compartmentObj": {...} // if compartment exists in "declarative Heta"
    },
    "sbml": {
        "class": "SBMLExport"
    }
```

**Output formats.**

file: `sbml.xml`

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<sbml 
    xmlns = "http://www.sbml.org/sbml/level2/version4"
    xmlns:xhtml="http://www.w3.org/1999/xhtml"
    level="2" version="4">
    ...
    <listOfSpecies>
        <species
            id = "s1"
            metaid = "s1"
            compartment = "comp0"
            name = "Species number one"
            boundaryCondition = "false"
            constant = "false",
            hasOnlySubstanceUnits = "false"
        />
    </listOfSpecies>
</sbml>
```
