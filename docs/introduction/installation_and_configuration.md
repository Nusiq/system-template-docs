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
                "systems": ["**/*"],
                "prioritized_systems": [
                    "full/path/to/system1",
                    "full/path/to/system2"
                ],
                "scope": {
                    "some_variable": "some_value"
                },
                "namespace": {
                    "hook": "nusiq",
                    "target": "xyz",
                    "keep_hook": true
                }
            }
        },
```

(configuration-settings)=
## Configuration Settings
Configuration settings should be placed in the `settings` property of the filter in the `config.json` file of your Regolith project. All settings are optional and have default behavior if not set.

### scope_path: str
The scope_path is the path to a JSON file defining the global scope of variables. You can learn more about scopes in the {ref}`Scopes And Variables<scopes-and-variables>` section of the documentation. The default path is set to `system_template/scope.json`.

### scope: dict
The scope property is a dictionary of variables that will be available in the templates. No variables are set by default in this scope. The variables defined in the `scope` property will override the variables defined in the `scope_path` file. You can learn more about scopes in the {ref}`Scopes And Variables<scopes-and-variables>` section of the documentation.

### systems: list[str]
The systems property is a list of glob patterns defining which system folders the filter should process. The default value is `["**/*"]`, meaning all system folders will be processed.

### log_path: str
The `log_path` is the path to a file where the log will be saved. The log contains information about which files are responsible for creating the output files and in what way. It's useful for debugging. Learn more about debugging in the {ref}`Debugging<debugging>` section of the documentation. There is no default value for this property; if not set, the log will not be saved.

### prioritized_systems: list[str]
The ordered list of the system paths that should be processed first. The items earlier in the list will be processed before the items later in the list. The default value is an empty list. Unspecified systems are be processed later, based on the alphabetical order of their full paths (including their {ref}`group paths<grouped-systems>`). The paths specified in the list should be relative to the system_tempalte data directory.

### namespace: dict
The `namespace` property is a convinient way to define replacement rules for the text files for System Template that effectively change the namespaces used in generated files.

Namespace property is a dictionary that has 3 properties - `hook: str`, `target: str`, and `keep_hook: bool`. If you add `namespace` to your configuration, you have to specify `hook` and `target`. The `keep_hook` property is optional and defaults to `false`.

The details of the `namespace` property are explained in the {ref}`Namespacing<namespacing>` section of the documentation.

### replacements: dict[str, str]
This property lets you define global replacement rules applied to all of the text files in every system. This feature is similar to `namespace`, but it's more flexible. The global replacements are combined with the replacements defined in the `_map.py` files. You can learn more about replacements in the {ref}`Replacements<replacements>` section of the documentation.
