# System Template With Scripting

```{note}
You can find the complete source code for this tutorial in the [Double Jump Boots](https://github.com/Nusiq/System-Template-Examples/tree/master/regolith/filters_data/system_template/double_jump_boots) system of the System Template Examples respository.
```

This tutorial demonstrates how to create systems that utilize the Scripting API. While System Template cannot parse JavaScript files, it can append content to them as if they were regular text files. This feature allows you to create a main script file by merging multiple smaller files together, where the main script imports the files containing the actual code.

In this example, the system implements boots that enable the player to perform a double jump.

## System Template and Scripting API Workflow

The workflow suggested in this tutorial allows you to leverage the static type checking of VS Code and use System Template to merge the files together.

Unfortunately, System Template doesn't support using TypeScript files and compiling them to JavaScript during the build process. However, you can still get a decent developer experience by using JSDoc to annotate your code. Additionally, you can obtain type hints for `@minecraft/server` in VS Code by installing it using `npm install @minecraft/server@VERSION_NUMBER` (assuming you already have NodeJS installed on your computer).

```{note}
The system from this tutorial uses `@minecraft/server@1.6.0`. If you want to get type hints for it, you should run `npm install @minecraft/server@1.6.0` in your project.
```

When working with System Template, it's recommended to have one main script in `BP/scripts/main.js`. It should import other scripts from the `subscripts` directory. Remember that you need to configure your `BP/manifest.json` properly to enable the script and use the correct version of the scripting API (in this case, it's 1.6.0).

## System File Structure

```text
📁 double_jump_boots
    📁 boots
        🗒️ double_jump_boots.attachable.json
        🖼️ double_jump_boots.attachable.png
        🗒️ double_jump_boots.bp_item.json
        🖼️ double_jump_boots.item.png
    📁 subscripts
        🗒️ double_jump_boots.js
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ item_texture.json
    🗒️ main.js
```

## System Configuration
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
        "source": "main.js",
        "target": AUTO_FLAT,
        "on_conflict": "append_end"
    },
    {
        "source": "subscripts/*.js",
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
The `main.js` file uses the `"append_end"` option for its `"on_conflict"` property. This means that the content of the script is added at the end of the file if it already exists in the project.

The files from the `subscripts` directory use the regular `AUTO` target. We want to keep the `subscripts` directory in the output path so that the import statement in the `main.js` file works.

Everything else, except `item_texture.json`, uses the `AUTO_FLAT` export target. The `item_texture.json` file is merged with the `RP/textures/item_texture.json` file. It uses direct mapping because the default `auto_map.json` file doesn't have any mapping for it. The content of the `item_texture.json` file is merged with the content of the file in the resource pack if it already exists.

## System Files

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
### double_jump_boots.attachable.png
```{image} ./system_template_with_scripting/double_jump_boots.attachable.png
:alt: double_jump_boots.attachable.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
The texture is just a recollored iron boots texture. The attachable uses the same model as the vanilla iron boots.

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
### double_jump_boots.item.png
```{image} ./system_template_with_scripting/double_jump_boots.item.png
:alt: double_jump_boots.item.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
### double_jump_boots.js
```js
//@ts-check
import {
	world,
	system,
	EntityEquippableComponent,
	EquipmentSlot,
	Player,
} from "@minecraft/server";

/**
 * Used to keep track of the double jump state of the player.
 * @enum {number}
 */
const DoubleJumpState = {
	ON_GROUND: 0,
	FIRST_JUMP_PRESSED: 1,
	FIRST_JUMP_RELEASE: 2,
	DOUBLE_JUMP_USED: 3,
};

class DoubleJumpController {
	constructor() {
		/** @type {Map<string, DoubleJumpState>} */
		this.playerStates = new Map();
	}

	tick() {
		for (let player of world.getAllPlayers()) {
			// Chek if player has boots
			/** @type {EntityEquippableComponent | undefined} */
			const inventory = /** @type {any} */ (
				player.getComponent(EntityEquippableComponent.componentId)
			);
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

	/**
	 * @param {Player} player
	 */
	tickPlayer(player) {
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
### main.js
```js
import "./subscripts/double_jump_boots.js";
```
This script is merged with the `main.js` script from behavior pack and it adds the import statement for the actual script.

## Other Files Required By The System
The `manifest.json` file needs to enable scripting and set the `scripts/main.js` as the main script. The scripting version required for this system is `@minecraft/server@1.6.0`.
