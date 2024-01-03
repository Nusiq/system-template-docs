(json-template-syntax)=
# Syntax

If you're familiar with Python and JSON, the syntax used by this filter should be easy to understand.

## Backticks
When a key or a string value is surrounded by backticks, the filter evaluates the expression inside the backticks and replaces the key or value accordingly.

*Input*
```json
{
    "foo": "`2 + 2`",
    "`str(5+5)`": "baz"
}
```
*Output*
```json
{
    "foo": 4,
    "10": "baz"
}
```
Note that the `5+5` expression must be converted to a string. Passing a number as a key would result in an error.

## Using Variables and the `scope.json` File
You can use variables defined in the `scope.json` file.

*scope.json*
```json
{
    "foo": 12345
}
```
*Input*
```json
{
    "bar": "`foo`"
}
```
*Output*
```json
{
    "bar": 12345
}
```

## Defining Objects in Loops

If a key evaluates to a list, the filter generates multiple JSON objects with keys based on each element of the list.

*Input*
```json
{ 
    "`[f'bar{i}' for i in range(2)]`": "baz"
}
```
*Output*
```json
{
    "bar0": "baz",
    "bar1": "baz"
}
```


## The `K` Object
There is a special `K` object that lets you extend the scope by providing it with new variables. The `K` object is especially useful when you define your keys in a loop. It can be used to pass the variables from the loop into the generated JSON objects.

*Input*
```json
{
    "`[K(f'foo{i}', number=i) for i in range(2)]`": {
        "bar": "`number`"
    }
}
```
*Output*
```json
{
    "foo0": {
        "bar": 0
    },
    "foo1": {
        "bar": 1
    }
}
```
The first value of `K` is the key to the generated JSON object,
the rest of the arguments must be keyword arguments. The keyword arguments are passed to the scope of the object.

## The `__unpack__` and `__value__` Keys

The `__unpack__` and `__value__` keys are special keys that can be used to generate objects inside a list. They only work inside objects directly contained in a list.

Using the `__unpack__` key inside such an object marks it as a template for generating values for the list. The value of `__unpack__` must be a list of dictionaries. Each dictionary is a scope of variables to be used when evaluating the template. The evaluated values are unpacked into the list in place of the original object.

If the generated values shouldn't be objects, but other types of values, you can use the `__value__` key. If the `__value__` key is present, the generated values will be the values of the `__value__` keys. The `__value__` key can be anything, including an expression surrounded by backticks.

**Example 1:**

*Input*
```json
[
    {
        "my_favourite_color": "I don't know"
    },
    {
        "__unpack__": [{"color": "red"}, {"color": "green"}],
        "my_favourite_color": "`color.upper()`"
    },
    {
        "my_favourite_color": "BLACK"
    }
]
```

*Output*
```json
[
    {
        "my_favourite_color": "I don't know"
    },
    {
        "my_favourite_color": "RED"
    },
    {
        "my_favourite_color": "GREEN"
    },
    {
        "my_favourite_color": "BLACK"
    }
]
```

**Example 2:**

*Input*
```json
[
    "It's not green",
    {
        "__unpack__": "`[dict(color=c) for c in ('red', 'green')]`",
        "__value__": "`color == 'green'`"
    },
    "Not green"
]
```

*Output*
```json
[
    "It's not green",
    false,
    true,
    "Not green"
]
```

````{warning}
When using a dictionary in the `__unpack__` list to define the scope for the values in the generated objects, the scope is not visible to other keys in the same object. If you want to generate the keys of the generated objects, you have to use the `__value__` key.

In Minecraft, it's useful for defining the transitions of the animation controllers, where each transition is a separate object, and the keys are the names of the transition targets, and the values are the transition conditions.

**Example:**
```
[
    {
        "__unpack__": "`[dict(i=i) for i in range(3)]`",
        "__value__": {
            "`f'variant{i}'`": "`f'q.variant == {i}'`"
        }
    }
]
```

**Output:**
```
[
    {
        "variant0": "q.variant == 0",
    },
    {
        "variant1": "q.variant == 1",
    },
    {
        "variant2": "q.variant == 2"
    }
]
```

````

## The `JoinStr` Object

The `JoinStr` object can be used to join a list of strings into a single string. Every list that starts with a `JoinStr` object is expected to contain only strings. The evaluated list discards the `JoinStr` object and uses the rest of the list as arguments for the `str.join` method. You can use `JoinStr` in combination with the `__unpack__` and `__value__` keys as long as the result contains only strings.

**Example 1:**

*Input*
```json
[
    "`JoinStr(';')`",
    "a = 1",
    "b = 2",
    "c = 3",
]
```

*Output*
```json
"a = 1;b = 2;c = 3"
```

**Example 2:**

*Input*
```json
[
    {
        "__unpack__": "`[dict(value=v) for v in (JoinStr(' '), 'hi', 'there')]`",
        "__value__": "`value`"
    },
    "hello",
    "there",
    "it's me"
]
```

*Output*
```json
"hi there hello there it's me"
```
