(system-template-esbuild)=
# System Template Esbuild

```{warning}
This documentation applies to **System Template Esbuild 1.0.1**
```
A simple script compiler that uses Esbuild designed to be used with the System Template Regolith Filter. It enables the use of typescript and npm modules in your project, while still keeping your code grouped into systems.

## Installation
Run the following command in the Regolith project to make this filter
available:
```
regolith install system_template_esbuild
```

Alternatively, you can use the direct URL to the filter:
```
regolith install github.com/Nusiq/regolith-filters/system_template_esbuild
```


Add the filter to the `filters` list in the `config.json` file of the Regolith project to enable it:
```json
{
    "filter": "system_template_esbuild",
    // The settings in this example are the default settings, ommitting them
    // would result in the same behavior.
    "settings": {
        "minify": false,
        "entryPoint": "data/system_template_esbuild/main.ts",
        "outfile": "BP/scripts/main.js",
        "external": ["@minecraft/server"],
        "scope_path": null
    }
}
```

## Settings
The filter supports the following settings:
- `minify: boolean` (optional, default `false`) - Whether to minify the output.
- `entryPoint: string` (optional, default `'data/system_template_esbuild/main.ts'`) - The entry point for the build.
- `outfile: string` (optional default `'BP/scripts/main.js'`) - The output file for the build.
- `external: string[]` (optional, default `["@minecraft/server]`) - A list of external modules to exclude from the bundle in addition to '@minecraft/server' which is included by default.
- `scope_path: string` (optional, default `null`) - The path to the JSON file containing the scope used for evaluating entryPoint and outfile. The path starts in the filters' `data` directory. It works exactly the same as in `system_template` filter (e.g. if you set the path to `scope.json`, it will evaluate to `data/scope.json`)

The evaluation of the `entryPoint` and `outfile` is based on simple substitution of variables. If `entryPoint` or `outfile` are strings that start and end with a backtick ('`` ` ``'), then the string is evaluated as a template string. This is not the same as a JavaScript template string. The variables need to be surrounded by `{}` instead of `${}`. The templating also doesn't supprot any expressions, only simple variable substitution with variables defined in the scope file.

The usage of the patterns for the `entryPoint` and `outfile` can be used to control the paths with a variable, in the same way as described in the {ref}`namespacing tutorial<namespacing-addons>`. For, example, you can have the following scope file:
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

## Workflow

This filter is very simple and doesn't handle the dependencies in any way. This means that you will need to perfrom some additional setup on your own:

### 1. Install the modules using `npm`
Installing the modules has two purposes. First, it makes the modules available for your IDE, so you can get autocompletion and type checking. Second, it makes the modules available for Regolith during the build. Installing `@minecraft/server` is not needed for the filter to work because this dependency is excluded from the bundle by default but it's still nice to have for the IDE.

If you want to add other modules, you will need to install them using `npm`. Simply run `npm install <your-dependency>` in the `data` directory. This will create a `node_modules` directory, `package-lock.json` and `package.json` in the `data` directory.

```{warning}
Make sure not to confuse `data` with `data/system_template` or `data/system_template_esbuild` directories. The modules should be installed directly in `data`. This way they will be available for all of the filters, while still being accessible for Regolith during the build.
```

```{warning}
If your additional dependencies are build into Minecraft, like `@minecraft/server`, you will need to exclude them from the build using the `external` setting. `@minecraft/server-ui` is an example of such a module.
```

If you want to install a specific version of a module, you can use the `@` symbol followed by the version number. For example, to install version `1.9.0` of `@minecraft/server` you would run `npm install @minecraft/server@1.9.0`. You can check the available versions of `@minecraft/server` on the [npm page](https://www.npmjs.com/package/@minecraft/server).

### 2. Add `@minecraft/server` to `BP/manifest.json`

In order to enable scripting in your behavior pack, you need to modify the `BP/manifest.json`. Add a reference to your script in the `modules` array and add define the `@minecraft/server` as a dependency.


```json
{
    "format_version": 2,
    "header": {
        // ...
    },
    "modules": [
        //. ...
        {
            "language": "javascript",
            "type": "script",
            // Change the uuid to a new one
            "uuid": "05f26eb6-522a-414d-8e85-94f07c2b3795",
            "version": [0, 0, 1],
            //  The entry should point at the same file as the outfile in the
            // filter settings
            "entry": "scripts/main.js"
        }
    ],
    "dependencies": [
        // ...
        {
            // Make sure you use the same version you installed with npm
            "module_name": "@minecraft/server",
            "version": "1.9.0"
        }
    ]
}
```


### 3. Configure the AUTO mapping.

You might have noticed that the default `entryPoint` in the filter settings is `data/system_template_esbuild/main.ts`. Keeping the script in the `data` directory (or its subdirectories) is necessary because that's where the npm modules are installed, and they're essential for the build. By default, System Template defines the {ref}`AUTO mapping<custom-auto-mapping>` for `.js` files to export them to the `BP/scripts` directory. If you're using System Template Esbuild, you should change the export path to `data` or some subdirectory of `data` (just make sure that your main script is the set as the `entryPoint` in the filter settings).

The modified `autom_map.json` file should contain following lines:
```json
{
    ".js": "data/system_template_esbuild",
    ".ts": "data/system_template_esbuild"
}
```
