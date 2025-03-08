(system-template-esbuild-debugging)=
# Debugging Setup

This section explains how to set up a VS Code project for debugging your script using the [Minecraft Bedrock Debugger](https://marketplace.visualstudio.com/items?itemName=mojang-studios.minecraft-debugger) extension and System Template Esbuild.

```{warning}
This guide assumes you have already read the {ref}`Basic Workflow<system-template-esbuild-basic-workflow>` section and are using the {ref}`recommended setup<system-template-esbuild-recommended-workflow>`.
```

## Enabling Source Maps

System Tempalte Esbuild makes it easy to compile files for Minecraft and create the source map at the same time. All you need to do is enable the `sourcemap` option and specify the `working_dir` path in the filter settings.

Example configuration:
```json
{
    "filter": "system_template_esbuild",
    "settings": {
        "sourcemap": true,
        "working_dir": "debug/script"
    }
}
```

How it works?:
- The compiled JavaScript file is placed in `BP/scripts/main.js` (default output location).
- The source map is stored in `debug/script/`.
- The `working_dir` path is relative to the root of your Regolith project.
- The `sourcemap` option is required for the Minecraft Bedrock Debugger to map TypeScript code correctly. Without it, debugging will be much harder since only compiled JavaScript will be available.

System Template Esbuild always compiles `main.ts` from its working directory, producing a `main.js` file in the same directory. After compilation, `main.js` is copied to the path specified in the `outfile` setting.

With the configuration above, running Regolith will automatically create the `debug/script` directory. This is where we can connect the Minecraft Bedrock Debugger extension to debug our script.

## Setting Up a VS Code Debugging Workspace

To streamline debugging, create a new VS Code workspace that includes the `debug` directory. If your workspace is located in the root of the Regolith project, it might look like this:

```json
{
    "folders": [
        {
            "path": "debug"
        },
        {
            "path": "local_modules"
        }
    ],
    "settings": {
        "workbench.colorTheme": "Tomorrow Night Blue"
    }
}
```

The key feature here is the addition of the `debug` folder to the workspace. Everything else is optional. The `local_modules` folder is a place where you can put your local npm modules. The change of the color theme lets you visually distinguish between the workspace you're working in and the one you're using for debugging.

## Configuring the Debugger

After setting up the workspace, you have to also add the configuration for the debugger. Add `launch.json`, `tasks.json` and `prepare_debug.bat` to the `.vscode` folder inside `debug` directory.

Your project structure should look like this:
```
📁 debug
    📁 .vscode
        📄 launch.json
        📄 tasks.json
        📄 prepare_debug.bat
    📁 script
📁 local_modules
```
And the contents of the files is shown below.

`launch.json`:
```json
{
    "version": "0.3.0",
    "configurations": [
        {
            "type": "minecraft-js",
            "request": "attach",
            "name": "Debug with Minecraft",
            "mode": "listen",
            "preLaunchTask": "Build For Debugging",
            "targetModuleUuid": "653126c0-89a0-4fc4-9e08-c84aada2c604",
            "localRoot": "${workspaceFolder}/script/",
            "sourceMapRoot": "${workspaceFolder}/script/",
            "port": 19144
        }
    ]
}
```

`tasks.json`:
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build For Debugging",
            "command": "call .vscode/prepare_debug.bat",
            "type": "shell",
            "options": {
                "shell": {
                    "executable": "cmd.exe",
                    "args": ["/d", "/c"]
                }
            },
            "problemMatcher": ["$tsc"],
            "presentation": {
                "reveal": "always"
            },
            "group": "test"
        }
    ]
}
```

`prepare_debug.bat`:
```
powershell CheckNetIsolation.exe LoopbackExempt -a -p=S-1-15-2-1958404141-86561845-1752920682-3514627264-368642714-62675701-733520436
powershell CheckNetIsolation.exe LoopbackExempt -a -p=S-1-15-2-424268864-5579737-879501358-346833251-474568803-887069379-4040235476
```

```{note}
This setup is based on a [tutorial from the official Minecraft documentation](https://learn.microsoft.com/en-us/minecraft/creator/documents/scriptdevelopertools?view=minecraft-bedrock-stable). If anything is unclear, refer to the original guide.
```

## Running the Debugger

Once everything is set up:

1. Run Regolith to compile the files.
2. Open your debugging workspace in VS Code.
3. Press `F5` to start the debugger.
4. Open the Minecraft world with your pack enabled.
5. Run the following command in Minecraft to connect the debugger:

   ```
   /script debugger connect
   ```

```{note}
You may need to enable Developer Mode in Windows to allow the debugger to function correctly.
```
