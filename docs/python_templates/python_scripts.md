(python-scripts)=
# Python Scripts
You can mark any **text** file in a system to be evaluated as a Python script before it undergoes further processing. To do this, simply set the `python_script` property of the file in the {ref}`_map.py file<the-mappy-file>` to `True`.

**Example:**
```py
[
    {
        "source": "example.behavior.json",
        "target": AUTO_FLAT,
        "python_script": True
    }
]
```

```{note}
Note that the file marked as a Python script in the example above uses the `.json` extension. This is not a mistake. As long as the file content is a valid Python script, the extension does not matter.

Sometimes using other extensions for Python scripts in System Template is beneficial because it allows you to utilize the {ref}`Auto Mapping<auto-mapping>` feature. If you prefer, you can always configure your {ref}`auto_map.json file<custom-auto-mapping>` to use `.py` extension and export files wherever you want.
```

```{note}
If you're using VS Code, you can temporarily change the syntax highlighting of the file to Python using `CTRL+SHIFT+P → Change Language Mode → Python`. This makes it easier to work with System Template's Python scripts.
```

## How It Works
When a file is marked as a Python script, System Template does not read its text content directly. Instead, the file is evaluated using Python's `exec` function.

- If the script assigns a value to the `__output__` variable, that value replaces the file's content.
- If `__output__` is **not** set, the file is skipped, and no further processing occurs.

Python script files are evaluated with the system's root as their working directory and have access to all variables passed using {ref}`scope<scopes-and-variables>`.

Since these are standard Python scripts, you can use them to generate additional files. In such cases, using `__output__` is optional.

When a Python script is evaluated, it receives an additional variable in its scope: `__target__`.

- `__target__` contains a `pathlib.Path` object pointing to the evaluated, absolute target path of the file.
- This is useful when generating files without using `__output__`.

## Combining Python Scripts with Other Features
The Python script feature simply adds an extra step to file evaluation; it does not interfere with other processing steps. This means that you can use Python scripts to generate text values for output that can still be further processed using {ref}`JSON Templates<json-template>`, {ref}`Subfunctions<subfunctions>`, or any other System Template feature.

## Example: Using a Python Script for File Generation
The content of `example.behavior.json` from the example above could look like this:

```py
import json

def get_blank_entity(name: str):
    return {
        "format_version": "1.20.80",
        "minecraft:entity": {
            "description": {
                "identifier": f"nusiq:{name}",
                "is_spawnable": True,
                "is_summonable": True
            },
            "component_groups": {},
        }
    }

# __target__ is not explicitly defined in the script but is still available.
with open(__target__.parent / "example2.behavior.json", "w") as f:
    json.dump(get_blank_entity("example2"), f, indent="\t")

__output__ = json.dumps(get_blank_entity("example"), indent="\t")
```

This script generates two files:
1. `example.behavior.json` (using `__output__`)
2. `example2.behavior.json` (by writing directly to a file)

## Considerations and Limitations
While Python scripts provide great flexibility, it is generally better to use other templating features offered by System Template. This feature is designed to give you full control over file generation, but it has some drawbacks:
- Python scripts may be harder to understand for those unfamiliar with Python.
- You lose the benefits of autocompletion that you would otherwise have when using {ref}`JSON Templates<json-template>`.
- Files created directly by Python scripts are not included in the {ref}`log<debugging>`.

