(installation-and-configuration)=
# Installation And Configuration
## Installation
To make the System Template filter available in your Regolith project, run the following command:

```text
regolith install system_template
```

To enable the filter, add the following configuration to the filters list in the `config.json` file of your Regolith project. Adjust the settings properties as needed (explained in the next section):

```json
        {
            "filter": "system_template",
            "settings": {
                "scope_path": "system_template/scope.json",
                "systems": ["**/*"]
            }
        },
```

## Configuration Settings
Configuration settings should be placed in the `settings` property of the filter in the `config.json` file of your Regolith project. All settings are optional and have default behavior if not set.

### scope_path: str
The scope_path is the path to a JSON file defining the global scope of variables. You can learn more about scopes in the {ref}`Scopes And Variables<scopes-and-variables>` section of the documentation. The default path is set to `system_template/scope.json`.

### systems: list[str]
The systems property is a list of glob patterns defining which system folders the filter should process. The default value is `["**/*"]`, meaning all system folders will be processed.

### log_path: str
The `log_path` is the path to a file where the log will be saved. The log contains information about which files are responsible for creating the output files and in what way. It's useful for debugging. Learn more about debugging in the {ref}`Debugging<debugging>` section of the documentation. There is no default value for this property; if not set, the log will not be saved.