(simple-entity)=
# Simple Entity

```{note}
You can find the complete source code for this tutorial in the [Stone Golem](https://github.com/Nusiq/System-Template-Examples/tree/3.6.0/regolith/filters_data/system_template/stone_golem) system of the System Template Examples respository.
```

```{image} ./simple_entity/result.png
:alt: stone_golem.png
:class: bg-primary
:width: 100%
:align: center
```

This tutorial describes how to create a simple entity using System Template. The setup assumes you're utilizing the default {ref}`Auto Map<custom-auto-mapping>` settings.

## System File Structure

```text
📁 stone_golem
    📁 visuals
        🗒️ stone_golem.animation.json
        🖼️ stone_golem.entity.png
        🗒️ stone_golem.geo.json
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ stone_golem.behavior.json
    🗒️ stone_golem.entity.json
    🗒️ stone_golem.rp_ac.json
```

## System Configuration

The system's configuration is defined in the `_scope.json` and `_map.py` files.

### _scope.json
```json
{}
```
For this simple example, only the `_map.py` file is utilized. Although the `_scope.json` file is still required, it contains an empty object since no custom variables are defined.


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
        "source": "stone_golem.behavior.json",
        "target": AUTO_FLAT
    },
    {
        "source": "stone_golem.entity.json",
        "target": AUTO_FLAT
    },
    {
        "source": "stone_golem.rp_ac.json",
        "target": AUTO_FLAT
    }
]
```

In the `_map.py` file, the mapping between source and target files is defined. All target locations are determined by file names, using the [auto mapping](auto-mapping) feature.

The files responsible for the entity's appearance reside in the `visuals` folder. This practice of grouping models, textures, and animations in a folder improves system manageability, especially when using external modeling tools like Blockbench or Mcblend. The `AUTO_FLAT` mapping omits the `visuals` folder from the target path and alters the texture file extension from `.entity.png` to just `.png` (assuming that you're using the default {ref}`Auto Map<custom-auto-mapping>` settings).

The entity definition files contain everything necessary for the entity to function in the game. Although the system uses `AUTO_FLAT` mapping, the regular `AUTO` mapping would function similarly since the files are located in the root folder.

## System Files

In this example, no variables or code generation features are utilized. The system's files remain identical to what they would be if you were working directly with a resource pack and behavior pack. The primary advantage of using System Template for such systems lies in having all files conveniently grouped and easily shareable with others.

### stone_golem.animation.json
```json
{
	"format_version": "1.8.0",
	"animations": {
		"animation.stone_golem.walk": {
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
		"animation.stone_golem.look_at_target": {
			"loop": true,
			"bones": {
				"head": {
					"rotation": [
						"q.target_x_rotation",
						"q.target_y_rotation",
						0.0
					]
				}
			}
		}
	}
}
```

### stone_golem.entity.png
```{image} ./simple_entity/stone_golem.entity.png
:alt: stone_golem.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```

### stone_golem.geo.json
```json
{
	"format_version": "1.16.0",
	"minecraft:geometry": [
		{
			"description": {
				"identifier": "geometry.stone_golem",
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

### stone_golem.behavior.json
```json
{
	"format_version": "1.20.30",
	"minecraft:entity": {
		"description": {
			"identifier": "nusiq:stone_golem",
			"is_spawnable": true,
			"is_summonable": true
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
			"minecraft:healable": {
				"force_use": false,
				"items": [
					{
						"item": "minecraft:diamond"
					}
				]
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
			"minecraft:behavior.tempt": {
				"priority": 1,
				"can_get_scared": false,
				"can_tempt_vertically": true,
				"items": ["diamond"],
				"within_radius": 0
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
		}
	}
}
```

### stone_golem.entity.json
```json
{
	"format_version": "1.20.30",
	"minecraft:client_entity": {
		"description": {
			"identifier": "nusiq:stone_golem",
			"materials": {
				"default": "entity_alphatest"
			},
			"textures": {
				"default": "textures/entity/stone_golem"
			},
			"geometry": {
				"default": "geometry.stone_golem"
			},
			"animations": {
				"walk_controller": "controller.animation.stone_golem.walk",
				"walk": "animation.stone_golem.walk",
				"look_at_target": "animation.stone_golem.look_at_target"
			},
			"scripts": {
				"animate": ["walk_controller", "look_at_target"]
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

### stone_golem.rp_ac.json
```json
{
	"format_version": "1.20.30",
	"animation_controllers": {
		"controller.animation.stone_golem.walk": {
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
