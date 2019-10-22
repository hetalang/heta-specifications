
[![Gitter](https://badges.gitter.im/hetalang/community.svg)](https://gitter.im/hetalang/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![GitHub release](https://img.shields.io/github/release/hetalang/hetalang.github.io.svg)](https://github.com/hetalang/hetalang.github.io/releases/)
[![GitHub issues](https://img.shields.io/github/issues/hetalang/hetalang.github.io.svg)](https://GitHub.com/hetalang/hetalang.github.io/issues/)
[![DOI:10.13140/RG.2.2.18935.06563](https://zenodo.org/badge/DOI/10.13140/RG.2.2.14881.35682.svg)](https://doi.org/10.13140/RG.2.2.14881.35682)

**Heta language** is a domain specific programming language (not general-purpose) for dynamic quantitative models used in quantitative systems pharmacology and systems biology.

## Language specification v0.1.0 (draft)

- [Syntax](syntax)
- [Classes](classes)
- [Actions](actions)
- [Namespaces](namespaces)
- [Heta modules](modules)
- [Math expressions](math)
- [Heta interpretation workflow](workflow)
- [Cases](cases)

## Features and philosophy
Heta language is a domain-specific programming language (DSL) for dynamic quantitative models used in quantitative systems pharmacology (QSP) and systems biology (SB).  It describes the biological systems as the components of QSP/SB modeling platform. The scenarios, annotation and configuration are parts of the platform.

The key idea of Heta language is the development and editing the biological models as expressive programming code which gives us the benefits of programming languages like: compact and human readable format, modularity, code reuse etc. as well as using version control systems or CI/CD tools. The development of Heta language was based on the following ideas:

-	Everything is code; model, tasks and experimental observations are also the code
-	Describe components but not the algorithms
-	Annotation matters

The important features of the language are:

-	Human-readable/writable code can be used for model creation, modification or integration.
-	Easy code parsing for potential implementation into different tools and frameworks.
-	Modularity: QSP platform can be subdivided into several modules for better project management.
-	Shared model parameters and datasets which are helpful for the development of modeling platforms and parameter identification.
-	Reach annotation capabilities for better code revision and reporting.
-	Easy transformation from/to different formats of platforms and models currently used in practice: sbml, dbsolve, excel spreadsheets, mrgsolve, simbiology.

## Hello world example with explanations
*A set of additional examples can be found in [Cases page](cases).*

The model describes a simple model with two species and one mass-action reaction. The following code definitely describes the model and can be translated to SBML, simbiology and mrgsolve code. The model is published on GitHub: <https://github.com/insysbio/heta-case-mini>

```Heta
// hello world example
namespace mod1 begin
  comp1 @Compartment;
  A @Species { compartment: comp1 };
  B @Species { compartment: comp1 };
  r1 @Reaction { actors: A->B };
  k1 @Record;

  comp1 .= 1;
  A .= 10;
  B .= 0;
  r1 := k1*A*comp1;
  k1 .= 0.01;
end
```
### Explanations
```
// hello world example
```
Comments are denoted by double slash.
```heta
namespace mod1 begin
...
end
```
The block creates namespace for the model. All components between begin, end are inside "mod1" model.
```heta
comp1 @Compartment;
```
This creates compartment with id "comp1" in "mod1" namespace
```
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
```
This code creates two species: "A", "B" inside compartment in "mod1" namespace.
```heta  
r1 @Reaction { actors: A->B };
```
This code creates reaction A->B
```
comp1 .= 1;
```
This string sets initial value (assignment at first step) for compartment volume.
```
A .= 10;
B .= 0;
```
Initialization of species (dynamic values) describing molecules concentration.
```
r1 := k1*A*comp1;
```
This line sets reaction rule.
```
k1 @Record .= 0.01;
```
This creates some value that can be described by numeric value in namespace "mod1" and set initial value 0.01 at the same line.

## Current state and implementation
Currently the draft version **v0.1.0** is published on Github-pages <https://hetalang.github.io/>

A series of tools supporting Heta language has been developed:
- Syntax highlighting VSCode: [heta-highlight-vscode](https://github.com/insysbio/heta-highlight-vscode) (published on GitHub )
- Syntax highlighting Atom editor: [heta-highlight-atom](https://github.com/insysbio/heta-highlight-atom) (published on GitHub)
- JavaScript framework with Heta support has been developed and tested: qs3p-js v0.3.0 (will be published soon)

The examples of Heta-based QSP platforms with CI/CD capabilitiew are shared on gitlab: 
- <https://github.com/insysbio/heta-case-mini> 
- <https://github.com/insysbio/faah-inhibitor>

The Heta language and Heta-based QSP framework were used in several “real world” projects for the development of middle-scale and large-scale models:
-	IRT database: <https://irt.insysbio.com> 
-	Alzheimer-consortium QSP platform
-	PK/RO simulator for anti-PD-1 mAbs (Shiny app): <https://insysbio.shinyapps.io/mAb-app/>
-	InSysBio internal projects with more than 1000 components each.

## Origin
Heta language was originally been developed for internal projects of InSysBio LLC to support development of large-scale QSP modeling platforms and storing them in database-like formats.

## Maintainers
Evgeny Metelkin

## License
The Heta language specification is licensed under a <a rel="license" href="">[Creative Commons Attribution-NoDerivatives 4.0 International License](http://creativecommons.org/licenses/by-nd/4.0/).

[![Creative Commons License](https://i.creativecommons.org/l/by-nd/4.0/80x15.png)](http://creativecommons.org/licenses/by-nd/4.0/)

InSysBio, 2018-2019
