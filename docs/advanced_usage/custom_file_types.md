(custom-file-types)=
# Custom File Types

Sometimes, you might prefer to use a custom file extension for your files while still treating them as a specific file type recognized by the System Template, allowing you to leverage the associated features. This situation arises, for instance, when you need to merge files that use JSON syntax but have a different file extension due to external tool requirements. In such scenarios, you can utilize the `file_type` property in the mappings of the `_map.py` file.

## Syntax

**Verbose syntax:**
```py
[
    {
        "source": "some_path/my_file.templ",
        "target": "BP/entities/my_file.templ",
        "file_type": {
            "source": "json",
            "target": "json"
        },
        "on_conflict": "merge"
    }
]
```

In this example, the `file_type` property informs the System Template that the source and target files are JSON files. This enables the use of the `merge` {ref}`merging option <merging-files>` for the `on_conflict` property, which would otherwise cause an error.

**Short syntax:**
```py
[
    {
        "source": "some_path/my_file.templ",
        "target": "BP/entities/my_file.templ",
        "file_type": "json",
        "on_conflict": "merge"
    }
]
```
If the source and target file types are the same, you can use a string instead of an object for the `file_type` property.

Using the verbose syntax is useful when the source and target file types are different. Currently, the only use case for this is when you want to use the {ref}`Python Template <python-templates>` feature. In such cases, the source file type is `py`, and the target file type is `json`.

The `file_type` property can be written both with and without the leading dot (`.json` and `json` are both valid).
