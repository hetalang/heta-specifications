# Syntax

See also the [Heta tutorial](https://hetalang.github.io/#/resources/?id=lesson-2-syntax)

The Heta code can be represented as a sequence of the statements that create and modify elements in a modeling platform. The parsing and interpretation of the code result in creating of static (database-like) structure representing the modeling platform, see [compilation](./compilation). There are many ways to write the same modeling system using Heta code. A developer has the freedom to make his code nice and readable.

## Action statement

1. The action statements are divided by semicolons. The line breaks between and inside statements do not matter for interpretation.
They can be used for code decoration.

    ```heta
    <statement one>; <statement two>;
    <statement 
        three>;
    ...
    ```

1. The action statement consists of a dictionary part and a set of shortened properties. The parts have the following priority rule: the subsequent part has a higher priority. List of part types are:

    - Dictionary
    - Index
    - Class
    - Action
    - Title
    - Notes
    - Assignments (several subtypes)

## Plain format of action statement

1. The plain format is the most flexible way to describe action properties. It begins with `{` symbol and ends with `}` symbol. A set of key-value pairs divided with commas `,` can be inside. The syntax is similar to the dictionary in JSON or YAML format.

    Example:
    ```heta
    { prop1: value 1, prop2: value 2, ...};
    ```

    The **property** is always string without spaces. The properties must be unique within a dictionary. The **value** can have one of five types: String, Number, Boolean, Dictionary, or Array. The dictionary and array values might include other types. 

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

2. The **String** value inside Dictionary starts from first non-space symbol and ends with non-space symbol before stop-list: ", } ] @ # ' \` " ". If you want to use symbols from the stop list inside string value, use parentheses `"`.

    Example:
    ```heta
    {
        prop1: Some string,
        prop2: "String with # stop-symbols @"
    };
    ```

2. There are [special string formats](#special-string-formats) which are used to describe the particular properties of actions or classes. They follow the same **String** syntax but also have the specific syntax rules.

    Example:
    ```heta
    {
        id: r1, // follows the ID format
        class: Reaction,
        actors: A => B // follows the ProcessExpr format
    };
    ```

3. The **Number** value inside Dictionary is the Number in Double format.
    
    Example:
    ```heta
    {
        prop1: 1,
        prop2: 1.2,
        prop3: 1.1e-2,
        prop4: 1.2E+3
    };
    ```

4. The **Boolean** value inside Dictionary may have two values: `true`, `false`. If you want to use a string with a value equal to `true`, false use parentheses `"true"`, `"false"`

    Example:
    ```heta
    {
        prop1: true,
        prop2: false,
        stringProp: "true"
    };
    ```

5. The nested **Dictionary** value follows the same rules as the plain format of action statement and can be nested.

    Example:
    ```heta
    {
        prop: { nestedProp: { nextLevel: Hello! } } 
    };
    ```

6. The **Array** value is a sequence of numbered elements divided by commas. Numeration starts from 0 (zero) index.

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

## Shortened format of action statement

1. To simplify code reading/writing there are several types of statement parts. They describe the commonly used properties in compact form, see [Classes](./classes) description.

1. The **Index** describes identifiers: `id` and `space` of components. The example if indexes are: `space::id` or just `id` for nameless space.

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

1. The **Class** shortened property sets `class`. Particular Class defines a list of properties that can be set. This property is denoted by `@` symbol. The list of possible classes can be found in [Classes](./classes) description. Classes' names always start from the uppercase symbol. When you use class shortened property `@` the parser will replace the first lowercase symbol for the capital one.

    Example:
    ```heta
    k2 @const { num: 1.3 };
    ```
    Which is equivalent to
    ```heta
    { id: k2, class: Const, num: 1.3 };
    ```

1. The **Action** shortened property sets `action`. The `action` describe what to do with the statement, see [Actions](./actions). This shortened is denoted by `#` symbol. The action property is a required property in the base statement, but it has a default value `upsert`.

    Example:
    ```heta
    #insert k3 @Const;
    k3 { num: 1 };
    ```
    Which is equivalent to
    ```heta
    { action: insert, id: k3, class: Const };
    { action: upsert, id: k3, num: 1 };
    ```

1. The **Title** shortened property sets `title`. It is denoted by single quotes `'`.

    Example:
    ```heta
    a 'Some title';
    ```
    which is equivalent to
    ```heta
    { id: a, title: Some title };
    ```

1. The **Notes** shortened property sets `notes`. It is denoted by three single quotes `'''`. The `notes` part is located usually before the other parts.

    Example:
    ```heta
    ''' Some notes '''
    a @Const;
    ```
    which is equivalent to the following plain version
    ```heta
    { notes: Some notes, id: a, class: Const };
    ```

## Shortened form of assignments

1. **Const assignment** sets `num` property and is designed only for `@Const` class. The symbol `=` is used to mark an assignment.

    Example:
    ```heta
    k1 @Const = 1.1;
    ```
    Which is equivalent to
    ```heta
    {
        id: k1,
        class: Const,
        num: 1.1
    };
    ```

1. **Start assignment** sets the initial assignment for the `@Record` instances. The symbol group `.=` or `[]=` can be used. (Do not put blanks between symbol group!)

    Example:
    ```heta
    s @Record .= 10;
    p @Species []= 0;
    ```
    Which is equivalent to 
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

1. **Rule assignment** sets the `ode_` assignment for `@Record` instances. The symbol group `:=` can be used.

    Example:
    ```heta
    rule1 @Record := x*y;
    ```
    Which is equivalent to 
    ```heta
    {
        id: rule1,
        class: Record,
        assignments: { ode_: x*y }
    };
    ```

1. **Switcher assignment** sets the re-assignment of records value managed by Switcher. The symbol group `[<switcher id>]=` can be used.

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

## Include statement

1. The `include` statement describes modules loading. The simplest explanation of how this works is that the file content should be included into the current one.

1. The `include` statement can be replaced by [`#include` action](./actions?id=include) with equivalent meaning.

1. The statement consists of the reserved word `include` followed by a relative or absolute file path. The full form of the statement can be presented in the following manner: 
    ```heta
    include <filepath> type <module type> with {...}
    ```
    where `<module type>` is one of the supported modules and `{...}` is a dictionary to set additional options. See more details on the [modules page](modules).

1. `include` statement can set additional parameters after `with` keyword in dictionary format

Example 1:
```heta
include ./addon.heta type heta
```

Example 2:
```heta
include file.xlsx type xlsx with {
    sheet: 2,
    omitRows: 3
}
```

## Namespace statement

1. Platform components are grouped in namespaces. `namespace` statement can be used for easier work with namespaces. For more details about namespaces see on the page [namespaces](namespaces).

1. The first use of Namespace block with a new space name initializes `namespace` implicitly by `#setNS` action.

1. To clarify the type of namespace one of the reserved words: `abstract`, `concrete` should be used. The default type is `concrete`.

1. The block is organized in the following manner.

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
    Which is equivalent to 
    ```heta
    #setNS { space: one, type: concrete };
    one::rec @Record .= 1;
    one::comp @Compartment .= 10;
    ```
    or equivalent to the plain format
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

1. Comments are the part of the code which are not compiled and are used only for annotation of code.

1. A single-line comment starts with `//` symbols and ends with line breaks.

    Example:
    ```heta
    // this is not part of the platform but only the comment to the code
    ```

1. A multiline comment starts with `/*` and ends with `*/` or end of the file.

    Example:
    ```heta
    /*
        Compiler will not parse this part.
    */
    ```

## Special string formats

Special string formats are additional rules for a specific properties of actions and classes.

### ID

`ID` describes the string format which is used for indexing Heta components. 
The ID format is applied to create identifiers of elements including component and namespaces as well as to create a reference to an element. The following properties must follow the ID format:

- `id`, `space` for all actions
- properties which describe references to elements like: `compartment` in `Species` class, `fromSpace` in `importNS` action, etc.
- references that are used inside `MathExpr`, `UnitsExpr`, `ProcessExpr`.

The base rules for ID are the following:

1. First symbol should be letter or underscore.
1. Second and following elements should be letter, number or underscore.
1. The last symbol should not be underscore.

Additionally some words cannot be used as identifiers because they are reserved for statements or specific object names:

`NaN`, `Infinity`, `e`, `pi`, 
`include`, `block`, `namespace`, `abstract`, `concrete`, `begin`, `end`

__Example__

**Correct:** `x`, `x12`, `x_12`, `_12`, `x___12`, `_begin`

**Wrong:** `12x`, `x-12`, `x 9`

**Wrong usage of reserved words as ID:** `begin`, `block`

**Wrong underscore position:** `_`, `x12_`

### Filepath

String representing relative or absolute file path.

The following properties must follow the Filepath format:

- `source` property in `include` statement
- `filepath` property in `export` statement

__Examples__

`output`, `./output`, `../output`, `Y:/my-platform/src/module1/model.heta`

### UnitsExpr

UnitsExpr strings represent the complex units combined from the predefined unit IDs.
Available operators: `*`, `/`, `^`, `1/`.

The following properties must follow the UnitsExpr format:

- `unit` property in `Const`, `Record` classes
- `unit` property in `defineUnit` action

See more details see [units](units) page.

__Example__

**Correct UnitsExpr:**: `mg`, `g/mole`, `1/h`, `kg/m2`, `kg/m^2`

**Wrong UnitsExpr:** `g/(mole*L)`, `5*g`, `km + kg`

### MathExpr

MathExpr describes mathematical expressions in string format.
Available operators: `+`, `-`, `*`, `/`, `^`. See details in [Math expressions](./math)

The following properties must follow the `MathExpr` format:

- `assignments` sub-properties in `Record` class
- `trigger` property in `DSwitcher`, `CSwitcher` classes

__Example__

**Correct MathExpr:** `x*y*pow(x,y)`

### ProcessExpr

A `ProcessExpr` formatted string represents process stoichiometry.
The "arrow" syntax (`->`, `<->`, `=>`, `<=>`) divides two parts: influx (left) and outflux (right).
The "plus" symbol divide two or more actors.
Stoichiometry coefficients are shown by numbers before reference. The asterisk symbol is optional here.

The following properties must follow the `ProcessExpr` format:

- `actors` sub-properties in `Process` class

__Example__

*Correct ProcessExpr*:

- `A->B`
- `A =>`
- `2A <=> 3*B + C`
- `2 A <=> 3 * B + C`
