
# One compartment PK model

file: `one-compartment.heta`

```heta
F @Const = 0.97;
dose @Const = 10;
kabs @Const = 1e-2;
kel @Const = 1.2e-3;

gut @Compartment .= 1;
Vd @Compartment .= 5;
a0 @Species {
    compartment: gut,
    isAmount: true
};
c1 @Species {
    compartment: Vd
};
r_abs @Reaction { actors: a0 => c1 };
r_el @Reaction { actors: c1 => };

a0 .= F * dose;
c1 .= 0;
r_abs := kabs * a0;
r_el := kel * c1 * Vd;

// this creates SBML formatted file
sbml @SBMLExport { filepath: sbml_output };
```
