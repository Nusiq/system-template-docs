# Namespacing Addons

```{image} ./namespacing_addons/result.png
:alt: result.png
:class: bg-primary
:width: 100%
:align: center
```

When your addon needs to coexist with others, there's a risk of name collisions, especially when you lack control over the other addons. System Template can aid in avoiding this issue if you follow some simple rules.

In this tutorial, you'll learn:
- How to create a system that automatically adds a namespace based on the addon's configuration (using the {ref}`global scope <global-scope>`).
- How to use the {ref}`Dynamic Auto Mapping <dynamic-auto-mapping>` to automatically add a namespace to all files in the system.
- How to leverage the {ref}`Plugins <plugins>` feature to modify a file, adding a namespace to it before export, without relying on {ref}`Python Templates <python-templates>` or {ref}`JSON Template <json-template>`.

```{warning}
In this tutorial, we use custom file extensions prefixed with `.namespaced` (e.g., `.namespaced.entity.png`). This is implemented this way because the system presented here is part of the [System-Template-Examples](https://github.com/Nusiq/System-Template-Examples/tree/3.4.0) repository, which contains non-namespaced systems. In a normal scenario, you would likely namespace everything, and the `.namespaced` prefix wouldn't be needed.
```

## System File Structure

```text
📁 snail
    📁 visuals
        🗒️ snail.animation.json
        🗒️ snail.geo.json
        🖼️ snail.namespaced.entity.png
    🗒️ _map.py
    🗒️ _scope.json
    🗒️ snail.behavior.json
    🗒️ snail.entity.json
```

Only `snail.namespaced.entity.png` uses the custom `.namespaced` extension. This is because, in normal circumstances, adding a namespace to the file path is not necessary. Most files in Minecraft are identified by an identifier defined in their content rather than their file path.

Here's a list of files identified by their paths:
- Mcfunction files (`.mcfunction`)
- Loot tables (`.loot.json`)
- Structures (`.mcstructure`)
- Trade tables (`.trade.json`)
- Sound Files (`.ogg`, `.wav`, `.mp4`, `.fsb`)
- Texture files (`.png`, `.tga`)

## System Configuration

### _scope.json

```json
{}
```

The system doesn't use any custom variables, so the scope is empty.

### _map.py

```python
add_geo_namespaces(
    "**/*.geo.json", namespace
) + add_animation_namespaces(
    "**/*.animation.json", namespace
) + [
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
        "source": "visuals/*.namespaced.entity.png",
        "target": AUTO_FLAT
    },
    # Entity definition
    {
        "source": "snail.behavior.json",
        "target": AUTO_FLAT,
        "json_template": True
    },
    {
        "source": "snail.entity.json",
        "target": AUTO_FLAT,
        "json_template": True
    }
]
```

Note that the `_map.py` file starts by calling two functions - `add_geo_namespaces` and `add_animation_namespaces`. They will be explained {ref}`later <namespacing-tutorial-namespace-plugin>`, but for now, you should know that they return an empty list, not affecting the content of the `_map.py` file in any way. In Python, the `+` operator concatenates lists. When concatenating an empty list to another list, you get the same list.

## System Files

### visuals/snail.animation.json

