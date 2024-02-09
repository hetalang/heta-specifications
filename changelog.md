# Change Log

## 0.4.5

- use `0`, `1` together with `true`, `false` for boolean
- `#deleteNS` action
- use of `;` everywhere
- update function list: log(x, b) -> logbase(x, b), remove nthRoot(x), hyperbolic functions support

## 0.4.4

- use `ProcessExpr` of type: A>B
- use Regular Expression in `spaceFilter` of `#export` action
- default values for `filepath` of `#export` action
- minor text fixes

## 0.4.3

- add `#defineFunction` action as an experimental

## 0.4.2

- use `import xxx.xlsx type table` instead of  `import xxx.xlsx type xlsx`
- add `@TimeScale`
- remove `@Dose` class
- Create explanation of "special string formats"
- Add `#setScenario` action

## 0.4.1

- `@StopSwitcher` as an experimental class
- remove `@SimpleTask`
- `piesewise()` function inside MathExpr (experimental)

## 0.4.0

- `reversible` property for `@Process`
- substitute `#include @UnitDef` instance by `#defineUnit` action

## 0.3.4

- include `atStart` in _Switcher class
- add cases
- add @Const properties: scale, lower, upper
- add `output` properties to `@Record`

## 0.3.3
- set @Dose class as experimental
- remove experimental from @CSwitcher
- multiple terminology updates: "action statement" 

## 0.3.2

- use `trigger` property with MathExpr in `CSwitcher` (continuous switcher) instead of condition referred to Record

## 0.3.1

- rename `CondSwitcher` to `CSwitcher` (continuous switcher)
- add `DSwitcher` class (discrete switcher)
- add ternary operator
- extend modules discription

## 0.3.0

- replace `@Export` class by `#export` action
- add units page
- add modules case
- add AUC case
- remove abstract classes: no abstract classes at all
- text corrections
s
## 0.2.5

- Rename `UnitDefinition` to `UnitDef` class
- Add class _Size
- Clarify namespaces usage: `#setNS`, `#importNS`, `#import`. Explain `abstact` namespace.
- Extend classes description
- add cases
- Multiple text corrections

## 0.2.4

- update list of core math functions
- add sheet (number) option in include statement

## 0.2.3

- add `include file.heta type heta with {}` statement instead of `import` action

## 0.2.2

- mark namespace as experimental (unstable) feature

## 0.2.1

- more details to actions chapter
- update versions table
- rename "transformation workflow" to "compilation steps" and extend description.
- add error names

## 0.2.0

- all classes instances can be in nameless and in non-nameless space;
- remove _Simple ans _Scoped class, use Component as top class for all indexed components;
- change the properties of _Export class: remove "model" property;
- allow nameless namespace elements for exports;
- add description for _Export classes: SLVExport, JSONExport, YAMLExport, SimbioExport, MrgsolveExport.

## 0.1.0 - first release

- main components are desrcibed (draft)
