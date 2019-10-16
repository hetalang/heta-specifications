# Heta transformation workflow

Heta code is transformed by the following steps:

1. **Parsing.** Heta language [code](syntax) from text files is translated to the      collection of [Modules](modules). 

    Parsing starts from single *index* file, than other files is added recursively  based on `#import` actions. Each file represents one module.

    Syntax checking can be performed on this step.

1. **Modules integration.** Collection of modules are combined into single list (sequence) of [Actions](actions) (array-like format).

    Modules must not have circular references.

1. **Translation.** Actions are translated sequentially to form [declarative Heta](heta-declarative) structures (dictionary-like format).

1. **Binding.** Binding Heta components based on internal cross references. 

    Check references. Check required properties.

## Example

Structures in example are shown in JSON notation.

**Step 1. Heta code**

file: `index.heta`
```heta
s1::one @Species { compartment: comp0 };

#import { source: ./addon.heta, type: heta };
```

file: `addon.heta`
```
s1::one 'Species number one';

sbml @SBMLExport { model: one };
```

**Step 2. Modules collection**

```json
{
    "Y:/platform/src/index.heta" : [
        {
            "action": "upsert",
            "class": "Species",
            "id": "s1",
            "space": "one",
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
            "space": "one",
            "title": "Species number one"
        },
        {
            "action": "upsert",
            "id": "sbml",
            "model": "one"
        }
    ]
}
```

**Step 3. Action sequence**

```json
[
    {
        "action": "upsert",
        "class": "Species",
        "id": "s1",
        "space": "one",
        "compartment": "comp0"
    },
    {
        "action": "upsert",
        "id": "s1",
        "space": "one",
        "title": "Species number one"
    },
    {
        "action": "upsert",
        "class": "SBMLExport",
        "id": "sbml",
        "model": "one"
    }
]
```

**Step 4. Declarative Heta**

```json
{
    "one::s1": {
        "class": "Species",
        "compartment": "comp0",
        "title": "Species number one"
    },
    "sbml": {
        "class": "SBMLExport",
        "model": "one"
    }
}
```

**Step 5. References**

```json
    "one::s1": {
        "class": "Species",
        "compartment": "comp0",
        "title": "Species number one",
        "compartmentObj": {#ref-to-comp1-component}
    },
    "sbml": {
        "class": "SBMLExport",
        "model": "one"
    }
```

**Step 5. Export**

file: `sbml.xml`

```XML
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<sbml 
    xmlns = "http://www.sbml.org/sbml/level2/version4"
    xmlns:xhtml="http://www.w3.org/1999/xhtml"
    level="2" version="4">
    <listOfSpecies>
        <species
            id = "s1"
            metaid = "one::s1"
            compartment = "comp0"
            name = "Species number one"
            boundaryCondition = "false"
            constant = "false",
            hasOnlySubstanceUnits = "false"
        />
    </listOfSpecies>
</sbml>
```
