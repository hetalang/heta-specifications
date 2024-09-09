# Annotation

Annotation of components in Michaelis-Menten model.

file: `annotation.heta`
```Heta
include ./qsp-units.heta;

Vmax @Const = 0.1;
Km @Const = 2.5;

'''Note for the Default compartment'''
default_comp @Compartment 'Default compartment' { units: L };
default_comp .= 1;

S @Species 'substrate' { compartment: default_comp, units: uM };
P @Species 'product' { compartment: default_comp, units: uM };

''' Transformation of __substate__ to __product__ '''
r1 @Reaction 'Michaelis-Menten reaction' {
  actors: S -> P,
  units: umole/min
};

r1 := Vmax*S/(Km+S)*default_comp; // S is used here but assigned below
S .= 10; // S should be initialialized after creation
P .= 0;
```