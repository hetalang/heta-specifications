# Include statement

`include` is Heta statement which allows to unite several files into one platform. 
It is designed for two objectives:
- split the whole platform to subfiles for better code management;
- allow whiting code in various formats.

1. **Heta-file** means file that can be translated to Heta platform: xlsx, sbml, json, etc. 

1. The identifier of the heta-file is the absolute path of file in file system. For example:  `y:/the-platform/src/index.heta`.

1. A file can be loaded also by `include` action, but it is not recommended. See [actions](./actions#include).

1. `include` has `type` property. The default type is `heta` and it means the file includes just regular Heta code.

1. `include` can set additional parameters after `with` keyword in dictionary format

    ```heta
    include file.xlsx type xlsx with {
        options: { sheetNum: [1, 2], omitRows: 3 }
    }
    ```

1. If you use another file type: `xlsx`, `dbsolve`, this means the code from the files will be parsed and transformed to heta format and can be used as regular Heta code. See [workflow](./workflow).

## Example 1
**file:** `index.heta`
```heta
include ./1.heta type heta
include ./2.json type json

k1 @Const = 1e-3;

sbml @SBMLExport;
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
