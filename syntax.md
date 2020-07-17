# Syntax

The Heta code can be represented as the sequence of statements which create and modify elements in modeling platform. The parsing and interpretation of the code results in creation of static (database-like) structure representing the modeling system, see [compilation](./compilation). There are many ways to write the same modeling system using Heta code. A developer has a freedom to make his code nice and readable.

## Reserved words

There are some words which cannot be used as identifiers because they are reserved for statements or specific object names.

`nameless`, 
`NaN`, `Infinity`, `e`, `E`, `pi`, `PI`, 
`include`, `block`, `namespace`, `abstract`, `concrete`, `begin`, `end`

## Base statements

1. Base statements are divided by semicolons. The line breakes between and inside statements do not matter but can be used for code decoration.

    ```heta
    <statement one>; <statement two>;
    <statement 
        three>;
    ...
    ```

1. Single base statement consists of any number of parts which differentiated lexically. The parts have the following priority rule: the subsequent part has the higher priority. List of part types are:

    - Dictionary
    - Index
    - Class
    - Action
    - Title
    - Notes
    - Assignments (several subtypes)

## Dictionary part

1. The **Dictionary** part is the most general type. It begins with `{` symbol and ends with `}` symbol. A set of key-value pairs devided with commas `,` can be inside. The syntax of the dictionary is similar to JSON or YAML format.

    Example:
    ```heta
    { prop1: value 1, prop2: value 2, ...};
    ```

    The **property** is always string without spaces. The properties must be unique withing dictionary. The **value** can have one of five types: String, Number, Boolean, Dictionary or Array.

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

2. The **String** value inside Dictionary starts from first non-space symbol and ends with non-space symbol before stop-list: ", } ] @ # ' \` " ". If you want to use symbols from stop list inside string value, use parentheses `"` .

    Example:
    ```heta
    {
        prop1: Some string,
        prop2: "String with # stop-symbols @"
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

4. The **Boolean** value inside Dictionary may have two values: `true`, `false`. If you want to use string with value true, false use parentheses `"true"`, `"false"`

    Example:
    ```heta
    {
        prop1: true,
        prop: false
    };
    ```

5. The **Dictionary** value follows the same rules as Dictionary part of statement and can be nested.

    Example:
    ```heta
    {
        prop: { nestedProp: { nextLevel: Hello! } } 
    };
    ```

6. The **Array** value represents the sequence of numbered elements divided by commas.

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

## Syntactic sugar parts

1. To simplify code reading/writing there are several types of statement parts. They describe the commonly used properties in compact form, see [Classes](./classes) description.

1. The **Index** describes identifiers: `id` and `space` of elements. The example if indexes are: `space::id` or `id` for nameless space.

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

1. **Class** part sets `class` property. Particular Class defines a list of properties which can be set. This property is denoted by `@` symbol. List of possible classes can be found in [Classes](./classes) description. Classes names always starts from uppercase symbol. When you use Class symbol `@` the parser will replace the first lowercase symbol for the capital one.

    Example:
    ```heta
    k2 @const { num: 1.3 };
    ```
    Which is equivalent to
    ```heta
    { id: k2, class: Const, num: 1.3 };
    ```

1. **Action** part sets `action` property. Action describe what to do with the statement, see [Actions](./actions). This property is denoted by `#` symbol. Action statement is required property in base statement, but it has default value `upsert`.

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

1. **Title** part sets `title` property. It is denoted by single quotes `'`.

    Example:
    ```heta
    a 'Some title';
    ```
    which is equivalent to
    ```heta
    { id: a, title: Some title };
    ```

1. **Notes** part sets `notes` property. It is denoted by three single quotes `'''`. Notes part is located usually before the other parts.

    Example:
    ```heta
    ''' Some notes '''
    a @Const;
    ```
    which is equivalent to
    ```heta
    { id: a, notes: Some notes };
    ```

## Assignments part

1. **Const assignment** sets `num` property and designed only for `@Const` class. The symbol `=` is used to mark the assignment.

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

1. **Start assignment** sets the initial assignment for the `@Record` instances. The symbol group `.=` or `[]=` can be used.

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

1. `include` statement describes modules loading. The simplest explanation how this works is that the file content should be included to the current one.

1. The statement consists of reserved word `include` followed by relative or absolute filepath. Full form of the statement can be presented in the following manner: 
    ```heta
    include <filepath> type <module type> {...}
    ```
    where `<module type>` is one of supported modules and `{...}` is dictionary to set additional options. See more details on the [modules page](modules).

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

## Namespace block statement

1. Platform components are groupped in namespaces. `namespace` statement can be used for easy work with namespaces. For more details about namespaces see on the page [namespaces](namespaces).

1. First use of Namespace block with new space name initializes namespace implicitly by `#setNS` action.

1. To clarify the type of namespace one of the reserved words: `abstract`, `concrete` should be used. The default type is `concrete`.

1. The block is organized in the following manner.

    ```heta
    namespace <space id> begin
        <base statement>;
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
    or equivalent to
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

1. Comments is the part of the code which is not compiled and used only for annotation of code.

1. **One line comment** starts with `//` symbols and ends with line breaks.

    Example:
    ```heta
    a @Const = 5;
    ```

1. Multiline comment starts with `/*` and ends with `*/` or end of file.

    Example:
    ```heta
    /*
        This is not parsed part
    */
    ```
