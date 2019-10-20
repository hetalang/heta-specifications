# Namespaces

1. Heta uses simple not-nested spaces of names.

1. A platform may have any number of namespaces and on global (anonimous) namespace.

1. Space and id form index of a component which is the coordinate of component in platform. A component in space `one` and id `p1` have an index `one::p1`. A component in anonimous space and id `k1` have and index `k1`. 

1. The components from anonimous namespace can be referenced from any namespace. The components from specific namespace can be referenced from the same namespace

    **Example 1:**

    ```heta
    k1 @Const = 1.1;
    one:S @Species .= 0;

    one::p1 @Record := k1 * S;
    ```

1. The current Heta version uses classes which is strongly assosiated with global namespace (unscoped classes): `Const`, `Page`, etc.; or local namespace (scoped classes): `Record`, `Species` etc. *The future Heta version will not have these limmitations.*
