# Syntax

See also the [Heta tutorial](https://hetalang.github.io/#/resources/?id=lesson-2-syntax)

The Heta code represents a sequence of statements that create and modify elements in a modeling platform. The parsing and interpretation of the code result in the creation of a static (database-like) structure representing the modeling platform, see [compilation](./compilation). There are many ways to write the same modeling system using Heta code, and a developer has the freedom to make their code more organized and readable.

## Action Statement

1. Action statements are separated by semicolons, and line breaks within and between statements do not affect interpretation; they can be used for code formatting.
    ```heta
    <statement one>; <statement two>;
    <statement 
        three>;
    ...
    ```
1. An action statement consists of a dictionary part and a set of shortened properties. The parts follow a priority rule, where the subsequent part has a higher priority. The list of part types includes:

    - Dictionary
    - Index
    - Class
    - Action
    - Title
    - Notes
    - Assignments (with several subtypes)

## Plain Format of Action Statement
1. The plain format is the most flexible way to describe action properties. It begins with the `{` symbol and ends with the `}` symbol, and it can contain a set of key-value pairs divided by commas (`,`), similar to a dictionary in JSON or YAML format.

    Example:
    ```heta
    { prop1: value 1, prop2: value 2, ...};
    ```

    The **property** is always a string without spaces. Properties must be unique within a dictionary. The **value** can be one of five types: \<String\>, \<Number\>, \<Boolean\>, \<Dictionary\>, or \<Array\>. \<Dictionary\> and \<Array\> values might include other types.


    Example:
    ```heta
    {
        stringProp: some text,
        numberProp: 1.2e-3,
        booleanProp: true,
        dictionaryProp: { nestedProp: true },
        arrayProp: [1, 2, 3]
    };
    ```

2. String values inside dictionaries start from the first non-space symbol and end with the non-space symbol before the stop-list: `, } ] @ # ' `. If you want to use symbols from the stop list inside a \<String\> value, use parentheses.

    Example:
    ```heta
    {
        prop1: Some string,
        prop2: "String with # stop-symbols @"
    };
    ```

3. There are [special string formats](#special-string-formats) used to describe specific properties of actions or classes. They follow the same \<String\> syntax but also have specific rules.

    Example:
    ```heta
    {
        id: r1, // follows the ID format
        class: Reaction,
        actors: A => B // follows the ProcessExpr format
    };
    ```

4. The \<Number\> value inside a dictionary is in \<Double\> format.

    
    Example:
    ```heta
    {
        prop1: 1,
        prop2: 1.2,
        prop3: 1.1e-2,
        prop4: 1.2E+3
    };
    ```

5. The \<Boolean\> value inside a dictionary may have two values: `true` or `false`.

    Example:
    ```heta
    {
        prop1: true,       // this is Boolean
        prop2: false,
        stringProp: "true" // this is String
    };
    ```

6. Nested \<Dictionary\> values follow the same rules as the plain format of an action statement and can be nested.

    Example:
    ```heta
    {
        prop: { nestedProp: { nextLevel: Hello! } } 
    };
    ```

6. The \<Array\> value is a sequence of numbered elements divided by commas. Numeration starts from 0 (zero) index.

    Example:
    ```heta
    {
        prop1: [],
        prop2: [1, 2, 3],
        prop3: [string, "string"],
        prop4: [[1, 2, 3], []],
        prop5: [{a: 1, b: 2, c: 3}, {}]
    };
    ```

## Shortened Format of Action Statement

1. To simplify code reading and writing, there are several types of statement parts that describe commonly used properties in a compact form. See the [Classes](./classes) description for details.

1. **Index** describes identifiers, such as `id` and `space` for components. The examples if indexes are: `space::id` or just `id` for nameless space.

    Example:
    ```heta
    one::k1 { prop1: some text };
    ```
    Which is equivalent to 
    ```heta
    { space: one, id: k1, prop1: some text };
    ```
    The specific form of index is "star index" which is helpful for some actions. This can be used when you do not want to state id.

    Example:
    ```heta
    #importNS target_ns::* { fromSpace: source_ns };
    ```
    which is equivalent to the following
    ```heta
    #importNS {space: target_ns, fromSpace: source_ns };
    ```
1. **Class** sets the `class` property. A particular class defines a list of properties that can be set. This property is denoted by the "@" symbol. The list of possible classes can be found in [Classes](./classes) description. Classes' names always start from the uppercase symbol. When you use class shortened property `@` the parser will replace the first lowercase symbol for the capital one.

    Example:
    ```heta
    k2 @const { num: 1.3 };
    ```
    Equivalent to:

    { id: k2, class: Const, num: 1.3 };
    ```
1. **Action** sets the `action` property. The `action` describes what to do with the statement, see [Actions](./actions). This property is denoted by the `#` symbol and is a required property in the base statement. Its default value is `upsert`.

    Example:
    ```heta
    #insert k3 @Const;
    k3 { num: 1 };
    ```
    Equivalent to:
    ```heta
    { action: insert, id: k3, class: Const };
    { action: upsert, id: k3, num: 1 };
    ```


1. **Title** sets the `title` property, denoted by single quotes `'`.

    Example:
    ```heta
    a 'Some title';
    ```
    which is equivalent to
    ```heta
    { id: a, title: Some title };
    ```

1. **Notes** sets the `notes` property, designated by three single quotes `'''`. The `notes` part is usually located before other parts.

    Example:
    ```heta
    ''' Some notes '''
    a @Const;
    ```
    which is equivalent to the following plain version
    ```heta
    { notes: Some notes, id: a, class: Const };
    ```

## Shortened Form of Assignments

1. **Const Assignment**  set the `num` property and is designed only for the `@Const` class. The `=` symbol is used to mark an assignment.

    Example:
    ```heta
    k1 @Const = 1.1;
    ```
    Equivalent to:
    ```heta
    {
        id: k1,
        class: Const,
        num: 1.1
    };
    ```

1. **Start Assignment** sets the initial assignment for the `@Record` instances. The symbols `.=` or `[]=` can be used. (Do not put spaces between the symbols.)

    Example:
    ```heta
    s @Record .= 10;
    p @Species []= 0;
    ```
    Equivalent to:
    ```heta
    {
        id: s,
        class: Record,
        assignments: { start_: 10 }
    };
    {
        id: p,
        class: Species,
        assignments: { start_: 0 }
    };
    ```

1. **Rule Assignment** sets the `ode_` assignment for `@Record `instances. The `:=` symbol can be used.

    Example:
    ```heta
    rule1 @Record := x*y;
    ```
    Equivalent to:
    ```heta
    {
        id: rule1,
        class: Record,
        assignments: { ode_: x*y }
    };
    ```

1. **Switcher Assignment** sets the re-assignment of records managed by a Switcher. The symbol `[<switcher id>]=` can be used.

    Example:
    ```heta
    s [dose_switch_1]= 0.1;
    ```
    Which is equivalent to
    ```heta
    {
        id: s,
        assignments: { dose_switch_1: 0.1 }
    };
    ```

## Include Statement

1. The `include` statement is used to describe module loading, where the file content is included (while compilation) into the current one. 

1. The `include` statement can be replaced by the [`#include`](./actions?id=include) action with equivalent meaning.

1. The statement consists of the reserved word `include` followed by a relative or absolute file path. The full form of the statement can be presented as follows:


    ```heta
    include <filepath> type <module type> with {...};
    ```
    Where `<module type>` is one of the supported modules, and `{...}` is a dictionary to set additional options. For more details on modules, refer to the modules page.

1. The `include` statement can set additional parameters after the `with` keyword in dictionary format.

1. The statement must be finalized with semicolon `;`.


Example 1:
```heta
include ./addon.heta type heta;
```

Example 2:
```heta
include file.xlsx type table with {
    sheet: 2,
    omitRows: 3
};
```
## Namespace Statement

1. Platform components are grouped in namespaces, and the `namespace` statement can be used to work with namespaces more easily. For details about namespaces, refer to the [namespaces](namespaces) page.

1. The first use of a namespace block with a new space name initializes the `namespace` (in the same manner as the `#setNS` action).

1. To clarify the type of the namespace, one of the reserved words `abstract` or `concrete` should be used. The default type is `concrete`.

1. The statement must be finalized with semicolon `;` or must include `begin/end` block part.

1. The block is organized as follows:


    ```heta
    namespace <space id> begin
        <action statement>;
        ...
    end
    ```

    Example:
    ```heta
    namespace one begin
        rec @Record .= 1;
        comp @Compartment .= 10;
    end
    ```
    Equivalent to:
    ```heta
    #setNS { space: one, type: concrete };
    one::rec @Record .= 1;
    one::comp @Compartment .= 10;
    ```
    or equivalent to the plain format:
    ```heta
    {
        space: one,
        type: concrete,
        action: setNS
    };
    {
        space: one,
        id: rec,
        class: Record,
        assignments: { start_: 1}
    };
    {
        space: one,
        id: comp,
        class: Compartment,
        assignments: { start_: 10}
    };
    ```

## Comments

1. Comments are parts of the code that are not compiled and are used solely for annotating the code.

1. A single-line comment starts with `//` symbols and ends with line breaks.

    Example:
    ```heta
    // this is not part of the platform but only a comment to the code
    ```
1. A multiline comment starts with `/*` and ends with `*/` or at the end of the file.

    Example:
    ```heta
    /*
        Compiler will not parse this part.
    */
    ```
## Special String Formats
Special string formats are additional rules for specific properties of actions and classes.

### ID
The ID format is used for indexing Heta components, creating identifiers for components and namespaces, and referencing elements. The following properties must follow the ID format:
- `id`, `space` for all actions
- Properties that describe references to elements like `compartment` in the `Species` class, `fromSpace` in the `importNS` action, etc.
- References used inside `MathExpr`, `UnitsExpr`, `ProcessExpr`.
The base rules for an ID are as follows:

1. The first symbol should be a letter or underscore.
1. The second and following elements should be letters, numbers, or underscores.
1. The last symbol should not be an underscore.
Additionally, some words cannot be used as identifiers because they are reserved for statements or specific object names, such as 
`NaN`, `Infinity`, `e`, `pi`, 
`include`, `block`, `namespace`, `abstract`, `concrete`, `begin`, `end`

__Example__

**Correct:** `x`, `x12`, `x_12`, `_12`, `x___12`, `_begin`

**Incorrect:** `12x`, `x-12`, `x 9`

**Incorrect usage of reserved words as an ID**: `begin`, `block`

**Incorrect underscore position**: `_`, `x12_`

### Filepath

A string representing a relative or absolute file path.

The following properties must follow the Filepath format:

- `source` property in the `include` statement
- `filepath` property in the `export` statement

__Examples:__

Correct Filepath: `output`, `./output`, `../output`, `Y:/my-platform/src/module1/model.heta`

### UnitsExpr

UnitsExpr strings represent complex units combined from predefined unit IDs. Available operators are `*`, `/`, `^`, and `1/`.

The following properties must follow the UnitsExpr format:
- `unit` property in `@Const` and `@Record` classes
- `unit` property in `#defineUnit` action

See more details see [units](units) page.

__Example:__

**Correct UnitsExpr:** `mg`, `g/mole`, `1/h`, `kg/m2`, `kg/m^2`
**Incorrect UnitsExpr:** `g/(mole*L)`, `5*g`, `km + kg`
### MathExpr:

MathExpr describes mathematical expressions in string format.
Available operators are `+`, `-`, `*`, `/`, and `^`. See details in [Math expressions](./math)

The following properties must follow the `MathExpr` format:
- `assignments` sub-properties in the `Record` class
- `trigger` property in `DSwitcher` and `CSwitcher` classes
__Example:__

**Correct MathExpr:** `x*y*pow(x,y)`

### ProcessExpr:

A `ProcessExpr` formatted string represents process stoichiometry.
The "arrow" syntax (`->`, `<->`, `=>`, `<=>`, `>`, `<>`) divides two parts into influx (left) and outflux (right).
The `+` symbol divides two or more actors, and stoichiometry coefficients are shown by numbers before the reference. The asterisk symbol is optional.
The following properties must follow the `ProcessExpr` format:

- `actors` property in the `Process` class

__Example__

*Correct ProcessExpr*:

- `A->B`
- `A =>`
- `2A <=> 3*B + C`
- `2 A <=> 3 * B + C`
- `2A > 3R + C`
