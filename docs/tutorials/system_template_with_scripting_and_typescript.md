(system-template-with-scripting-and-typescript)=
# System Template With Scripting and TypeScript

```{note}
You can find the complete source code for this tutorial in the [Double Jump Boots](https://github.com/Nusiq/System-Template-Examples/tree/3.12.0/project-2-typescript/filters_data/system_template/double_jump_boots) system of the System Template Examples respository.
```

This tutorial is a continuation of the {ref}`System Template With Scripting<system-template-with-scripting>` it shows how to switch from the pure System Template, JavaScript-based workflow to a workflow that uses the {ref}`System Template Esbuild<system-template-esbuild>` filter and TypeScript. It's recommended to read the previous tutorial first to get better understanding.

In this example, we implement the same double jump boots system as in the previous tutorial, but with TypeScript.

## System Template Esbuild Workflow

Working with the System Template Esbuild is very similar to the pure System Template workflow. However, there are some differences:
- System Template Esbuild must be installed and enabled in the Regolith project.
- The {ref}`AUTO mapping<auto-mapping>` of the scripting files must be changed, to export the files to the `data/system_template_esbuild` directory.
- The npm modules lieke `@minecraft/server` must be installed in the filters' data directory.
- Optionally, TypeScript can be used instead of JavaScript.

Using TypeScript and being able to use npm modules are the main advantages of the System Template Esbuild. System Template Esbuild also lets you minify the output.

(system-template-with-scripting-and-typescript-step-by-step)=
## Step-By-Step Guide
This section provides a step-by-step guide on how to update the existing scripting system to use TypeScript and System Template Esbuild.

### 1. System Template Esbuild Installation
{ref}`System Template Esbuild<system-template-esbuild>` is an additional filter which isn't part of the System Template. Install it using the following command:
```
regolith install system_template_esbuild
```

The filter must be added to the filters list in the `config.json` file to make it active. It should be placed **after** the System Template Filter. Here is a part of the Regolith's `config.json` file with additional comments:

```json
{
    "filter": "system_template",
    "settings": {
        "log_path": "system_template_log.json",
        "scope_path": "scope.json"
    }
},
// The System Template Esbuild filter added after the System Template filter
{
    "filter": "system_template_esbuild",
    // In this tutorial, we're using the defaults so "settings" isn't needed
    "settings": {
        "minify": false
    }
},
```

```{note}
You can find the [full content of the config.json file](https://github.com/Nusiq/System-Template-Examples/blob/3.6.0-1/regolith/config.json) on the GitHub repository linked at the beginning of this tutorial.
```

### 2. Update The AUTO Mapping 

The AUTO mapping must be updated to export the TypeScript files. Optionally, a support for JavaScript files used with System Template Esbuild can be added (even though it's not used in this tutorilal). Below is the partial content of the `auto_map.json` file:

```json
{
    ".js": "data/system_template_esbuild",
    ".ts": "data/system_template_esbuild"
}
```
When the System Template Esbuild is used, the scripts should be exported to the `data/system_template_esbuild` directory for further processing.


### 3. Npm Modules Installation

The `npm` modules must be installed inside the filters' data directory (that's where all of the filters have their data stored). To do that you may need to remove leftovers from the previous tutorial which are most likely installed in the root of the Regolith project. Simply delete the `node_modules` directory, `package-lock.json` and `package.json` files from the root of the Regolith project.

After removing the files, install the `@minecraft/server`. First, navigate to the filters' data directory (in the tutorial project the folder is called `filters_data`) and then run the following command:

```text
npm install @minecraft/server@1.6.0
```

In case of making systems that use other modules, you can install them in the same way. Note that the build-in Minecraft modules must be added to the `external` setting in the System Template Esbuild filter settings. You can find more information about this in the {ref}`System Template Esbuild documentation<system-template-esbuild>`. The `@minecraft/server	` is excluded by default so no extra configuration is needed for it.

### 4. Change the system to use TypeScript

