# Namespaces

1. Heta uses simple not-nested spaces of names.

1. A platform include one default namespace`nameless` and any number of user defined namespaces.

1. If space is not set than the component refers to `nameless` (default) namespace.

1. Space and id form index of a component which is the coordinate of component in platform. A component in space `one` with id `p1` has index `one::p1`. A component in `nameless` space and id `k1` have and index `k1` which is the same as `nameless::k1`.

1. Namespaces is intended for:
  - storing several independent models in one platform;
  - re-using model components;

1. There are two ways to work with namespaces: using `namespace` statement and without it.

  **Example:**
  Working with `namespace` statement. This code creates the simple model in a created namespace "one".
  ```heta
  namespace one begin
    comp1 @Compartment .= 1.1;
    x1 @Species { compartment: comp1 } .= 10;
    x2 @Species { compartment: comp1 } .= 0;
    r1 @Reaction { actors: x1 => x2 } := k1*x1*comp1;
    k1 @Const = 1.1;
  end
  ```
  This code is equivalent to the following code without `namespace` statement.
  ```heta
  #setNS { space: one };
  one::comp1 @Compartment .= 1.1;
  one::x1 @Species { compartment: comp1 } .= 10;
  one::x2 @Species { compartment: comp1 } .= 0;
  one::r1 @Reaction { actors: x1 => x2 } := k1*x1*comp1;
  one::k1 @Const = 1.1;
  ```

1. There are two types of namespaces: `abstract` and `concrete`. The default namespace is `concrete` if not set in `#setNS` or `namespace` statement. If `abstract` namespace is set then checking and binding references is skipped in [compilation](compilation).

  **Example 1:**
  Setting concrete namespace using `namespace` statement.
  ```heta
  concrete namespace new begin // the same as "namespace new begin" 
    rule1 @Record := x*y;
  end
  ```
  Creating concrete namespace using `#setNS`
  ```heta
  #setNS { type: concrete, space: new }; // the same as "#setNS new::*;"
  new::rule1 @Record := x*y;
  ```

  **Example 2:**
  Setting abstract namespace using `namespace` statement.
  ```heta
  abstract namespace new begin
    rule1 @Record := x*y;
  end
  ```
  Creating abstract namespace using `#setNS`
  ```heta
  #setNS { type: abstract, space: new };
  new::rule1 @Record := x*y;
  ```
