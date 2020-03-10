# Namespaces

1. Heta uses simple not-nested spaces of names.

1. If space is not set than the component refer to `nameless` (default) namespace.

1. Namespaces is intended for:
  - storing several independent models in one platform;
  - sharing model components;

1. A platform may have any number of namespaces and one `nameless` namespace.

1. Space and id form index of a component which is the coordinate of component in platform. A component in space `one` and id `p1` have an index `one::p1`. A component in `nameless` space and id `k1` have and index `k1`. 
