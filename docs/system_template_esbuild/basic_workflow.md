(system-template-esbuild-basic-workflow)=
# Basic Workflow

The System Template Esbuild filter is simple and does not handle dependencies automatically. This means you will need to perform some additional setup on your own:

## 1. Install Modules Using `npm`

Installing modules serves two purposes:  

1. It makes them available for your IDE, enabling autocompletion and type checking.  
2. It ensures they are available for Regolith during the build process.  

Installing `@minecraft/server` is not required for this filter to work because it is excluded from the bundle by default. However, having it installed is still useful for IDE support.  

If you need additional modules, install them using `npm`:  

```
npm install <your-dependency>
```

There are two approaches to installing modules:  

1. **Installing them in the project root (recommended)**  
2. **Installing them in the `data` directory**  

(system-template-esbuild-recommended-workflow)=
### Installing Modules in the Project Root (Recommended)

Installing Node modules in the root directory prevents Regolith from copying them to the [Regolith project cache](https://regolith-docs.readthedocs.io/en/1.5.1/using-regolith/regolith-cache/#project-cache) during compilation. This has advantages and disadvantages:

Advantages:

- Faster build times, as Regolith does not copy the `node_modules` folder during (it can be quite large).
- Compatible with the {ref}`Debugging Setup<system-template-esbuild-debugging>` workflow described in the other section.

Disadvantages:

- This method does not work if the [use_project_app_data_storage](https://regolith-docs.readthedocs.io/en/1.5.1/using-regolith/user-configuration/#use-project-app-data-storage-bool) setting is enabled in Regolith.

### Installing Modules in the `data` Directory (not recommended)
You should only install modules in the `data` directory if you are using the `use_project_app_data_storage` setting in Regolith. In this case, navigate to the `data` directory before running `npm install`:

```
cd data
npm install <your-dependency>
```

```{warning}
Do not confuse `data` with `data/system_template` or `data/system_template_esbuild`. This workflow requires installing modules directly in the `data` directory of your Regolith project.
```

## 2. Add `@minecraft/server` to `BP/manifest.json`

To enable scripting in your behavior pack, modify `BP/manifest.json`. Add a reference to your script in the `modules` array and define `@minecraft/server` as a dependency.


```json
{
    "format_version": 2,
    "header": {
        // ...
    },
    "modules": [
        // ...
        {
            "language": "javascript",
            "type": "script",
            // Change the UUID to a new one
            "uuid": "05f26eb6-522a-414d-8e85-94f07c2b3795",
            "version": [0, 0, 1],
            // Ensure this matches the 'outfile' setting in the filter
            "entry": "scripts/main.js"
        }
    ],
    "dependencies": [
        // ...
        {
            // Use the same version as the one installed via npm
            "module_name": "@minecraft/server",
            "version": "1.9.0"
        }
    ]
}
```

To install a specific module version, use the `@` symbol followed by the version number. For example, to install `@minecraft/server` version `1.9.0`:

```
npm install @minecraft/server@1.9.0
```

You can check available versions of `@minecraft/server` on its [npm page](https://www.npmjs.com/package/@minecraft/server). Ensure that the version in `BP/manifest.json` matches the one installed via npm.

```{warning}
If your project includes built-in Minecraft dependencies (e.g., `@minecraft/server`), exclude them from the build using the `external` setting in `config.json`.  
An example of such a module is `@minecraft/server-ui`.
```

## 3. Configure AUTO Mapping

If you are using System Template Esbuild with the System Template Regolith Filter, ensure that the {ref}`AUTO mapping<custom-auto-mapping>` is configured correctly.  

System Template Esbuild always uses `data/system_template_esbuild/main.ts` as the Esbuild `entryPoint`. This means script files from your systems should be placed in the `system_template_esbuild` directory.

Ensure that System Template defines the {ref}`AUTO mapping<custom-auto-mapping>` for `.ts` and `.js` files, exporting them to `data/system_template_esbuild`. Additionally, verify that `system_template` runs before `system_template_esbuild` in the filter chain.

Modify `auto_map.json` to include the following lines:

```json
{
    ".js": "data/system_template_esbuild",
    ".ts": "data/system_template_esbuild"
}
```

To prevent name conflicts when using `system_template` and `system_template_esbuild`, structure your files as follows:

1. Each system should export two (or more) files:``
    - `main.ts` - this is merged with the original `main.ts` using the `"append_end"` option in the {ref}`on_conflict<on-conflict>` setting in System Template.  
    - Other files contain the actual code and are imported by `main.ts`.  

This approach ensures a clean separation of logic while preventing potential conflicts.
