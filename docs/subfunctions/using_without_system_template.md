(subfunctions-filter)=
# Using Without System Template
Subfunctions module can be used independently from System Template. This section describes how to do it. If you use System Template, you can skip this section.

## Installation
Run the following command in the Regolith project to make this filter available:
```
regolith install github.com/Nusiq/regolith-filters/subfunctions
```
Add the filter to the `filters` list in the `config.json` file of the Regolith project to actually enable it (the settings properties are explained in the next section):
```json
                    {
                        "filter": "subfunctions",
                        "settings": {
                            "scope_path": "subfunctions/scope.json"
                        }
                    },
```

## Configuration settings
- `scope_path: str` - a path to JSON file that diefines the scope of variables provided to the template during its evaluation. The default value of this property is `subfunctions/scope.json`. The path is relative to data folder in working directory of Regolith.
- `edit_lang_files: bool` - a flag that indicates whether the filter should edit the language files. The default value of this property is `false`.