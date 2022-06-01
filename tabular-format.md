# Tabular format

The Heta standard includes different forms of modeling platform representation.
All formats share the same meaning and similar properties where possible.

For many reasons the tabular representation can be useful for the model development: 

- Easier for those who are unfamiliar with the Heta syntax;
- Code parser is not required for external tools;
- Simplify model content review and annotation;
- Excel and other table processor utilization.

_See also [format comparison example](cases/format-comparison#table-format)._

## Basics

1. The content of the tables can be loaded with [include statement](syntax#include-statement) of the `table` type. There are also addition properties for this type, see [Modules](modules#table-module) chapter.

    Example:
    ```heta
    #include {source: ./components.xlsx, type: table, sheet: 0, omitRows: 1};
    ```

1. The modeling platforms can be created from single Heta table, several tables, or with mixture of modules of different types without restrictions.

1. Colors, fonts and other decoration available for specific table processors do not play a role for modeling platform meaning.

1. The first raw of the table is the header. It describes the meaning of cells content of other raws. The header syntax follows the [rules](#header-syntax-rules) for describing component properties and sub-properties.

1. Each row represents a single Heta action. Empty rows will be skipped. Rows with `on` property set to `0` or _empty_ will be skipped as well.

1. Each cell represents the property value or values. Empty cell means the value is not set.

1. Boolean values in cells can be set with `false`, `true` values or equivalently `0`, `1`

1. Simple string or numeric arrays can be declared with values divided by semicolon: `A;B;C` or `1;2;3`

1. The quotation for strings in cells should not be applied in opposite to Heta code syntax.

## Header syntax rules

- Simple property (of first level) like `title`, `id`, `num` should be set in the header as a string.
    Example:
    | id | class | num | title |
    |---|---|---|---|
    | k1 | Const | 1.1 | rate constant 1 |
    | k2 | Const | 0.1 | |

    This table is an equivalent of the following plain Heta:
    ```heta
    {id: k1, class: Const, num: 1.1, title: rate constant 1};
    {id: k2, class: Const, num: 0.1};
    ```
    And the same in shortened version:
    ```heta
    k1 @Const = 1.1 'rate constant 1';
    k2 @Const = 0.1;
    ```

- Sub-properties like `start_`, `ode_` in `assignments`, etc. can be declared with dot symbol.
    Example:
    | id | class | assignments.start_ | assignments.sw1 |
    |---|---|---|---|
    | x1 | Record | 10 | 100 |
    | x2 | Record | 0.1 | |

    This table is an equivalent of the following plain Heta:
    ```heta
    {id: x1, class: Record, assignments: {start_: 10, sw1: 100}};
    {id: x2, class: Record, assignments: {start_: 0.1}};
    ```
    And the same in shortened version:
    ```heta
    x1 @Record .= 10 [sw1]= 100;
    x2 @Record .= 0.1;
    ```

- Arrays can be declared with square brackets

    Example:
    | id | class | num | tags[] |
    |---|---|---|---|
    | coef1 | Const | 1e-3 | A;B;C |

    This table is an equivalent of the following plain Heta:
    ```heta
    {id: coef1, class: Const, num: 1e-3, tags: [A,B,C]};
    ```
    And the same in shortened version:
    ```heta
    coef1 @Const = 1e-3 {tags: [A,B,C]};
    ```

- There is one specific header `on`. The value `0` or _empty_ in correspondent cells means "skip this row". It is the mechanism to mimic a single-line comment or a header.

    Example:
    | on | id | class | num | title |
    |---|---|---|---|---|
    | 1 | k1 | Const | 1.1 | this will not be skipped |
    | 0 | k2 | Const | 0.1 | this will be skipped |

    This is equivalent to the following:
    ```heta
    k1 @Const = 1.1 'this will not be skipped';
    ```