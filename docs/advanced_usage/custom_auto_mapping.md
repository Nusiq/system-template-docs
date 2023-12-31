(custom-auto-mapping)=
# Custom Auto Mapping

When installing the `system_template` Regolith filter in your project for the first time, it will create the default `auto_map.json` file. This file defines the behavior of the {ref}`AUTO mapping<auto-mapping>` feature. The default setup should be sufficient for most users, and it's recommended to keep it that way for your convenience and that of others in case of collaboration.

The default values have been chosen to be as universal as possible and to work well with other popular software for Minecraft Bedrock Edition Addon development. All of the default extensions are recognized by the [Blockception's Minecraft Bedrock Development](https://marketplace.visualstudio.com/items?itemName=BlockceptionLtd.blockceptionvscodeminecraftbedrockdevelopmentextension) VS Code extension, and the resource pack files use the `.animation.json` extension, which is recognized by the [Blockbench](https://www.blockbench.net/) modeling software.

If you want to change the default behavior, you can edit the `auto_map.json` file or add your own rules. This section explains how to do that.

```{note}
If you think the default `auto_map.json` file is missing important rules, please consider creating an issue or a pull request on the project's [GitHub repository](https://github.com/Nusiq/regolith-filters/tree/master/system_template).
```

## Setting Up the auto_map.json File

The `auto_map.json` file should be placed in the root directory of the data files of the `system_template` Regolith filter. The file contains a dictionary of rules that map the file endings to the target paths. The keys of the dictionary are the file endings, and the values are the target paths. Optionally, the values can be objects that contain the `target` property and the `replace_extension` property.

The `target` property is the target path; it's the same as just using the string instead of the object as the value of the mapping.

The `replace_extension` property is used to replace the mapped file ending with a different one. This feature has 3 use cases:
- Mapping `.py` files (for {ref}`Python Templates<python-templates>`) to create `.json` files in the output.
- Making the output files shorter by removing some part of the file ending.
- Mapping files that have possible multiple target paths to desired target paths. For example, `.png` files can be used as item textures, entity textures, block textures, etc. You can create longer file endings for each of these cases and then use the `replace_extension` to remove the part used to distinguish the cases.

The example below illustrates these use cases using some of the rules from the default `auto_map.json` file:

```json
{
  ".geo.json": "RP/models/entity",
  ".entity.png": {
    "target": "RP/textures/entity",
    "replace_extension": ".png"
  },
  "_entity.png": "RP/textures/entity",
  ".bp_ac.py": {
    "target": "BP/animation_controllers",
    "replace_extension": ".bp_ac.json"
  }
}
```

This example defines the following rules:

- Files ending with `.geo.json` will be placed in the `RP/models/entity` folder.
- Files ending with `.entity.png` will be placed in the `RP/textures/entity` folder, but the `.entity.png` ending will be replaced with `.png`.
- Files ending with `_entity.png` will be placed in the `RP/textures/entity` folder. This is a leftover from the older version of the System Template, where the `replace_extension` property didn't exist. It's kept for compatibility reasons, and it's recommended to use the `.entity.png` ending instead.
- Files ending with `.bp_ac.py` will be placed in the `BP/animation_controllers` folder, but the `.bp_ac.py` ending will be replaced with `.bp_ac.json`. Note that when you change the extension from `.py` to `.json`, the content of the Python file is evaluated and then saved using the JSON format.