The last thing to do is updating the system itself. All of the files are listed in the next section with additional comments explaining the changes.

## System File Structure

```text
📁 double_jump_boots
    📁 boots
        🗒️ double_jump_boots.attachable.json
        🖼️ double_jump_boots.attachable.png
        🗒️ double_jump_boots.bp_item.json
        🖼️ double_jump_boots.item.png
    📁 subscripts
        🗒️ double_jump_boots.ts
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ item_texture.json
    🗒️ main.ts
```

The only difference in comparison to the previous tutorial is that the system uses `.ts` files instead of `.js`.

## System Configuration
This section lists the configuration files (`_map.py` and `_scope.json`) and provides a brief explanation of the applied changes if needed.

### _scope.json
```json
{}
```
The system doesn't use any variables, so the scope is empty.

### _map.py
```py
[
    # Scripts
    {
        "source": "main.ts",
        "target": AUTO_FLAT,
        "on_conflict": "append_end"
    },
    {
        "source": "subscripts/*.ts",
        "target": AUTO
    },
    # Boots
    {
        "source": "boots/double_jump_boots.attachable.json",
        "target": AUTO_FLAT
    },
    {
        "source": "boots/double_jump_boots.attachable.png",
        "target": AUTO_FLAT
    },
    {
        "source": "boots/double_jump_boots.bp_item.json",
        "target": AUTO_FLAT
    },
    {
        "source": "boots/double_jump_boots.item.png",
        "target": AUTO_FLAT
    },
    # Item texture
    {
        "source": "item_texture.json",
        "target": "RP/textures/item_texture.json",
        "on_conflict": "merge"
    }
]
```

Just like in the file structure, the only difference is switching from `.js` to `.ts`. The script files will be exported to different directories, but this is handled by the AUTO mapping and a different configuration of the `auto_map.json` file (explained in the {ref}`Step-By-Step Guide<system-template-with-scripting-and-typescript-step-by-step>`) section above.

This system uses the same technique of merging files as in the previous tutorial. The main script contains the imports of the subscripts that run the actual logic. When multiple systems are merged, the imports are appended to the main script.

```{note}
Note that you have to decide whether you want to use JavaScript or TypeScript as your main file. System Template can merge the files only if all of the systems use the same language for `main`. In this tutorial, we use TypeScript, which is also a default language for System Template Esbuild.
```

## System Files
This section lists the content of the system files and provides a brief explanation of the applied changes if needed.

### double_jump_boots.attachable.json
```json
{
	"format_version": "1.8.0",
	"minecraft:attachable": {
		"description": {
			"identifier": "nusiq:double_jump_boots",
			"materials": {
				"default": "armor",
				"enchanted": "armor_enchanted"
			},
			"textures": {
				"default": "textures/attachables/double_jump_boots"
			},
			"geometry": {
				"default": "geometry.humanoid.armor.boots"
			},
			"scripts": {
				"parent_setup": "variable.boot_layer_visible = 0.0;"
			},
			"render_controllers": ["controller.render.default"]
		}
	}
}
```
_This file is the same as in the previous tutorial._

### double_jump_boots.attachable.png
```{image} ./system_template_with_scripting/double_jump_boots.attachable.png
:alt: double_jump_boots.attachable.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
_This file is the same as in the previous tutorial._

### double_jump_boots.bp_item.json
```json
{
	"format_version": "1.20.40",
	"minecraft:item": {
		"description": {
			"identifier": "nusiq:double_jump_boots"
		},
		"components": {
			"minecraft:hover_text_color": "gold",
			"minecraft:max_stack_size": 1,
			"minecraft:icon": {
				"texture": "double_jump_boots"
			},
			"minecraft:wearable": {
				"dispensable": true,
				"slot": "slot.armor.feet",
				"protection": 3
			},
			"minecraft:enchantable": {
				"value": 30,
				"slot": "armor_feet"
			},
			"minecraft:durability": {
				"damage_chance": {
					"min": 100,
					"max": 100
				},
				"max_durability": 429
			}
		}
	}
}
```
_This file is the same as in the previous tutorial._

### double_jump_boots.item.png
```{image} ./system_template_with_scripting/double_jump_boots.item.png
:alt: double_jump_boots.item.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
_This file is the same as in the previous tutorial._

