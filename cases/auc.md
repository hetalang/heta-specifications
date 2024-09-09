# AUC

Model describes the example how to include simulation of AUC

```heta
// simple one-compartment model
comp1 @Compartment .= 5.5;
S @Species { compartment: comp1 } .= 10;
elimination @Reaction { actors: S => };
elimination := kel * S * comp1;

// including the variable reperesenting AUC
// this corresponds to ODE: d(auc)/dt = pr1 = S, auc(0) = 0
auc @Record .= 0;
pr1 @Process { actors: => auc };
pr1 := S;
```

