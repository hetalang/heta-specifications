# Syntax

The Heta code represents the sequence of statements which create and modify elements in modeling platform. The parsing and interpretation of the code results in creation of static (database-like) structure representing the modeling system. See [workflow](./workflow). There are many ways to write the same modeling system using Heta code. A developer has flexibility to make his code nice and readable.

## Base statements

1. Base statements are divided by semicolons. The line breakes do not matter but can be used for code decoration.

    ```heta
    <statement one>; <statement two>;
    <statement 
        three>;
    ...
    ```

1. Single statement consists of any number of parts which differentiated lexically. The parts have the following priority rule: the subsequent part has the higher priority. List of part types are:

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

2. The **String** value inside Dictionary starts from first non-space symbol and ends with non-space symbol before stop-list: ", } ] @ # ' \` " ". If you want to use symbols from stop list inside stop list, use parentheses `"` .

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

4. The **Boolean** value inside Dictionary may have two values: `true`, `false`

    Example:
    ```heta
    {
        prop1: true,
        prop: false
    };
    ```

5. The **Dictionary** value follows the same rules as Dictionary part of statement and can be nested.

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

1. To simplify code reading/writing there are several types of statement parts. They describe the commonly used properties in compact form. See [Classes](./classes) description.

1. The **Index** describes identifiers: `id` and `space` of elements. The syntax can be described by the example: `space::id` or `id` for anonimous space.

    Example:
    ```heta
    one::k1 { prop1: some text };
    ```
    Which is equivalent to 
    ```heta
    { space: one, id: k1, prop1: some text };
    ```

1. The **Class** part sets the `class` property. Class defines a list of properties which can be set. This property is denoted by `@` symbol. List of possible classes can be found in [Classes](./classes) description. Classes names always starts from uppercase symbol. When you use Class symbol the parser will replace the first lowercase symbol for the capital one.

    Example:
    ```heta
    k2 @const { num: 1.3 };
    ```
    Which is equivalent to
    ```heta
    { id: k2, class: Const, num: 1.3 };
    ```

1. The **Action** part sets the `action` property. Action describe what to do with the statement. See [Actions](./actions). This property is denoted by `#` symbol. Action statement is required property, but it has default value `upsert`.

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

1. The **Title** part sets the `title` property. It is denoted by single quotes `'`.

    Example:
    ```heta
    a 'Some title';
    ```
    which is equivalent to
    ```heta
    { id: a, title: Some title };
    ```

1. The **Notes** part sets the `notes` property. It is denoted by three single quotes `'''`

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

1. The **Const assignment** sets the `num` property and designed only for @Const class. The symbol `=` is used to mark the assignment.

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

1. The **Start assignment** sets the initial assignment for the @Record instances. The symbol group `.=` or `[]=` can be used.

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

1. The **Rule assignment** sets the ode assignment for the @Record instances. The symbol group `:=` can be used.

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

1. The **Switcher assignment** sets the assignment when model switches. The symbol group `[<switcher id>]=` can be used.

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

1. `include` statement describes which files should be included to the current one.

    ```heta
    include ./addon.heta type heta
    ```

## Namespace block

1. Several base statements can be groupped by namespace. The block is organized in the following manner.

    ```heta
    namespace <space id> begin
        <statement without space>;
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
    one::rec @Record .= 1;
    one::comp @Compartment .= 10;
    ```
    or equivalent to
    ```heta
    {
        space: one,
        id: rec,
        class: Record,
        assignments: { start_: 1}
    };
    {
        space: one,
        id: rcomp,
        class: Compartment,
        assignments: { start_: 10}
    };
    ```

## Comments

1. Comments is the part of the code which is not parsed and used only for annotation of code.

1. **One line comment** starts with `//` symbols and ends with line breaks.

    Example:
    ```heta
    a @Const = 5;
    ```

1. Multiline comment starts with `/*` and ends with `*/`.

    Example:
    ```heta
    /*
        This is not parsed part
    */
    ```
