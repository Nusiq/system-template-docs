(auto-mapping)=
# Auto Mapping

AUTO mapping is a feature that enables the automatic detection of a file's target path based on its ending. To activate AUTO mapping, use one of the `AUTO...` keywords in the `"target"` property of the mappings in the `_map.py` file. For more details on `_map.py`, refer to the {ref}`The _map.py File<the-mappy-file>` section. The file endings recognized by AUTO mapping are defined in the `auto_map.json` file. For customization options, explore the {ref}`Custom AUTO mapping<custom-auto-mapping>` section.

There are 4 `AUTO...` keywords available:

- `AUTO`
- `AUTO_SUBFOLDER`
- `AUTO_FLAT`
- `AUTO_FLAT_SUBFOLDER`

## AUTO
This mapping generates the target path based on the `auto_map.json` file and the source file's path within the system folder.

**Example:**
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO
    }
]
```
Assuming `auto_map.json` correctly maps `.behavior.json` to the `BP/entities` path, the evaluated target path will be `BP/entities/some_path/my_behavior.behavior.json`.

## AUTO_SUBFOLDER
This keyword works similarly to `AUTO` but inserts the {ref}`name of the system<the-name-of-the-system>` in the target path, in front of the source file's path within the system folder.

**Example:**
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
The `AUTO_FLAT` keyword uses only the path defined in `auto_map.json` and the source file's name, ignoring its path within the system folder.

**Example:**
```py
[
    {
        "source": "some_path/my_behavior.behavior.json",
        "target": AUTO_FLAT
    }
]
```
The evaluated target path will be `BP/entities/my_behavior.behavior.json`.

This keyword is particularly useful for grouping files within the system using folders while keeping exported file paths short.

## AUTO_FLAT_SUBFOLDER
`AUTO_FLAT_SUBFOLDER` combines `AUTO_FLAT` and `AUTO_SUBFOLDER`. It uses the `auto_map.json` path, {ref}`name of the system<the-name-of-the-system>`, and the source file's name.

**Example:**
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
The `AUTO_SUBFOLDER` and `AUTO_FLAT_SUBFOLDER` mappings use the "name of the system" in the target path. The name of the system is a path from the root of the {ref}`group<grouped-systems>` that contains the system to the system itself. If the system doesn't belong to any group, the name of the system is the path from the root of the system template data folder to the system.