(merging-files)=
# Merging Files
The goal of System Template is to let you group related files together into directories, to make it easier to reuse them in other projects and to make the code overall better organized. Unfortunately, it's not always possible in Minecraft. A system often needs to modify common files, shared by multiple systems. For example, you can't add new sounds to the game without modifying the `sound_definitions.json` file and you often can't add new abilities to the player without modifying the player's behavior (in system template's default setup it's the `player.behavior.json` file).

To solve this problem, System Template has multiple ways of merging files based on their types. You can enable merging files in their {ref}`Mapping Rules<the-mappy-file>` using the `on_conflict` property. You can also decide to ovewrite or ignore the files, or to throw an error if you don't expect to have any conflicts.

## Merging JSON files
The JSON files (`.json` or `.material`) have only one way of merging the. You can enable it by setting the `on_conflict` property to `"merge"`. The `"merge"` option tries to keep as much of the information from both files as possible. If it's not possible, the information from the new file is used. Here is how it works:

1. If the JSON path from the new file leads to a primitive value (string, number, boolean, null), the same value in the same JSON path is added to the merged file.
2. If the JSON path from both files leads to an array, the value in the merged file is the concatenation of both arrays with the values from the new file being added at the end.
3. If the JSON path from both files leads to an object, the value in the merged file is the result of the recursive merging of both objects.

Here are some examples of how the merging works:

_Original File:_
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

_Added File:_
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

_Merged File (with comments that indicate the source):_
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

This way of merging is best suited for most of the cases in Minecraft. It doesn't let you overwrite the values inside the arrays but that's usually what you want.

```{warning}
This way of merging doesn't preserve the order of the values in the objects. This shouldn't be a problem because the order of the values in the objects doesn't matter in JSON but it may make reading the merged files harder.
```

## Merging Text Files
The non-JSON text files like `.mcfunction` or `.lang` have 3 ways of merging them. You can enable them by setting the `on_conflict` property to `"append_start"` or `"append_end"`. Their behavior should be self-explanatory. `"append_start"` adds the content of the new file at the beginning of the existing file and `"append_end"` adds the content of the new file at the end of the existing file.

## Other Merging Options
The `on_conflict` property has other options that don't merge the files but handle the conflicts in other ways. You can read more about them in the {ref}`on_conflict <on-conflict>` section of the documentation page about th Mapping Rules.