(auto-mapping)=
# Auto Mapping

The AUTO mapping is a feature that allows you to automatically detect the target path of a file based on its ending. You can activate the AUTO mapping by using one of the `AUTO...` keywords in the `"target"` property of the mappings in the `_map.py` file. You can read more about the `_map.py` file in the {ref}`The _map.py File<the-mappy-file>` section. The file endings recognized by the auto mapping are defined in the `auto_map.json` file. You can read more about it in the {ref}`Custom AUTO mapping<custom-auto-mapping>` section.

There are 4 `AUTO...` keywords that you can use:

- `AUTO`
- `AUTO_SUBFOLDER`
- `AUTO_FLAT`
- `AUTO_FLAT_SUBFOLDER`

## AUTO
This way of mapping generates the target path based on the path defined in the `auto_map.json` file and the path of the source file within the system folder.

_Example:_
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO
    }
]
```
Assuming that the `auto_map.json` correctly maps the `.behavior.json` ending to the `BP/entities` path, the evaluated target path will be `BP/entities/some_path/my_behavior.behavior.json`.

## AUTO_SUBFOLDER
The `AUTO_SUBFOLDER` keyword works similarly to the `AUTO` keyword, but it inserts the {ref}`name of the system<the-name-of-the-system>` into the target path between the path defined in the `auto_map.json` file and the path of the source file within the system folder.

_Example:_
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO_SUBFOLDER
    }
]
```
The evaluated target path will be `BP/entities/my_system/some_path/my_behavior.behavior.json`.

## AUTO_FLAT
The `AUTO_FLAT` keyword uses only the path defined in the `auto_map.json` file and the name of the source file completely ignoring the path of the source file in the system folder.

_Example:_
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO_FLAT
    }
]
```
The evaluated target path will be `BP/entities/my_behavior.behavior.json`.

It's probably the most useful keyword, because it allows you to group your files in the system using folders, but keeps the exported file paths short.

## AUTO_FLAT_SUBFOLDER
The `AUTO_FLAT_SUBFOLDER` is a combination of the `AUTO_FLAT` and `AUTO_SUBFOLDER` keywords. It uses the path defined in the `auto_map.json` file, then the {ref}`name of the system<the-name-of-the-system>`, and then the name of the source file.

_Example:_
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO_FLAT_SUBFOLDER
    }
]
```
The evaluated target path will be `BP/entities/my_system/my_behavior.behavior.json`.

(the-name-of-the-system)=
## The "name of the system"
The `AUTO_SUBFOLDER` and `AUTO_FLAT_SUBFOLDER` mappings use the "name of the system" in the target path. The name of the system is a path from the root of the {ref}`group<grouped-systems>` that contains the system to the system itself. In the example above, the name of the system is `my_system`, which means that the system is not nested in any subfolders but in a different case it couples be something like `some_path/my_system`. If the system doesn't belong to any group, the name of the system is the path from the root of the system template data folder to the system.