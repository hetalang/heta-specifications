# Heta modules

Splitting the model to three files to create SBML file.

file: `main.heta`
```heta
// load base model
include ./model.heta
// add updated values of constants
include ./constants.heta

#export {
    format: SBML,
    filepath: sbml_model
};
```

file: `model.heta`
```heta
x1 @Record .= 10;
x2 @Record .= 0;
x3 @Record .= 0;

pr1 @Process { actors: x1 -> x2 } := k1 * x1;
pr2 @Process { actors: x2 -> x3 } := k2 * x2;

k1 @Const = 0.1;
k2 @Const = 0.2;
```

file: `constants.heta`
```heta
k1 = 0.005;
k2 = 0.11;
```
