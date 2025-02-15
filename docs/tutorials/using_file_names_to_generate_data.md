# Using File Names To Generate Data

```{note}
You can find the complete source code for this tutorial in the [Ore Golem](https://github.com/Nusiq/System-Template-Examples/tree/3.12.0/project-1-basics/filters_data/system_template/ore_golem) system of the System Template Examples respository.
```

```{image} ./using_file_names_to_generate_data/result.png
:alt: ore_golem.png
:class: bg-primary
:width: 100%
:align: center
```

This tutorial outlines how to leverage the JSON Template module of System Template to dynamically generate entity variants based on provided texture files. This technique proves especially useful when dealing with entities featuring numerous visual variants, allowing for easy testing without the need for manual modifications across multiple files.

This tutorial builds upon the {ref}`Simple Entity<simple-entity>` tutorial but introduces more advanced concepts. The system described here creates an Ore Golem entity with four variants: Diamond, Emerald, Lapis, and Redstone. The system is set up in a way, that makes it easy to add more variants by simply adding texture files.

## System File Structure

```text
📁 ore_golem
    📁 visuals
        🖼️ ore_golem_diamond.entity.png
        🖼️ ore_golem_emerald.entity.png
        🖼️ ore_golem_lapis.entity.png
        🖼️ ore_golem_redstone.entity.png
        🗒️ ore_golem.animation.json
        🗒️ ore_golem.geo.json
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ ore_golem.behavior.json
    🗒️ ore_golem.entity.json
    🗒️ ore_golem.rc.json
    🗒️ ore_golem.rp_ac.json
```


## System Configuration

### _scope.json
```json
{}
```
The system doesn't utilize any scope variables, resulting in an empty JSON object for the scope file.

### _map.py
```py
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
        "source": "visuals/ore_golem_*.entity.png",
        "target": AUTO_FLAT
    },
    # Entity definition
    {
        "source": "ore_golem.behavior.json",
        "target": AUTO_FLAT,
        "json_template": True,
        "scope": {
            "variants": [
                p.stem.removeprefix('ore_golem_').removesuffix('.entity')
                for p in Path("visuals").glob("ore_golem_*.entity.png")
            ]
        }
    },
    {
        "source": "ore_golem.entity.json",
        "target": AUTO_FLAT,
        "json_template": True,
        "scope": {
            "variants": [
                p.stem.removeprefix('ore_golem_').removesuffix('.entity')
                for p in Path("visuals").glob("ore_golem_*.entity.png")
            ]
        }
    },
    {
        "source": "ore_golem.rp_ac.json",
        "target": AUTO_FLAT
    },
    {
        "source": "ore_golem.rc.json",
        "target": AUTO_FLAT,
        "json_template": True,
        "scope": {
            "variants": [
                p.stem.removeprefix('ore_golem_').removesuffix('.entity')
                for p in Path("visuals").glob("ore_golem_*.entity.png")
            ]
        }
    },
]
```

All texture files with the `ore_golem_` prefix and `.entity.png` suffix are automatically exported using a wildcard in the source path. The export path is determined automatically based on the file name through the {ref}`Auto Mapping<auto-mapping>` feature.

The behavior file, client entity file, and render controller file use JSON Template to generate some of their data. Each of them adds the `variants` variable to their scopes, forming a list of names based on the names of the texture files. This variable is then used to link the textures to the entity variants.

