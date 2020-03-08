
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
- [Versions](versions)
- [Change Log](changelog)

## Features and philosophy
**Heta** language is a domain-specific programming language (DSL) for dynamic quantitative models used in quantitative systems pharmacology (QSP) and systems biology (SB). It describes the biological systems as the components of QSP/SB modeling platform. The scenarios, annotation and configuration files are parts of the platform.

Heta language represents the dynamic model as a set of interacting components which describe volumes, concentrations, amounts, rates and other components. The most native representation of Heta code is ordinary differential equations (ODEs) but Heta does not require writing ODE system manualy. The differential equations are compiled "on fly" when they are required.

The key idea of Heta language is the development and editing the bio-mathematical models as expressive programming code which gives us the benefits of programming languages like: compact and human readable format, modularity, code reuse, etc. The model development can be easily organized as workflow inside group. Heta code is friendly for version control systems and different continuous integration and continuous integration (CI/CD) tools can be used to reach maximal productivity.

The development of Heta language was based on the following ideas:

-	Everything is code; model, tasks and experimental observations are also the code;
-	Describe components but not the algorithms;
-	Annotation matters.

The important requrements to the language are:

-	Human-readable/writable code can be used for model creation, modification or integration.
-	Easy code parsing and transformation for potential implementation into different tools and frameworks.
-	Modularity: QSP platform can be subdivided into several files and spaces for better project management.
-	Reused models and datasets are helpful for the development of modeling platforms and increase efficiency.
-	Reach annotation capabilities for better code revision and reporting.
-	Easy transformation from/to different formats of platforms and models currently used in practice: sbml, dbsolve, excel spreadsheets, mrgsolve, simbiology, mrgsolve, etc.

## Hello world example with explanations
*A set of additional examples can be found in [Cases page](cases).*

The example describes a simple model with two species and one mass-action reaction. The following code definitely describes the model and can be translated to SBML, simbiology and mrgsolve code. The model is published on GitHub: <https://github.com/insysbio/heta-case-mini> as an example of open-source QSP modeling platform.

```Heta
// Hello world! example
comp1 @Compartment;
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
r1 @Reaction { actors: A->B };

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
This code creates two species: "A", "B" inside compartment.
```heta  
r1 @Reaction { actors: A->B };
```
This code creates reaction A->B
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
Evgeny Metelkin

## Versions

The **latest** published version of Heta standard is stored in **master** branch and can be seen on [web site](https://hetalang.github.io/).

The previous versions are stored as **tags**.

## License
The Heta language specification is licensed under a <a rel="license" href="">[Creative Commons Attribution-NoDerivatives 4.0 International License](http://creativecommons.org/licenses/by-nd/4.0/).

[![Creative Commons License](https://i.creativecommons.org/l/by-nd/4.0/80x15.png)](http://creativecommons.org/licenses/by-nd/4.0/)

InSysBio, 2018-2020
