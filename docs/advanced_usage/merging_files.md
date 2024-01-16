(merging-files)=
# Merging Files
The primary goal of System Template is to enable users to group related files together into directories, making it easier to reuse them in other projects and maintaining organized code. However, in Minecraft, it's common for a system to need modifications to common files shared by multiple systems. For example, you can't add new sounds to the game without modifying the `sound_definitions.json` file, and introducing new abilities to the player often involves adjustments to the player's behavior (in the default setup of System Template, this corresponds to the `player.behavior.json` file).

To solve this problem, System Template offers various methods for merging files based on their types. You can enable file merging in your {ref}`Mapping Rules<the-mappy-file>` by using the `on_conflict` property. You also have the option to overwrite or ignore conflicting files or throw an error if you don't expect any conflicts.

Merging files depends on their type. The following sections describe what merging options are available for each file type. If you need to treat a file as a different type, you can override its type using the {ref}`file_type <custom-file-types>` property.

## Merging JSON Files
JSON files (`.json` or `.material`) support a single method of merging, which can be enabled by setting the `on_conflict` property to `"merge"`. The `"merge"` option tries to keep as much information from both files as possible, with the following merging rules:

1. If the JSON path from the new file leads to a primitive value (string, number, boolean, null), the same value in the same JSON path is added to the merged file.
2. If the JSON path from both files leads to an array, the value in the merged file is the concatenation of both arrays, with the values from the new file added at the end.
3. If the JSON path from both files leads to an object, the value in the merged file results from the recursive merging of both objects.

Here are some examples illustrating how merging works:

**Original File:**
```json
{
    "A": [
        1,
        {"x": 1, "y": 2, "z": 3}
    ],
    "B": true,
    "C": {
        "x": 1,
        "y": 2,
        "z": 3
    },
    "D": "Hello World!"
}

```

**Added File:**
```json
{
    "A": [
        11,
        {"x": 11, "y": 22, "z": 33}
    ],
    "B": false,
    "C": {
        "x": 11,
        "y": 22,
        "z": 33
    }
}
```

**Merged File (with comments indicating their origin):**
```json
{
    "A": [                          // Values are concatenated
        1,                          // Original File
        {"x": 1, "y": 2, "z": 3},   // Original File
        11,                         // Added File
        {"x": 11, "y": 22, "z": 33} // Added File
    ],
    "B": false,                     // Added File (overwrites)
    "C": {
        "x": 11,                    // Added File (overwrites recursively)
        "y": 22,                    // Added File (overwrites recursively)
        "z": 33                     // Added File (overwrites recursively)
    },
    "D": "Hello World!"             // Original File (unchanged)
}
```

This merging approach is well-suited for most cases in Minecraft, as it does not allow overwriting values inside arrays, which is typically desirable.

```{warning}
This method of merging does not preserve the order of values in objects. While the order of values in objects does not matter in JSON, it may make reading the merged files more challenging.
```

## Merging Text Files
Non-JSON text files like `.mcfunction` or `.lang` provide 3 ways of merging, which can be enabled by setting the `on_conflict` property to `"append_start"` or `"append_end"`. The behavior is self-explanatory: `"append_start"` adds the content of the new file at the beginning of the existing file and `"append_end"` adds the content of the new file at the end of the existing file.

## Other Merging Options
The `on_conflict` property includes additional options (`"stop"`, `"skip"` and `"overwrite"`) that do not merge the files but handle conflicts in alternative ways. Further details can be found in the {ref}`on_conflict <on-conflict>` section of the documentation page about Mapping Rules.