```json
{
	"format_version": "1.8.0",
	"animations": {
		"animation.snail.walk": {
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
The animation file doesn't use any templating and doesn't have a namespace added to its identifier (yet). The namespace is added by a plugin (explained later).

### visuals/snail.geo.json
```json
{
	"format_version": "1.16.0",
	"minecraft:geometry": [
		{
			"description": {
				"identifier": "geometry.snail",
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

The model file doesn't use any templating and doesn't have a namespace added to its identifier (yet). The namespace is added by a plugin (explained later).

### visuals/snail.namespaced.entity.png
```{image} ./namespacing_addons/snail.namespaced.entity.png
:alt: snail.namespaced.entity.png
:class: bg-primary
:width: 100%
:align: center
:class: pixelart
```

The texture uses the `.namespaced.entity.png` extension. The mapping is defined in the `auto_map.json` file shown later in this tutorial.

### snail.behavior.json
```json
{
	"format_version": "1.20.30",
	"minecraft:entity": {
		"description": {
			"identifier": "`f'{namespace}:snail'`",
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
The behavior uses {ref}`JSON template <json-template>` to insert the namespace into the identifier.

### snail.entity.json
```json
{
	"format_version": "1.20.30",
	"minecraft:client_entity": {
		"description": {
			"identifier": "`f'{namespace}:snail'`",
			"materials": {
				"default": "entity_alphatest"
			},
			"textures": {
				"default": "`f'textures/entity/{namespace}/snail'`"
			},
			"geometry": {
				"default": "`f'geometry.{namespace}_snail'`"
			},
			"animations": {
				"walk": "`f'animation.{namespace}.snail.walk'`"
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

All components referenced in the file use the `namespace` variable. This variable should be defined in the global scope. The `.entity.json` files reference many other files, that's why the variable is used multiple times.

## Other Files Required By The System

### auto_map.json
```json
{
	".behavior.json": "BP/entities",
	".animation.json": "RP/animations",
	".entity.json": "RP/entity",
	".geo.json": "RP/models/entity",
	".rc.json": "RP/render_controllers",
	// .namespace.entity.png must be before .entity.png because otherwise, the
	// latter will match first. Order matters!
	".namespaced.entity.png": {
		"target": "`f'RP/textures/entity/{namespace}'`",
		"replace_extension": ".png"
	},
	".entity.png": {
		"target": "RP/textures/entity",
		"replace_extension": ".png"
	}
    // Other rules for mapping files...
}
```

```{note}
The content of the file above is incomplete. For the sake of readability, only the rules relevant to this tutorial are presented here.
```

The {ref}`auto_map.json <custom-auto-mapping>` file must be modified to include the `.namespaced.entity.png` extension. In normal circumstances, you would namespace every file that uses its path as an identifier and replace regular extensions (without the `.namespaced` prefix) instead of adding new ones. We're not doing that here because the files from this tutorial are part of a bigger project that contains non-namespaced systems.

Note that the file uses {ref}`JSON template <json-template>` syntax to insert the `namespace` variable into the target path.

### Global scope.json

```json
{
	"namespace": "nusiq_st_demo"
}
```

The {ref}`global scope <global-scope>` file should be modified to include the `namespace` variable. Alternatively, you could add that variable to the system's `_scope.json` file, but if you want to use the same namespace in multiple systems, it's better to add it globally.


(namespacing-tutorial-namespace-plugin)=
### namespaces.py (plugin)

In the `_map.py` file, we're using two functions. Their purpose is to add a namespace to the identifiers in the `snail.geo.json` and `snail.animation.json` files. We could use templating features of System Template, like we did in other files, but models and animations are often accessed by external programs for editing. Having invalid identifiers could cause problems. That's why we keep regular identifiers in the files, but System Template modifies them so that they're exported with an additional namespace.

The `namespaces.py` file should be added to the global {ref}`_plugins <plugins>` directory. Adding it to the system's `_plugins` directory would work too, but it's not recommended because in normal scenarios you would probably want to use this plugin in multiple systems.

Making a system rely on global plugins makes it slightly less portable, but once you start using plugins, you'll probably notice that there are some things that fit specifically into your workflow. This way, you'll likely end up developing your own version of a standard library. Adding namespaces to files is a good example of such a thing.

```python
from pathlib import Path
from better_json_tools import load_jsonc
import json
from typing import Any

def add_geo_namespaces(pattern: str, namespace: str) -> list[Any]:
    '''
    Walks model files matching the given pattern and adds a namespace prefix
    to the geometry identifiers.

    Example: geometry.my_entity -> geometry.my_namespace_my_entity
    '''
    for file in Path(".").glob(pattern):
        file_walker = load_jsonc(file)
        for desc in file_walker / "minecraft:geometry" // int / "description":
            identifier: str = desc.data["identifier"]  # type: ignore
            # Geometry identifiers are like "geometry.[name]", we're changing
            # that to "geometry.[namespace]_[name]".
            desc.data["identifier"]  = (
                "geometry." + namespace + "_" +
                identifier.removeprefix("geometry."))
        with file.open('w', encoding='utf8') as f:
            json.dump(file_walker.data, f, indent='\t')
    return []

def add_animation_namespaces(pattern: str, namespace: str) -> list[Any]:
    '''
    Walks animation files matching the given pattern and adds a namespace
    prefix to the animation identifiers.

    Example: animation.my_animation -> animation.my_namespace.my_animation
    '''
    for file in Path(".").glob(pattern):
        anim_file_data: dict[str, Any] = load_jsonc(file).data  # type: ignore
        for k in list(anim_file_data["animations"].keys()):
            # Animation identifiers are like "animation.[name]", we're changing
            # that to "animation.[namespace].[name]".
            identifier: str = k
            anim_file_data["animations"][
                "animation." +
                namespace + "." +
                identifier.removeprefix("animation.")
            ] = anim_file_data["animations"].pop(k)
        with file.open('w', encoding='utf8') as f:
            json.dump(anim_file_data, f, indent='\t')
    return []
```

```{warning}
The functions presented in this tutorial are simplified and don't handle all possible errors. It's recommended to throw an exception with a descriptive message when something goes wrong. The functions above just assume that the provided data is valid.
```

The `add_geo_namespaces` function adds the provided namespace to models that match the given pattern, while the `add_animation_namespaces` function adds the namespace to animation files. Both functions return an empty list, which is essential because this way, you can easily add them at the beginning of the `_map.py` file using the `+` operator, and they won't affect the rest of the map file.

If you're familiar with Python, you might have noticed that the code is using the `better_json_tools` library. It's not a standard library, but it's a dependency of System Template (therefore it's always available). Better JSON tools is a library that provides a more convenient way of working with JSON files. You can read more about it in its [documentation](https://better-json-tools.readthedocs.io/en/latest/).
