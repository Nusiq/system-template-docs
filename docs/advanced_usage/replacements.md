(replacements)=
# Replacements

Replacements are the most basic way to modify the content of the text files during the export process. They simply replace a string with another string. You can specify replacements in the {ref}`_map.py<the-mappy-file>` file using the `replacements` property.

**Example:**
```py
[
    {
        "source": "entity_handler.ts",
        "target": AUTO,
        "replacements": {
            # Simple example: replacing string with another string
            '"@replace-boss-name"': "nusiq:spooky_boss",

            # Replacing an empty object with an object from _scope.json
            "{\*@replace-friendly-entity-properties\*}": json.dumps(entity_properties)
        }
    }
]
```

The example above hints the primary use case of replacements - injecting values from the `_scope.json` files into scripts (JS/TypeScript). Of course, you can use replacements for any other purpose. The `@replace-` prefix is a convention, not a requirement. You can use any string you want.

The replacements are applied to the content of the source file, before any other operations like merging, appending or parsing (in case of JSON files). This means that if the source file is merged into a file that contains the string you're replacing, the replacement will NOT be applied to the content of the target file.