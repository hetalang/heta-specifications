# Multiple compartments

A dynamic model with several compartments and species.

See more details in **"Heta tutorial" #4**

## Figure
![multiple-compartments](https://raw.githubusercontent.com/hetalang/heta-specifications/master/cases/multiple-compartments.png)

## Code

```heta
comp1 @Compartment .= 1;
c1 @Species {compartment: comp1} .= 10;

comp2 @Compartment .= 2;
c2 @Species {compartment: comp2} .= 0;
a2 @Species {compartment: comp2, isAmount: true} .= 0;

comp3 @Compartment .= 3;
c3 @Species {compartment: comp3} .= 0;

// reactions
r1 @Reaction {actors: c1 = c2} := k1 * c1 * comp1;
r2 @Reaction {actors: c2 = c3 + a2} := k2 * c2 *comp2;
r3 @Reaction {actors: c3 = } := k3 * c3 * comp3;

// constants
k1 @Const = 0.01;
k2 @Const = 0.02;
k3 @Const = 0.03;

#export {format: Matlab, filepath: model};
```
