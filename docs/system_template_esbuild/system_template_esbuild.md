(system-template-esbuild)=
# System Template Esbuild

```{warning}
This documentation applies to **System Template Esbuild 3.0.0**.
```

System Template Esbuild is a simple script compiler that uses Esbuild, designed to work with the System Template Regolith filter. It enables the use of TypeScript and npm modules in your project.

It compiles the `main.ts` file located in the `data/system_template_esbuild` directory into a single JavaScript file at a user-defined output location.

## Installation
Run the following command in your Regolith project to install this filter:
```
regolith install system_template_esbuild
```

Alternatively, you can use the direct URL to the filter:
```
regolith install github.com/Nusiq/regolith-filters/system_template_esbuild
```

To enable the filter, add it to the `filters` list in your project's `config.json` file:
```json
{
    "filter": "system_template_esbuild",
    // The settings in this example are the default settings,
    // ommitting them would result in the same behavior.
    "settings": {
        "minify": false,
        "outfile": "BP/scripts/main.js",
        "external": ["@minecraft/server"],
        "scope_path": null,
        "working_dir": null,
        "sourcemap": false
    }
}
```

## Configuration Settings

### minify: bool
Determines whether to minify the output. Optional, default is `false`.

### external: string[]
A list of external modules to exclude from the bundle, in addition to `@minecraft/server`, which is always excluded by default. Optional, default is `["@minecraft/server"]`.

### scope_path: string
Specifies the path to a JSON file containing the scope used for evaluating `outfile`. The path is relative to the filters' `data` directory and follows the same behavior as in the `system_template` filter. For example, if set to `"scope.json"`, it will reference `data/scope.json`. Optional, default is `null`.

### sourcemap: boolean
Determines whether to generate a sourcemap for the output file (`outfile`).  
For more details, see the {ref}`Debugging Setup<system-template-esbuild-debugging>` section. Optional, default is `false`.

### working_dir: string
Specifies the directory where files are copied before the build.  
For more details, see the {ref}`Debugging Setup<system-template-esbuild-debugging>` section. Optional, default is `null`.

### outfile: string
Specifies the output file for the build. Optional, default is `"BP/scripts/main.js"`.

If `scope_path` is specified, `outfile` can be evaluated using simple variable substitution. If `outfile` is a string enclosed in backticks (`` ` ``), it is treated as a template string. This is not the same as a JavaScript template string. The variables need to be surrounded by `{}` instead of `${}`. The templating also doesn't support any expressions, only simple variable substitution with variables defined in the scope file.

The usage of the patterns for the `outfile` can be used to control the paths with a variable. For example, you can have the following scope file:
```json
{
    "namespace": "my_namespace"
}
```
And the following settings in the filter:
```json
{
    "filter": "system_template_esbuild",
    "settings": {
        "scope_path": "scope.json",
        "outfile": "`BP/scripts/{namespace}/main.js`"
    }
}
```
The effective `outfile` would be `BP/scripts/my_namespace/main.js`.
