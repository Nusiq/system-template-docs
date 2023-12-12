(installation-and-configuration)=
# Installation And Configuration

## Installation
Run the following command in the Regolith project to make this filter
available:
```
regolith install system_template
```
Add the filter to the `filters` list in the `config.json` file of the Regolith
project to actually enable it (the settings properties are explained in the
next section):
```
                    {
                        "filter": "system_template",
                        "settings": {
                          "scope_path": "system_template/scope.json",
                          "systems": ["**/*"]
                        }
                    },
```

## Configuration settings
Configuration settings should be placed in the `settings` property of the filter in the `config.json` file of the Regolith project. All settings are optional and have a default behavior if not set.

### scope_path: str

Scope path is a path to JSON file that defines the global scope of variables. You can read more about the scopes in the {ref}`Scopes And Variables<scopes-and-variables>` section of the documentation.

By default the scope path is set to `system_template/scope.json`.

### systems: list[str]

The `systems` property is a list of glob patterns that define which system folders should be processed by the filter.

The default value is `["**/*"]` which means that all system folders will be processed.

### log_path: str

The `log_path` property is a path to a file where the log will be saved. The log contains information about which files are responsible for creating the output files and in what way. It's useful for debugging. You can read more about debugging in the {ref}`Debugging<debugging>` section of the documentation. 

There is no default value for this property. If it's not set, the log will not be saved.