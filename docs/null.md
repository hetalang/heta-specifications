# Null values

Null values are special property values that represent the absence of a value. They are used to indicate that a property is intentionally empty or missing.

null values cannot be used as a ID of components because it is reserved word. 

Setting a property to `null` works differently for different statements.

## null in #insert and #update

When we set a property to `null` in the `#insert` or `#forceInsert` statements, it means that the property is intentionally empty,missing, or has the default value. It is the same as we do not declare the property at all.

```heta
k1 @Const {units: null} = 1; // implicit #insert
```

works the same as

```heta
k1 @Const = 1;
```

When we set a property to `null` in the `#update` statement, it means that the property is intentionally removed.

```heta
k1 @Const {units: mM};
k1 {units: null} = 1; // implicit #update
```

which is equivalent to

```heta
k1 @Const = 1;
```

## wrong usage of null

Null properties are allowed only in properties but not in sub-properties like in examples below.

```heta
x1 @Record {
    aux: {a: 1, b: null} // throw syntax error
}; 
```

```heta
x1 @Record {
    aux: null // correct
}; 
```

```heta
x1 @Record {
    tags: [a, b, null] // throw syntax error
}; 
```

```heta
x1 @Record {
    tags: null // correct
}; 
```

Null properties values must be used only for components, but not for `#defineFunction` or `#defineUnit`.

```heta
#defineFunction f1 {
    arguments: [x,y,z],
    math: null          // throw syntax error
};
```

```heta
#defineUnit u1 {
    units: null          // throw syntax error
};
```

Null value cannot be used as a ID of components.

```heta
null @Const = 1; // throw syntax error
```
