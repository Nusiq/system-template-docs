(the-mappy-file)=
# The `_map.py` File

The `_map.py` file is a configuration file that defines how the files inside the system should be mapped to the output files. It's a Python file but less advanced users can treat it as a JSON file with list of objects. The choice of Python as the configuration language was made to allow for more complex configurations using Python features. The `_map.py` file must contain a single Python expression that evaluates to a list of dictionaries.

```{note}
A single expression in Python can be very powerful. Creating complex objects based on adding multiple lists created with comprehensions is considered a single expression.
```

## Structure of the `_map.py` file

The `_map.py` should evaluate into a list of dictionaries. Each dictionary defines a single mapping. 

_Example `_map.py` file:_
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

This simple example doesn't show all of the features of the `_map.py` file, but it's enough to understand the basic structure and some of the features. It maps two files. The first file uses {ref}`AUTO mapping<auto-mapping>` to automatically find the output path for the file. The second file uses `"append_end"` as the `on_conflict` property, which means that if the target file already exists, the content of the new file will be appended at the end of the old one. The exact structure of the mapping is explained in the next section.

## Mapping structure
Every object in the `_map.py` list follows the same structure.

Required properties:
- `source`
- `target`

Optional properties:
- `scope`
- `json_template`
- `on_conflict`
- `subfunctions`


### source
The path to the source file relative to the folder of the sytem. The value of the source property can be a [glob pattern](https://en.wikipedia.org/wiki/Glob_(programming)). Using glob patterns is useful in combination with the {ref}`AUTO mapping<auto-mapping>` feature which allows you to automatically find the output path for the files based on their names and extensions. 

Some source paths can have a `SHARED:` prefix, to mark the srouce as a shared resource (bewteen multiple systems). In that case, using glob patterns is not allowed. You can read more about shared resources in the {ref}`Shared Files<shared-files>` section.

### target
The `target` property is used to define where the files matched by the `source` should be exported. It can be a string or one of the keywords used for the auto mapping. You can read more about the auto mapping in the {ref}`AUTO mapping<auto-mapping>` section.

If the target is a string, it's treated as a direct value for the output path. It must start with `RP/` or `BP/` to specify if the file should be exported to the Resource Pack or Behavior Pack respectively.

### scope
Scope property can set local scope of variables for evaluating the source file. System Template has multiple features that allow you to generate the files. You can read more about them in their own documentation sections:
- {ref}`Python Templates<python-templates>`
- {ref}`Json Template<json-template>`
- {ref}`Subfunctions<subfunctions>`

The scope provided directly in the `_map.py` file overrides the other scopes. You can read more about the scopes in the {ref}`Scopes and Variables<scopes-and-variables>` section. Using the `scope` property is especially useful when you generate the content of the `_map.py`  file using Python's comprehension.

_Example:_
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
This setup will generate 3 files using a single source file (`template.behavior.json`). Each file is generated using a different scope, with different `name` variable.

### json_template
This property is only available for mappings with the source file being a `.json` or `.material` file. Setting it to `True` enables the evaluation of the file using the `json_template` module. You can read more about it in {ref}`JSON Template<json-template>` section.

(on-conflict)=
### on_conflict
The `on_conflict` property defines the behavior of the System Template in case of conflicting files (multiple files mapped to the same output path). There is multiple settings available and their default values depend on the extension of the target file:

- `stop` - breaks the execution and raises an error. This is the default value for all file types unless other mode below specifies otherwise.
- `append_start` - the content of the file is inserted in front of the already existing file. This mode is only available for the non-JSON text files.
- `append_end` - the content of the file is appended at the end of the already existing file. This mode is only available for the non-JSON text files. This is the default value for the `.lang` files.
- `overwrite` - the content of the file overwrites the already existing file.
- `skip` - If file already exists, nothing happens.
- `merge` - the content of the file is merged with the already existing file. This mode is only available for the JSON files (`.json` and `.material`).

The files with different extensions than `.json`, `.mcfunction` and `.lang` can only use the `stop`, `overwrite` and `skip` options.

Merging files is one of the most powerful features of the System Template and it has its own documentation section. You can read more about it in the {ref}`Merging Files<merging-files>` section.

### subfunctions
The `subfunctions` property enables/disables the evaluation of the file using the `subfunctions` module. It's only available for the `.mcfunction` and `.lang` files. It is enabled by default for the `.mcfunction` files and disabled for the `.lang` files. You can read more about the `subfunctions` module in the {ref}`Subfunctions<subfunctions>` section.




