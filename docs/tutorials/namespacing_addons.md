(namespacing-addons)=
# Namespacing Addons

```{note}
You can find the complete source code for this tutorial in the [Snail](https://github.com/Nusiq/System-Template-Examples/tree/3.12.0/project-3-namespacing/filters_data/system_template/snail) system of the System Template Examples respository.
```

```{image} ./namespacing_addons/result.png
:alt: result.png
:class: bg-primary
:width: 100%
:align: center
```

When your addon needs to coexist with others, there's a risk of name collisions, especially when you lack control over the other addons. System Template can aid in avoiding this issue.

In this tutorial, you'll learn:
- How to create a system that automatically adds a project specific namespace based on the addon's configuration (using the {ref}`Namespacing<namespacing>`).

The system in the tutorial uses `nusiq` as a namespace (this is the string that will be replaced in the files). The generated files use a combination of the namespace from the system (`nusiq`) and the target namespace (`xyz`). It's a common practice for namespacing add-ons, to use a mix of your personal identifier and some short code for the project, but it's more convenient to always use the same identifier in your source, to make your systems reusable. This is where the namespacing feature comes in handy.

```{warning}
If this tutorial feels pointlessly easy, that's because namespacing used to be harder before the {ref}`namespace property<configuration-settings>` was introduced. Now, you basically just work the same way you would without namespacing. This tutorial is an updated version of the old tutorial that was more complex.
```

## System File Structure

```text
📁 snail
    📁 visuals
        🗒️ snail.animation.json
        🗒️ snail.geo.json
        🖼️ snail.entity.png
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ snail.behavior.json
    🗒️ snail.entity.json
```

## System Configuration

### _scope.json

```json
{}
```

The system doesn't use any custom variables, so the scope is empty.

### _map.py

```python
[
    # Visuals
    {
        "source": "visuals/*.geo.json",
        "target": AUTO_FLAT
    },
    
    {
        "source": "visuals/*.animation.json",
        "target": AUTO_FLAT
    },
    {
        "source": "visuals/*.entity.png",
        "target": AUTO_FLAT
    },
    # Entity definition
    {
        "source": "snail.behavior.json",
        "target": AUTO_FLAT,
    },
    {
        "source": "snail.entity.json",
        "target": AUTO_FLAT,
    }
]
```
Note that none of the files in the system use {ref}`JSON Template<json-template>`. That's because namespacing isn't the same as just using variables. It's more similar to {ref}`Replacements<replacements>` feature, but specialized for changing namespaces.

## System Files

You can see below that there is absolutely nothing special about the system. The namespacing is fully handled by System Template. The files are listed below:

### visuals/snail.animation.json

```json
{
	"format_version": "1.8.0",
	"animations": {
		"animation.nusiq.snail.walk": {
			"loop": true,
			"anim_time_update": "q.modified_distance_moved*0.3",
			"animation_length": 1,
			"bones": {
				"body": {
					"scale": [1, 1, "math.sin(q.anim_time*360)*0.1+1"]
				},
				"shell": {
					"position": [0, "-math.sin(q.anim_time*360)*0.1", 0]
				}
			}
		}
	}
}
```

### visuals/snail.geo.json
```json
{
	"format_version": "1.16.0",
	"minecraft:geometry": [
		{
			"description": {
				"identifier": "geometry.nusiq.snail",
				"visible_bounds_width": 2.0,
				"visible_bounds_height": 2.5,
				"visible_bounds_offset": [0, 0.75, 0],
				"texture_width": 40,
				"texture_height": 38
			},
			"bones": [
				{
					"name": "root",
					"pivot": [0, 0, 0],
					"rotation": [0, 0, 0]
				},
				{
					"name": "body",
					"parent": "root",
					"pivot": [0, 0, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [0.0, 0.0],
							"size": [4, 4, 16],
							"origin": [-2, 0, -8],
							"pivot": [0, 0, 0],
							"rotation": [0, 0, 0]
						}
					]
				},
				{
					"name": "eye_l",
					"parent": "body",
					"pivot": [1, 4, -7],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [12.0, 12.0],
							"size": [1, 3, 1],
							"origin": [1, 4, -8],
							"pivot": [0, 0, 0],
							"rotation": [0, 0, 0]
						}
					]
				},
				{
					"name": "eye_r",
					"parent": "body",
					"pivot": [-1, 4, -7],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [12.0, 12.0],
							"size": [1, 3, 1],
							"origin": [-2, 4, -8],
							"pivot": [0, 0, 0],
							"rotation": [0, 0, 0]
						}
					]
				},
				{
					"name": "shell",
					"parent": "root",
					"pivot": [0, 0, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [0.0, 20.0],
							"size": [6, 9, 9],
							"origin": [-3, 2, -2],
							"pivot": [0, 0, 0],
							"rotation": [0, 0, 0]
						}
					]
				}
			]
		}
	]
}
```

