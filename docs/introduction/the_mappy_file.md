(the-mappy-file)=
# The `_map.py` File

The `_map.py` file is a configuration file that defines how files inside the system should be mapped to output files. While it's a Python file, less advanced users can treat it as a JSON file with a list of objects. Python was chosen to allow for more complex configurations using Python features. The `_map.py` file must contain a single Python expression that evaluates to a list of dictionaries.

```{note}
A single expression in Python can be very powerful. Creating complex objects based on adding multiple lists created with comprehensions is considered a single expression.
```

## Structure of the `_map.py` file

The `_map.py` should evaluate into a list of dictionaries. Each dictionary defines a single mapping.

**Example `_map.py` file:**
```py
[
    {
        "source": "example.behavior.json",
        "target": AUTO_FLAT,
    },
    {
        "source": "sound_definitions.json",
        "target": "RP/sounds/sound_definitions.json",
        "on_conflict": "append_end",
    }
]
```

This simple example doesn't show all features of the `_map.py` file but is sufficient to understand the basic structure. It maps two files, one using {ref}`AUTO mapping<auto-mapping>` and the other using `"append_end"` for the `on_conflict` property, meaning that if the target file exists, the content of the new file will be appended at the end of the old one. The exact structure of the mapping is explained in the next section.

## Mapping structure

Every object in the `_map.py` list follows the same structure.

**Required properties:**
- `source`
- `target`

**Optional properties:**
- `scope`
- `json_template`
- `on_conflict`
- `subfunctions`
- `file_type`
- `export_once`

### source

The path to the source file relative to the folder of the system. The value of the source property can be a [glob pattern](https://en.wikipedia.org/wiki/Glob_(programming)). Using glob patterns is useful in combination with the {ref}`AUTO mapping<auto-mapping>` feature, allowing automatic output path determination based on file names and extensions.

### target

The `target` property specifies the export destination for files identified by the `source`. It can either be a string representing a path or one of the keywords utilized in auto-mapping.

When using a path string, start with `RP/`, `BP/`, or `data/`, indicating the export to the resource pack, behavior pack, or data folder, respectively.

The `target` path can be a filename or a directory. If the path ends with a forward slash (`/`), the file is exported to the directory with the same name as the source file. If the path doesn't end with a forward slash, the file is exported with the specified name. Using directory paths is especially useful when combined with glob patterns in the `source` property.

```{warning}
If a system uses the `data/` target and exports to a directory of a self-modifying filter (a filter utilizing `"exportData": true`), the changes applied by System Template will become permanent. This is because filters with the `exportData` flag retain changes applied to their directory during the execution of the `regolit run` command.
```

If the target value corresponds to an auto-mapping keyword, the destination is determined by the rules outlined in the {ref}`AUTO mapping<auto-mapping>` section.

### scope

The `scope` property can set the local scope of variables for evaluating the source file. This property overrides other scopes. It's especially useful when generating the content of the `_map.py` file using Python's comprehension.

Consider the following example:

```py
[
    {
        "source": "template.behavior.json",
        "target": f"BP/entities/{entity_name}.behavior.json",
        "scope": {
            "name": entity_name
        },
        "json_template": True
    }
    for entity_name in ["entity1", "entity2", "entity3"]
]
```

In this example, the `scope` property introduces the variable `name` in the local scope. This variable is then utilized in the `target` property, demonstrating how you can dynamically generate file paths. To explore more advanced features related to code generation, refer to the respective sections on {ref}`Python Templates<python-templates>`, {ref}`JSON Templates<json-template>`, and {ref}`Subfunctions<subfunctions>`.

### json_template

This property is only available for mappings with the source file being a `.json` or `.material` file. Setting it to `True` enables the evaluation of the file using the `json_template` module. Read more about it in the {ref}`JSON Template<json-template>` section.

(on-conflict)=
### on_conflict
The `on_conflict` property defines the behavior of the System Template in case of conflicting files (multiple files mapped to the same output path). Here are the available options:

- `stop` - Breaks the execution and raises an error. Default for most file types.
- `append_start` - Inserts the content of the file in front of the existing file. Only for non-JSON text files.
- `append_end` - Appends the content of the file at the end of the existing file. Default for `.lang` files.
- `overwrite` - Overwrites the content of the existing file.
- `skip` - If the file already exists, nothing happens.
- `merge` - Merges the content of the file with the existing file. Only for JSON files (`.json` and `.material`).

For more details on handling conflicting files, refer to the {ref}`Merging Files<merging-files>` section.

### subfunctions

The `subfunctions` property enables/disables the evaluation of the file using the `subfunctions` module. It's only available for `.mcfunction` and `.lang` files. It is enabled by default for `.mcfunction` files and disabled for `.lang` files. Read more about the `subfunctions` module in the {ref}`Subfunctions<subfunctions>` section.

### file_type

The `file_type` property informs System Template about the file's type, both for the source and target files. It takes precedence over the default behavior determined by the file extension. This feature proves beneficial when the file extension does not align with the actual file type, which can be essential when working with tools that require a specific file extension but operate with a file format recognized by System Template. For further details on the `file_type` property, refer to the {ref}`Custom File Types <custom-file-types>` section.

### export_once

The export_once property lets you mark the files to be exported only once in case of multiple mappings of the same file to the same target. You can read more about this feature in the {ref}`Export Once<export-once>` section.