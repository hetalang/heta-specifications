[![Heta project](https://img.shields.io/badge/%CD%B1-Heta_project-blue)](https://hetalang.github.io/)
[![GitHub release](https://img.shields.io/github/release/hetalang/heta-specifications.svg)](https://github.com/hetalang/heta-specifications/releases/)
[![GitHub issues](https://img.shields.io/github/issues/hetalang/heta-specifications.svg)](https://GitHub.com/hetalang/heta-specifications/issues/)
[![DOI:10.13140/RG.2.2.18935.06563](https://zenodo.org/badge/DOI/10.13140/RG.2.2.14881.35682.svg)](https://doi.org/10.13140/RG.2.2.14881.35682)
[![License: CC BY-ND 4.0](https://img.shields.io/badge/License-CC%20BY--ND%204.0-lightgrey.svg)](http://creativecommons.org/licenses/by-nd/4.0/)

See more information on the [Heta project page](https://hetalang.github.io/).

**Heta** language is a domain-specific programming language (DSL) for dynamic quantitative models used in quantitative systems pharmacology (QSP) and systems biology (SB). It describes the biological systems as the components of QSP/SB modeling platform.

The Heta language represents the dynamic model as a set of interacting components describing volumes, concentrations, amounts, rates, and others. You don't need to writing an ODE system manually. The differential equations are compiled "on the fly" where they are required.

The key idea of Heta language is the development and editing of the bio-mathematical models as expressive programming code which gives us the benefits of programming languages: compact and human-readable format, modularity, reusability, etc. The model development can be easily organized as a workflow inside a group. The Heta code is friendly for version control systems and different continuous integration and delivery (CI/CD) tools can be used to reach maximal productivity.

The important pre-formulated requirements to the language were:

-  Human-readable/writable code can be used for model creation, modification, or integration.
-  Easy code parsing and transformation for potential implementation into different tools and frameworks.
-  Modularity: QSP platform can be sub-divided into several files and spaces for better project management.
-  Polymorphism of code: code can be presented in different formats with the same meaning.
-  Reusability: modeling platforms should be easily extended. Model parts and datasets can be used for other models and applications.
-  Reach annotation capabilities for better code revision and reporting.
-  The easy transformation from/to different formats of platforms and models currently used in practice: SBML, DBSolve, tables, databases, mrgsolve, Simbiology, etc.

## "Hello world!" example

The example describes a simple model with two species and one mass-action law reaction.

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

*See more examples on [Cases page](https://hetalang.github.io/#/specifications/cases).*

## Origin

Heta language was originally developed for internal projects of InSysBio to support the development of large-scale QSP modeling platforms and storing them in database-like formats.

## Maintainers

- Evgeny Metelkin

## Versions

The **latest** version of Heta standard is located in **master** branch of the [hetalang/heta-specifications](https://github.com/hetalang/heta-specifications) repository. 

The published versions are stored as [**tags**](https://github.com/hetalang/heta-specifications/releases) in the repository.

Heta specifications versions follow [Semantic Versioning](https://semver.org/) standard. While the Heta standard is still under active development the MAJOR number is 0 and will not be changed before publishing the stable release. Each substantial change in Heta syntax or interpretation will update the MINOR number. The PATCH number means update in the format description without rules changes.

## License
The Heta language specifications are licensed under [Creative Commons Attribution-NoDerivatives 4.0 International License](http://creativecommons.org/licenses/by-nd/4.0/).

InSysBio, 2018-2024