### visuals/snail.entity.png
```{image} ./namespacing_addons/snail.entity.png
:alt: snail.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```

### snail.behavior.json
```json
{
	"format_version": "1.20.30",
	"minecraft:entity": {
		"description": {
			"identifier": "nusiq:snail",
			"is_spawnable": true,
			"is_summonable": true
		},
		"components": {
			"minecraft:collision_box": {
				"height": 0.5,
				"width": 0.5
			},
			"minecraft:health": {
				"value": 1,
				"max": 1
			},
			"minecraft:breathable": {
				"total_supply": 15,
				"breathes_air": true,
				"breathes_water": true,
				"breathes_lava": true,
				"breathes_solids": true,
				"generates_bubbles": false
			},
			"minecraft:movement": {
				"max": 0.08
			},
			"minecraft:movement.basic": {},
			"minecraft:variable_max_auto_step": {
				"base_value": 1.0625,
				"jump_prevented_value": 0.5625
			},
			"minecraft:navigation.walk": {
				"avoid_damage_blocks": true,
				"avoid_sun": true,
				"can_breach": false,
				"can_break_doors": false,
				"can_float": false,
				"can_sink": true
			},
			"minecraft:behavior.random_stroll": {
				"priority": 2,
				"interval": 30,
				"xz_dist": 10,
				"y_dist": 7
			},
			"minecraft:behavior.random_look_around": {
				"priority": 3
			},
			"minecraft:physics": {}
		}
	}
}

```

### snail.entity.json
```json
{
	"format_version": "1.20.30",
	"minecraft:client_entity": {
		"description": {
			"identifier": "nusiq:snail",
			"materials": {
				"default": "entity_alphatest"
			},
			"textures": {
				"default": "textures/nusiq/entity/snail"
			},
			"geometry": {
				"default": "geometry.nusiq.snail"
			},
			"animations": {
				"walk": "animation.nusiq.snail.walk"
			},
			"scripts": {
				"animate": ["walk"]
			},
			"render_controllers": ["controller.render.default"],
			"spawn_egg": {
				"base_color": "#288483",
				"overlay_color": "#2B7135"
			}
		}
	}
}
```

## Other Files Required By The System

As mentioned before there is nothing special about the system itself. The magic happens in the configuration.

You can find the full files in [the project](https://github.com/Nusiq/System-Template-Examples/tree/3.12.0/project-3-namespacing/). This is a pretty standard configuration for System Tmplate. You can read more about it on the {ref}`Namespacing<namespacing>` page.

### config.json

Part of the `config.json` file with the `system_template` run configuration:
```json
{
	"filter": "system_template",
	"settings": {
		"log_path": "system_template_log.json",
		"namespace": {
			"hook": "nusiq",
			"keep_hook": true,
			"target": "xyz"
		},
		"scope_path": "scope.json"
	}
},
```

The `namespace` property defines the rules that make System Template replace strings like `nusiq_` with `nusiq_xyz_` etc. The exact rules are explained in the {ref}`Namespacing<namespacing>` section.

### auto_map.json

Some of the mappings in the `auto_map.json` file:
```json
".mcfunction": "`f\"BP/functions/{__namespace__['hook']+'/'+__namespace__['target'] if __namespace__['keep_hook'] else __namespace__['target']}/\"`",
".bp_item.json": "BP/items",
".item.json": "BP/items",
".loot.json": "`f\"BP/loot_tables/{__namespace__['hook']+'/'+__namespace__['target'] if __namespace__['keep_hook'] else __namespace__['target']}\"`",
".recipe.json": "BP/recipes",
```

As you can see some of the mappings use the `__namespace__` variable. This variable holds the same content as the `namespace` property in the `config.json` file. The output paths for auto mapping is set up in a way that matches the namespacing rules. More about it in the {ref}`Namespacing<namespacing>` section.
