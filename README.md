[![Heta project](https://img.shields.io/badge/%CD%B1-Heta_project-blue)](https://hetalang.github.io/)
[![GitHub release](https://img.shields.io/github/release/hetalang/heta-specifications.svg)](https://github.com/hetalang/heta-specifications/releases/)
[![GitHub issues](https://img.shields.io/github/issues/hetalang/heta-specifications.svg)](https://GitHub.com/hetalang/heta-specifications/issues/)
[![DOI:10.13140/RG.2.2.18935.06563](https://zenodo.org/badge/DOI/10.13140/RG.2.2.14881.35682.svg)](https://doi.org/10.13140/RG.2.2.14881.35682)

**Heta language** is a domain specific programming language (not general-purpose) for dynamic quantitative models used in quantitative systems pharmacology and systems biology.

## Language specification

- [Syntax](syntax)
- [Classes](classes)
- [Actions](actions)
- [Namespaces](namespaces)
- [Include statement](include)
- [Math expressions](math)
- [Compilation steps](compilation)
- [Cases](cases)
- [Change Log](changelog)

## Features and philosophy
**Heta** language is a domain-specific programming language (DSL) for dynamic quantitative models used in quantitative systems pharmacology (QSP) and systems biology (SB). It describes the biological systems as the components of QSP/SB modeling platform.

Heta language represents the dynamic model as a set of interacting components which describe volumes, concentrations, amounts, rates and others. The Heta code can be represented as ordinary differential equations (ODEs) but Heta does not require writing ODE system manualy. The differential equations are compiled "on fly" when they are required.

The key idea of Heta language is the development and editing the bio-mathematical models as expressive programming code which gives us the benefits of programming languages like: compact and human readable format, modularity, code reuse, etc. The model development can be easily organized as workflow inside group. Heta code is friendly for version control systems and different continuous integration and continuous integration (CI/CD) tools can be used to reach maximal productivity.

The development of Heta language was based on the following ideas:

-  Everything is code; model, tasks and experimental observations are also the code;
-  Describe components but not the algorithms;
-  Annotation matters.

The important pre-formulated requrements to the language were:

-  Human-readable/writable code can be used for model creation, modification or integration.
-  Easy code parsing and transformation for potential implementation into different tools and frameworks.
-  Modularity: QSP platform can be subdivided into several files and spaces for better project management.
-  Reused models and datasets are helpful for the development of modeling platforms and increase efficiency.
-  Reach annotation capabilities for better code revision and reporting.
-  Easy transformation from/to different formats of platforms and models currently used in practice: sbml, dbsolve, excel spreadsheets, mrgsolve, simbiology, mrgsolve, etc.

## "Hello world!" example
*A set of additional examples can be found in [Cases page](cases).*

The example describes a simple model with two species and one mass-action reaction. The following code definitely describes the model and can be translated to SBML, simbiology and mrgsolve code. The model is published on GitHub: <https://github.com/insysbio/heta-case-mini> as an example of open-source QSP modeling platform.

```heta
// Hello world! example
comp1 @Compartment;
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
r1 @Reaction { actors: A -> 2B };

comp1 .= 1;
A .= 10;
B .= 0;
r1 := k1*A*comp1;
k1 @Const = 0.01;
```
### Explanations
```
// Hello world! example
```
Comments are denoted by double slash.
```heta
comp1 @Compartment;
```
This creates compartment with id "comp1" in nameless namespace.
```
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
```
This code creates two species: "A", "B" inside "comp1".
```heta  
r1 @Reaction { actors: A -> 2B };
```
This code creates reaction A -> 2B, where one molecule of A transforms to two molecules of B.
```
comp1 .= 1;
```
This string sets initial value (assignment at initialization step) for compartment volume.
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
k1 @Const = 0.01;
```
This creates a component that can be described by number and sets the value 0.01 at the same line.

## Origin
Heta language was originally developed for internal projects of InSysBio LLC to support development of large-scale QSP modeling platforms and storing them in database-like formats.

## Maintainers
- Evgeny Metelkin

## Versions

The **stable version** is published on <https://hetalang.github.io/#/specifications/> and stored as branch [v0.2](https://github.com/hetalang/heta-specifications/tree/v0.2).

The **latest** version of Heta standard is published as separate page <https://hetalang.github.io/heta-specifications/> and stored in **master** branch of the [hetalang/heta-specifications](https://github.com/hetalang/heta-specifications) repository. 

The previous versions are stored as **tags** in the repository.

Heta specifications versions follow [Semantic Versioning](https://semver.org/) standard. While the Heta standard is still under active development the MAJOR number is 0 and will not be changed before publishing an stable release. Each substantial change in Heta syntax or interpretation will update the MINOR number. The PATCH number means update in the format description without rules changes.

## License
The Heta language specification is licensed under a <a rel="license" href="">[Creative Commons Attribution-NoDerivatives 4.0 International License](http://creativecommons.org/licenses/by-nd/4.0/).

[![Creative Commons License](https://i.creativecommons.org/l/by-nd/4.0/80x15.png)](http://creativecommons.org/licenses/by-nd/4.0/)

InSysBio, 2018-2020
