# Using Without System Template
JSON Template module can be used independently from System Template. This section describes how to do it. If you use System Template, you can skip this section.

## Installation
Run the following command in the Regolith project to make this filter available:
```
regolith install github.com/Nusiq/regolith-filters/json_template
```
When you have done that, you can use the filter in your Regolith project by adding it to the `filters` section of a profile in the `config.json` file of the Project:
```json
                    {
                        "filter": "json_template",
                        "settings": {
                            "scope_path": "json_template/scope.json",
                            "'patterns": ["BP/**/*.json", "RP/**/*.json"]
                        }
                    },
```
## Configuration settings
- `scope_path: str` - a path to JSON file that diefines the scope of variables provided to the template during its evaluation. The default value of this property is `json_template/scope.json`. The path is relative to data folder in working directory of regolith. Scope file lets you define variables that can be used in the templates.
- `patterns: List[str]` - a list of glob patterns that define the files that will be processed by the filter. The default value of this property is `["BP/**/*.json", "RP/**/*.json"]`.

Providing the settings is optional. The example above is equivalent to not providing any settings at all as it uses the default values.