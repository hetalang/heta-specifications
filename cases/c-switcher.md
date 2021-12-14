# CSwitcher

Here `x1` and `p1` values will be updated each time when `6-x1` threshold zero from negative to positive.

```heta
k1 @Const = 0.01;

comp1 @Compartment .= 1;
x1 @Species { compartment: comp1 , output: true} .= 10;
x2 @Species { compartment: comp1, output: true } .= 0;
r1 @Reaction { actors: x1->x2 } := k1*x1*comp1;
p1 @Record {output: true} .= 10;
x3 @Species { compartment: comp1, output: true } .= 12.12;

// switcher
sw1 @CSwitcher {
  trigger: 6-x1,
  active: true
};
x1 [sw1]= x1 + 10;
p1 [sw1]= x1 * 2;
```
