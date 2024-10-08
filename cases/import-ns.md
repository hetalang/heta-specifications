# Import namespace

file: `index.heta`

```heta
// create drug pk template in separate namespace
abstract namespace drug begin
  gut @Compartment .= 1; // value is not used
  central @Compartment .= 5.2; // central compartment
  a0 @Species {compartment: gut, isAmount: true} .= 0;
  c1 @Species {compartment: central} .= 0;
  vabs @Reaction {actors: a0 = c1};
  vabs := kabs * a0;
  vel @Reaction {actors: c1 = };
  vel := kel * c1 * central;
end

// create drug #1 and change compartment name
#importNS {
  fromSpace: drug,
  suffix: _first,
  rename: {gut: gut, central: blood}
};
kabs_first @Const = 1e-3;
kel_first @Const = 0.1;

// create drug #2 and change compartment name
#importNS {
  fromSpace: drug,
  suffix: _second,
  rename: {gut: gut, central: blood}
};
kabs_second @Const = 2e-3;
kel_second @Const = 0.2;

// create drug #3 and change compartment name
#importNS {
  fromSpace: drug,
  suffix: _third,
  rename: {gut: gut, central: blood}
};
kabs_third @Const = 3e-3;
kel_third @Const = 0.3;
```