# Compilation steps

Heta is an open format and its rules can be elaborated by software tools in different ways.
Nevertheless formulating the Heta specification we keep in mind the following steps.

1. **Parsing.** **Heta modules** are translated to the collection of components. 

    Parsing starts from single *index* file (it can have any name), then other files are added recursively based on `#include` actions. Each file represents one module.

    The errors which happen at the stage will be of types: `FileSystemError`, `ParsingError`.

1. **Modules integration.** Collection of Heta modules are combined into a single structure **queue** which is sequence of actions for platform container. The modules must not have circular references.

    The errors which happen at the stage will be of type `ModuleError`.

1. **Translation.** The queue is translated sequentially to create the elements of Heta container. Generally it is a key-value pairs. The properties must satisfy the requirements for its classes. See the structure of Heta container below.

    The errors which happen at the stage will be of types: `QueueError`, `ValidationError`.

1. **Binding.** Binding Heta components are based on internal cross references. Checking references. Checking required properties.

    The errors which happen at the stage will be of types: `BindingError`.

1. **Testing circular references.** In general case some of references (like `assignments` in `@Record` or `units` in `#defineUnit`) may be circular which is not allowed. On this step the compiler searches them and throws an error if found.

1. **Checking units consistency (optional).** Testing if left and right part of assignments has the same calculated unit.

1. **Checking units terms.** Checking the units requirement for specific components: @Compartment is volume or square, or length, etc.

1. **Other steps.** The next steps depend on a tool used. For example it is expected that `#export` actions induce the creation of code for export.

    The errors which happen at the stage will be of types: `ExportError`.

## Base structure of Heta container

- namespaceStorage (key/value dictionary)
    - namespace (key/value dictionary)
        - components
- unitDefStorage (key/value dictionary)
    - unit definition
- exportStorage (key/value dictionary)
    - export description

## Example

Structures in example are shown in Heta and JSON notation.

**Structure 1. Heta language code**

file: `index.heta`
```heta
s1 @Species { compartment: comp0 };

#include {source: ./addon.heta, type: heta};
```

file: `addon.heta`
```
s1 'Species number one';

#export { format: SBML, filepath: sbml };
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
            "action": "include",
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
            "action": "export",
            "format": "SBML",
            "filepath": "sbml"
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
        "action": "export",
        "format": "SBML",
        "filepath": "sbml"
    }
]
```

**Structure 4. Heta container**

```json
{
    "namespaceStorage": {
        "nameless": {
            "s1": {"class": "Species", "compartment": "comp0", "title": "Species number one"}
        }
    },
    "unitDefStorage": {},
    "exportStorage": {
        "some_random_id": {"class": "SBMLExport", "filepath": "sbml"}
    }
}
```

**Structure 5. Heta container (bound)**

```json
{
    "namespaceStorage": {
        "nameless": {
            "s1": {
                "class": "Species",
                "compartment": "comp0",
                "title": "Species number one",
                "compartmentObj": {...} // direct reference to the compartment comp0
            }
        }
    },
    "unitDefStorage": {
    },
    "exportStorage": {
        "some_random_id": {
            "class": "SBMLExport",
            "filepath": "sbml"
        }
    }
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
            metaid = "nameless::s1"
            compartment = "comp0"
            name = "Species number one"
            boundaryCondition = "false"
            constant = "false",
            hasOnlySubstanceUnits = "false"
        />
    </listOfSpecies>
</sbml>
```
