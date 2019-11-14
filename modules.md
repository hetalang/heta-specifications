# Heta modules

Heta modules is designed for two objectives:
- split the whole platform to subfiles for better code management;
- allow whiting code in various formats.

1. In the current version of Heta the module is approximately the same as file. The identifier of the module is the absolute path of file in file system. For example:  `y:/the-platform/src/index.heta`.

1. The modules can be loaded by `import` action. See [actions](./actions#import).

1. The modules have `type` property. The default type is `heta` and it means the file includes just regular Heta code.

1. If you use another module type: `md`, `xlsx`, `dbsolve`, this means the code from the files will be parsed and transformed to heta module format and can be used as regular Heta code. See [workflow](./workflow).

## Example 1
**file:** `index.heta`
```heta
#import { source: ./1.heta, type: heta };
#import { source: ./2.json, type: json };

k1 @Const = 1e-3;

sbml @SBMLExport { };
```

**file:** `1.heta`
```heta
c1 @Compartment .= 1;
```

**file:** `2.json`
```json
[
    {
        "class": "Species",
        "id": "S",
        "assignments": {
            "start_": 0
        }
    }
]
```

The final result in the platform will be

```json
{
    "c1": {
        "class": "Compartment",
        "assignments": {
            "start_": 1
        }
    },
    "S": {
        "class": "Species",
        "assignments": {
            "start_": 0
        }
    },
    "k1": {
        "class": "Const",
        "num": 1e-3
    },
    "sbml": {
        "class": "SBMLExport"
    }
}
```
