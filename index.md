# Heta Language Specifications

**Heta** is a modeling language for quantitative systems pharmacology and systems biology.

The current version is [![GitHub release](https://img.shields.io/github/release/hetalang/heta-specifications.svg)](https://github.com/hetalang/heta-specifications/releases/).

See also the [Heta video tutorial](../resources/?id=lesson-0-introduction)

## Content

- **Intro**
- [Syntax](./syntax.md)
- [Tabular format](./tabular-format.md)
- [Actions](./actions.md)
- [Classes](./classes.md)
- [Modules](./modules.md)
- [Namespaces](./namespaces.md)
- [Math expressions](./math.md)
- [Null values](./null.md)
- [Units](./units.md)
- [Cases](./cases.md)
- [Compilation steps](./compilation.md)
- [Change Log](./changelog.md)


## Features and philosophy

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

*See more examples on [Cases page](./cases).*

### Explanations

Comments are denoted by a double slash.
```heta
// Hello world! example
```

This creates the volume (compartment) with id "comp1" in the "nameless" namespace.
```heta
comp1 @Compartment;
```

This code creates two species: "A", "B" inside "comp1".
```heta
A @Species { compartment: comp1 };
B @Species { compartment: comp1 };
```

This code creates reaction A -> 2B, where one molecule of A is transformed to two molecules of B.
```heta  
r1 @Reaction { actors: A -> 2B };
```

This string sets the initial value (assignment at initialization step) for compartment volume.
```heta
comp1 .= 1;
```

This sets the initial values for species concentrations (dynamic values).
```heta
A .= 10;
B .= 0;
```

This line sets reaction law.
```heta
r1 := k1*A*comp1;
```

This creates a component that can be described by number and sets the value 0.01 at the same line.
```heta
k1 @Const = 0.01;
```

## Versions

The **latest** version of Heta standard is located in **master** branch of the [hetalang/heta-specifications](https://github.com/hetalang/heta-specifications) repository. 

The published versions are stored as [**tags**](https://github.com/hetalang/heta-specifications/releases) in the repository.

Heta specifications versions follow [Semantic Versioning](https://semver.org/) standard. While the Heta standard is still under active development the MAJOR number is 0 and will not be changed before publishing the stable release. Each substantial change in Heta syntax or interpretation will update the MINOR number. The PATCH number means update in the format description without rules changes.

## License
The Heta language specifications are licensed under [Creative Commons Attribution-NoDerivatives 4.0 International License](http://creativecommons.org/licenses/by-nd/4.0/).

InSysBio, 2018-2024
