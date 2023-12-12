(scopes-and-variables)=
# Scopes And Variables
System Template has code generation features that allow you to create files based on the data prvided by differnt scopes. Just like in programming the values from more specific scopes shadow the values from less specific scopes. There are following scopes in System Template (from the least specific to the most specific):
- Default Scope
- Global Scope
- Global Plugins
- Group Scope (optional)
- Local Plugins
- System Scope
- File Scope (provided by the _map.py file)

The scopes are defined in JSON objects, in {ref}`Plugins<plugins>` or in scope property of {ref}`_map.py File<the-mappy-file>`. The keys are used as the names of the variables. System Template runs in Python, so it's not allowed to use names that are not valid Python identifiers. If the structure of the scope is nested, the arrays are treated as Python's lists and the objects are treated as Python's dictionaries.

_Example:_
```json
{
    "foo": "bar",
    "obj": {"x": 1, "y": 2},
    "ls": ["a", "b", "c"]
}
```
This scope would define the following variables:
- `foo` with the value `"bar"`
- `obj` with the value `{"x": 1, "y": 2}`
- `ls` with the value `["a", "b", "c"]`

Note that the example deliberately avoids using names `object` and `list` as they are already used by Python.

## Default Scope
The defaut scope provides some basic variables for your convienience and the special AUTO mapping variables:

- `true` and `false` - aliases for `True` and `False`, useful for writing JSON-like Python files with {ref}`Python Templates<python-templates>`
- `AUTO`, `AUTO_FLAT`, `AUTO_SUBFOLDER` and `AUTO_FLAT_SUBFOLDER` - special variables used for {ref}`AUTO mapping<auto-mapping>`.

## Global Scope
The global scope is defined by the `scope.json` file, which is located in the root folder of the `system_template` filter by default or in the folder specified in the {ref}`Filter's configuration<installation-and-configuration>`.

## Group Scope
The group scope is defined by the `_group_scope.json` file, which is located in the root folder of the group. You can read more about the group scope in the {ref}`The Structure of the Systems<the-structure-of-the-systems>` section.

## System Scope
The system scope is defined by the `_scope.json` file, which is located in the root folder of the system. You can read more about the system scope in the {ref}`The Structure of the Systems<the-structure-of-the-systems>` section.

## File Scope
The file scope is provided by the {ref}`_map.py File<the-mappy-file>` and contains variables related to the file that is being processed. You can read more about the file scope in the {ref}`The _map.py File<the-mappy-file>` section.
