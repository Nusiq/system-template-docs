# Using Without System Template
The JSON Template module can be used independently from System Template. This section provides instructions for standalone usage. If you are using System Template, you can skip this section.

## Installation
To make this filter available in your Regolith project, run the following command in the project directory:
```bash
regolith install github.com/Nusiq/regolith-filters/json_template
```
Once installed, integrate the filter into your Regolith project by adding it to the `filters` section of a profile in the `config.json` file:

```json
{
    "filter": "json_template",
    "settings": {
        "scope_path": "json_template/scope.json",
        "patterns": ["BP/**/*.json", "RP/**/*.json"]
    }
},
```

## Configuration Settings
- `scope_path: str`: The path to the JSON file that defines the scope of variables provided to the template during its evaluation. The default value is `json_template/scope.json`. The path is relative to the data folder in the working directory of Regolith. The scope file allows you to define variables for use in the templates.
- `patterns: List[str]`: A list of glob patterns defining the files to be processed by the filter. The default value is `["BP/**/*.json", "RP/**/*.json"]`.

Providing these settings is optional. The example above is equivalent to not providing any settings at all, as it uses the default values.
