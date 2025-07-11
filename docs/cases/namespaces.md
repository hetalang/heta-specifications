# Namespaces 

This example demonstrate the usage of "namespaces" in Heta and how it can be used for creating of different models.

## Shared files

The following two files are heta modules.
They will be used to create different models.

```heta
/* cell-dynamics.heta file */

blood @Compartment .= 5.3;
cell_count @Species { compartment: blood } .= 1;
v_prol @Reaction { actors: => cell_count } 
    := k_prol * cell_count;

k_prol @Const = 1e-3;
```

```heta
/* intracell.heta file */

cell_vol @Compartment := 1e-6 * cell_count;
m1 @Species { compartment: cell_vol } .= 0;
m2 @Species { compartment: cell_vol } .= 0;

vsyn_m1 @Reaction { actors: => m1 } 
    := ksyn * cell_vol;
v_m1_m2 @Reaction { actors: m1 => m2 } 
    := kcat_m1 * m1 / (Km1 + m1) * cell_vol;
vtr_m2 @Reaction { actors: m2 => } 
    := P * S * m2;

ksyn @Const = 1e-2;
kcat_m1 @Const = 1e-1;
Km1 @Const = 12;
P @Const = 1.2;
S @Const = 1e-8;
```

```heta
/* index.heta file */

#include { source: intracell.heta };
#include { source: cell-dynamics.heta };
```

![namespaces-1](./namespaces-1.png)

## Creating two models in one platform

```heta
/* index.heta */

#include { source: intracell.heta };
#include { source: cell-dynamics.heta };

// add new species in blood
m3 @Species { compartment: blood } .= 0;
// update transport
vtr_m2 { actors: m2 => m3 }
  := P * S * (m2- m3);

namespace intracell begin
  #include { source: intracell.heta };
  cell_count @Const = 1;
end
```

![namespaces-2](./namespaces-2.png)

## Cloning models with import

```heta
/* index.heta */
#include { source: intracell.heta };
#include { source: cell-dynamics.heta };

// add new species in blood
m3 @Species { compartment: blood } .= 0;
// update transport
vtr_m2 { actors: m2 => m3 }
  := P * S * (m2- m3);

#setNS {space: modified};
#importNS { space: modified, fromSpace: nameless };
modified::vcl_m3 @Reaction { actors: m3 => }
  := kcl_m3 * m3 * blood;
modified::kcl_m3 @Const = 1e-2;
```

![namespaces-3](./namespaces-3.png)

## Cloning models with renaming components

```heta
/* index.heta */
#include { source: intracell.heta };
#include { source: cell-dynamics.heta };

// add new species in blood
m3 @Species { compartment: blood } .= 0;
// update transport
vtr_m2 { actors: m2 => m3 }
  := P * S * (m2 - m3);

#setNS {space: modified};
#importNS {
  space: modified, fromSpace: nameless,
  suffix: _1, rename: {m3: m3}
};
#importNS {
  space: modified, fromSpace: nameless,
  suffix: _2, rename: {m3: m3}
};
#importNS {
  space: modified, fromSpace: nameless,
  suffix: _3, rename: {m3: m3}
};

modified::ksyn_3 = 1e-8;
```

![namespaces-4](./namespaces-4.png)