### double_jump_boots.ts
```ts
import {
	world,
	system,
	EntityEquippableComponent,
	EquipmentSlot,
	Player,
} from "@minecraft/server";

/**
 * Used to keep track of the double jump state of the player.
 */
enum DoubleJumpState {
	ON_GROUND,
	FIRST_JUMP_PRESSED,
	FIRST_JUMP_RELEASE,
	DOUBLE_JUMP_USED,
}

/**
 * Used to keep track of the players who try to use the double jump.
 */
class DoubleJumpController {
	/** Maps player IDs to the double jump states of the players. */
	private playerStates: Map<string, DoubleJumpState>;

	constructor() {
		this.playerStates = new Map();
	}

	tick() {
		for (let player of world.getAllPlayers()) {
			// Chek if player has boots
			const inventory = player.getComponent(
				EntityEquippableComponent.componentId
			) as EntityEquippableComponent;
			let hasBoots = false;
			if (inventory !== undefined) {
				hasBoots =
					inventory.getEquipment(EquipmentSlot.Feet)?.typeId ==
					"nusiq:double_jump_boots";
			}

			// If has boots tick the player, otherwise remove from the map
			if (hasBoots) {
				this.tickPlayer(player);
			} else {
				this.playerStates.delete(player.id);
			}
		}
	}

	private tickPlayer(player: Player) {
		const state = this.playerStates.get(player.id);

		// Register unknown players
		if (state === undefined) {
			this.playerStates.set(player.id, DoubleJumpState.ON_GROUND);
		}

		// If on ground, change state and return
		if (player.isOnGround) {
			this.playerStates.set(player.id, DoubleJumpState.ON_GROUND);
			return;
		}
		// else: Player is not on ground
		switch (state) {
			case DoubleJumpState.ON_GROUND:
				if (player.isJumping) {
					this.playerStates.set(player.id, DoubleJumpState.FIRST_JUMP_PRESSED);
				} else {
					// Player can drop from a ledge and still double jump
					this.playerStates.set(player.id, DoubleJumpState.FIRST_JUMP_RELEASE);
				}
				break;
			case DoubleJumpState.FIRST_JUMP_PRESSED:
				if (!player.isJumping) {
					this.playerStates.set(player.id, DoubleJumpState.FIRST_JUMP_RELEASE);
				}
				break;
			case DoubleJumpState.FIRST_JUMP_RELEASE:
				if (player.isJumping) {
					// Perform the double jump and update the state
					this.playerStates.set(player.id, DoubleJumpState.DOUBLE_JUMP_USED);
					const { x, y, z } = player.getViewDirection();
					const verticalLength = Math.sqrt(x * x + z * z);
					const horizontalLength = Math.sqrt(
						verticalLength * verticalLength + y * y
					);
					player.applyKnockback(
						x,
						z,
						horizontalLength * 6,
						verticalLength * 0.5
					);
				}
				break;
		} // case DOUBLE_JUMP_USEd: pass
	}
}

// THE BODY OF THE SCRIPT
const doubleJumpController = new DoubleJumpController();
function main() {
	doubleJumpController.tick();
}
system.runInterval(main, 1);

```

### item_texture.json
```json
{
	"resource_pack_name": "pack.name",
	"texture_name": "atlas.items",
	"texture_data": {
		"double_jump_boots": {
			"textures": "textures/items/double_jump_boots"
		}
	}
}
```
_This file is the same as in the previous tutorial._

### main.ts
```ts
import "./subscripts/double_jump_boots.ts";
```

## Other Files Required By The System
The `manifest.json` file needs to enable scripting and set the `scripts/main.js` as the main script. The scripting version required for this system is `@minecraft/server@1.6.0`.
