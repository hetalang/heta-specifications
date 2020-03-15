# Time switcher

Model describes one-reaction model with periodic event.

```Heta
/*
  Simple model with one reaction
*/
comp1 @Compartment .= 1;
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
r1 @Reaction { actors: A => 2B };

// initialization
A .= 10;
B .= 0;
// rules
r1 := k1*A*comp1;
// constants
k1 @Const = 1.2e-1;

// This part describes periodic event 
// starting from 0 and then each 12 time units infinitely
evt1 @TimeSwitcher {
  start: 0,
  period: 12
};

/*
  event assignment
*/
// addition of 10 to A concentration (not amount)
A [evt1]= A + 10;

// exports
sbml1 @SBMLExport { filepath: sbml1_export };
```
