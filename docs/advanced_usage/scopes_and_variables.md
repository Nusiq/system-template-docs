(scopes-and-variables)=
# Scopes And Variables
System Template offers code generation features allowing you to create files based on data provided by different scopes. Similar to programming, values from more specific scopes shadow those from less specific scopes. The following scopes are available in System Template, arranged from least specific to most specific:

- Default Scope
- Global Scope
- Global Plugins
- Group Scope (optional)
- Local Plugins
- System Scope
- File Scope (provided by the _map.py file)

Scopes are defined in JSON objects, either in {ref}`Plugins<plugins>` or in the scope property of the {ref}`_map.py File<the-mappy-file>`. The keys serve as variable names, and since System Template runs in Python, the names must be valid Python identifiers. In nested structures, arrays are treated as Python lists, and objects are treated as Python dictionaries.

**Example:**
```json
{
    "foo": "bar",
    "obj": {"x": 1, "y": 2},
    "ls": ["a", "b", "c"]
}
```

This example defines the following variables:
- `foo` with the value `"bar"`
- `obj` with the value `{"x": 1, "y": 2}`
- `ls` with the value `["a", "b", "c"]`

Note that the example deliberately avoids using names `object` and `list` as they are already used by Python.

## Default Scope
The default scope provides some basic variables for your convenience and special AUTO mapping variables:

- `true` and `false` - aliases for `True` and `False`, useful for writing JSON-like Python files with {ref}`Python Templates<python-templates>`
- `AUTO`, `AUTO_FLAT`, `AUTO_SUBFOLDER`, and `AUTO_FLAT_SUBFOLDER` - special variables used for {ref}`AUTO mapping<auto-mapping>`.

## Global Scope
The global scope is defined by the `scope.json` file, located in the root folder of the `system_template` filter by default or in the folder specified in the {ref}`Filter's configuration<installation-and-configuration>`.

## Group Scope
The group scope is defined by the `_group_scope.json` file, located in the root folder of the group. More information about the group scope is available in the {ref}`The Structure of the Systems<the-structure-of-the-systems>` section.

## System Scope
The system scope is defined by the `_scope.json` file, located in the root folder of the system. Further details about the system scope can be found in the {ref}`The Structure of the Systems<the-structure-of-the-systems>` section.

## File Scope
The file scope is provided by the {ref}`_map.py File<the-mappy-file>` and contains variables related to the file being processed. Additional information about the file scope is available in the {ref}`The _map.py File<the-mappy-file>` section.