## System Files
### ore_golem_diamond.entity.png
```{image} ./using_file_names_to_generate_data/ore_golem_diamond.entity.png
:alt: stone_golem.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
### ore_golem_emerald.entity.png
```{image} ./using_file_names_to_generate_data/ore_golem_emerald.entity.png
:alt: stone_golem.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
### ore_golem_lapis.entity.png
```{image} ./using_file_names_to_generate_data/ore_golem_lapis.entity.png
:alt: stone_golem.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
### ore_golem_redstone.entity.png
```{image} ./using_file_names_to_generate_data/ore_golem_redstone.entity.png
:alt: stone_golem.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```
### ore_golem.animation.json
```json
{
	"format_version": "1.8.0",
	"animations": {
		"animation.ore_golem.walk": {
			"loop": true,
			"anim_time_update": "q.modified_distance_moved*0.2",
			"bones": {
				"leg_l": {
					"rotation": ["math.sin(q.anim_time*360+180)*30", 0, 0]
				},
				"leg_r": {
					"rotation": ["math.sin(q.anim_time*360)*30", 0, 0]
				},
				"arm_l": {
					"rotation": ["math.sin(q.anim_time*360)*30", 0, 0]
				},
				"arm_r": {
					"rotation": ["math.sin(q.anim_time*360+180)*30", 0, 0]
				}
			}
		},
		"animation.ore_golem.look_at_target": {
			"loop": true,
			"bones": {
				"head": {
					"rotation": ["q.target_x_rotation", "q.target_y_rotation", 0.0]
				}
			}
		}
	}
}
```
### ore_golem.geo.json
```json
{
	"format_version": "1.16.0",
	"minecraft:geometry": [
		{
			"description": {
				"identifier": "geometry.ore_golem",
				"visible_bounds_width": 2.0,
				"visible_bounds_height": 2.0,
				"visible_bounds_offset": [0, 1, 0],
				"texture_width": 64,
				"texture_height": 64
			},
			"bones": [
				{
					"name": "root",
					"pivot": [0, 0, 0],
					"rotation": [0, 0, 0]
				},
				{
					"name": "leg_l",
					"parent": "root",
					"pivot": [1.95, 3.107, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [26.0, 26.0],
							"size": [3, 3, 3],
							"origin": [0, 0, -1.5],
							"pivot": [1.5, 3, 0],
							"rotation": [0, 0, 0],
							"mirror": true
						}
					]
				},
				{
					"name": "leg_r",
					"parent": "root",
					"pivot": [-1.95, 3.107, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [29.0, 14.0],
							"size": [3, 3, 3],
							"origin": [-3, 0, -1.5],
							"pivot": [-1.5, 3, 0],
							"rotation": [0, 0, 0]
						}
					]
				},
				{
					"name": "torso",
					"parent": "root",
					"pivot": [0, 2.975, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [0.0, 16.0],
							"size": [9, 6, 4],
							"origin": [-4.5, 5, -2.1],
							"pivot": [0, 5, -0.1],
							"rotation": [0, 0, 0]
						},
						{
							"uv": [26.0, 20.0],
							"size": [6, 3, 3],
							"origin": [-3, 3.2, -1.5],
							"pivot": [0, 3, 0],
							"rotation": [0, 0, 0],
							"inflate": 0.2
						}
					]
				},
				{
					"name": "head",
					"parent": "torso",
					"pivot": [0, 10.975, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [0.0, 0.0],
							"size": [8, 8, 8],
							"origin": [-4, 11.192, -4],
							"pivot": [0, 11.192, 0],
							"rotation": [0, 0, 0]
						}
					]
				},
				{
					"name": "arm_l",
					"parent": "torso",
					"pivot": [6, 9.975, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [14.0, 26.0],
							"size": [3, 6.999, 3],
							"origin": [4.5, 4.001, -1.5],
							"pivot": [6, 11, 0],
							"rotation": [0, 0, 0],
							"mirror": true
						}
					]
				},
				{
					"name": "arm_r",
					"parent": "torso",
					"pivot": [-6, 9.975, 0],
					"rotation": [0, 0, 0],
					"cubes": [
						{
							"uv": [0.0, 26.0],
							"size": [3, 6.999, 3],
							"origin": [-7.5, 4.001, -1.5],
							"pivot": [-6, 11, 0],
							"rotation": [0, 0, 0]
						}
					]
				}
			]
		}
	]
}
```
### ore_golem.behavior.json
```json
{
	"format_version": "1.20.30",
	"minecraft:entity": {
		"description": {
			"identifier": "nusiq:ore_golem",
			"is_spawnable": true,
			"is_summonable": true
		},
		"component_groups": {
			"`[K(v, variant=v, i=i) for i, v in enumerate(variants)]`": {
				"minecraft:variant": {
					"value": "`i`"
				},
				"minecraft:healable": {
					"force_use": false,
					"items": [
						{
							"item": "`f'minecraft:{variant}'`"
						}
					]
				},
				"minecraft:behavior.tempt": {
					"priority": 1,
					"can_get_scared": false,
					"can_tempt_vertically": true,
					"items": ["`variant`"],
					"within_radius": 0
				}
			}
		},
		"components": {
			"minecraft:collision_box": {
				"height": 1.2,
				"width": 0.6
			},
			"minecraft:health": {
				"value": 8,
				"max": 8
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
				"max": 0.2
			},
			"minecraft:movement.basic": {},
			"minecraft:jump.static": {
				"jump_power": 0.42
			},
			"minecraft:navigation.walk": {
				"avoid_damage_blocks": true,
				"avoid_sun": true,
				"can_breach": false,
				"can_break_doors": false,
				"can_float": false,
				"can_sink": true
			},
			"minecraft:follow_range": {
				"value": 16,
				"max": 24
			},
			"minecraft:behavior.panic": {
				"priority": 0
			},
			"minecraft:behavior.random_stroll": {
				"priority": 2,
				"interval": 120,
				"xz_dist": 10,
				"y_dist": 7
			},
			"minecraft:behavior.random_look_around": {
				"priority": 3
			},
			"minecraft:physics": {}
		},
		"events": {
			"minecraft:entity_spawned": {
				"randomize": [
					{
						"__unpack__": "`[{'variant': v} for v in variants]`",
						"weight": 1,
						"add": {
							"component_groups": ["`variant`"]
						}
					}
				]
			}
		}
	}
}
```
The behavior file generates component groups and the `minecraft:entity_spawned` event based on the `variants` variable. The syntax used for this process is described in the JSON Template {ref}`Syntax<json-template-syntax>` documentation page.

### ore_golem.entity.json
```json
{
	"format_version": "1.20.30",
	"minecraft:client_entity": {
		"description": {
			"identifier": "nusiq:ore_golem",
			"materials": {
				"default": "entity_alphatest"
			},
			"textures": {
				"`[K(v, variant=v) for v in variants]`": "`f'textures/entity/ore_golem_{variant}'`"
			},
			"geometry": {
				"default": "geometry.ore_golem"
			},
			"animations": {
				"walk_controller": "controller.animation.ore_golem.walk",
				"walk": "animation.ore_golem.walk",
				"look_at_target": "animation.ore_golem.look_at_target"
			},
			"scripts": {
				"animate": ["walk_controller", "look_at_target"]
			},
			"render_controllers": ["controller.render.ore_golem"],
			"spawn_egg": {
				"base_color": "#288483",
				"overlay_color": "#2B7135"
			}
		}
	}
}
```
The texture list of the client entity file is generated based on the `variants` variable using JSON Template.

### ore_golem.rc.json
```json
{
	"format_version": "1.20.41",
	"render_controllers": {
		"controller.render.ore_golem": {
			"geometry": "geometry.default",
			"arrays": {
				"textures": {
					"array.skin": "`[f'texture.{variant}' for variant in variants]`"
				}
			},
			"materials": [
				{
					"*": "material.default"
				}
			],
			"textures": ["array.skin[q.variant]"]
		}
	}
}
```
The render controller uses a texture array generated using the `variants` variable and JSON Template.

### ore_golem.rp_ac.json
```json
{
	"format_version": "1.20.30",
	"animation_controllers": {
		"controller.animation.ore_golem.walk": {
			"states": {
				"default": {
					"transitions": [
						{
							"walk": "q.modified_move_speed > 0.1"
						}
					]
				},
				"walk": {
					"animations": ["walk"],
					"transitions": [
						{
							"default": "q.modified_move_speed < 0.05"
						}
					]
				}
			}
		}
	}
}
```