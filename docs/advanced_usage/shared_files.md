(shared-files)=
# Shared Files
Sometimes you want to share some files between multiple systems. You can do it using a special `_shared` folder and `SHARED:` prefix in the file `source` property.

There are two ways to use the shared resources:
- You can put the shared resources in the `_shared` folder in the group folder.
- You can put the shared resources in the `_shared` folder in the root of the data folder of system_template (in the `system_template/_shared` folder).

The `_shared` folder in the group folder is used for sharing resources inside the group. The `_shared` folder in the root of the data folder of system_template is visible to all systems. The groupped systems also have access to the global `_shared` folder but it has lower priority than the group's `_shared` folder.

Example:

*some_system/_map.py*
```
[
    {
        // Regular resource
        "source": "my_texture.png",
        "target": AUTO
    },
    {
        // Shared resource
        "source": "SHARED:my_shared_texture.png",
        "target": AUTO
    }
]
```
This configuration means that you can put the `my_shared_texture.png` either in `some_system/my_shared_texture.png` or in `_shared/my_shared_texture.png`. If you crate both files, the local file of the system will be used.

The purpose of the shared resources is to avoid duplicating the files.

Since the use of the shared resouces means that you put resources outside of the system, this means that you can't just copy the system folder to another project. The `system_template` implements a solution to this problem. It adds 3 new commands that can be used with the `regolith apply-filter` command:
- `pack` - packs the `_shared` resources into the system folder.
- `unpack` - unpacks the system folders into the `_shared` folder. If the system is in a group, then it unpacks the system into the `_shared` folder of the group.
- `undo` - undoes the last operation.

A typical workflow of moving the system from project A to project B would be as follows (the examples of the commands and their syntax are explained in the next section):

1. In project A run the `pack` command.
2. Copy the system folder to project B.
3. In project B run the `unpack` command.
4. In project A run the `undo` command.

You don't have to unpack the system in project B if you're not going to share any resources of that system with other systems on the project. In that case, it is recommended to remove the `SHARED:` prefix from the `source` properties in the system's `_map.py` file. In general, the cleanest solution is to keep all of the shared resources in the `_shared` folder and to remove the `SHARED:` prefix if a resource is not shared.

## The syntax of the `pack`, `unpack` and `undo` commands

The `pack`, `unpack` and `undo` commands are  designed to be used with the `regolith apply-filter` command. The `apply-filter` command syntax is:
```
regolith apply-filter <filter_name> -- <filter_arguments>
```
In case of the `system_template` filter, the filter name is always `system_template`:
```
regolith apply-filter system_template -- <filter_arguments>
```

### The `pack` and `unpack` commands
The `pack` and `unpack` commands have their own flags that can be used to specify their behavior:

- `--systems` - a list of glob patterns that define which systems should be packed or unpacked. The default value is `**` which means that all systems will be packed or unpacked. You can provide multiple patterns by separating them with a space.
- `--scope` - a path to a JSON file that defines the scope that will be used when evaluating the `_map.py` files. The default value is `system_template/scope.json` which means that the default scope will be used.

Example: Unpacking `my_system` using the `scope.json` file as the scope.
```
regolith apply-filter system_template -- unpack --systems my_system --scope scope.json
```

### The `undo` command
The `undo` command has no flags. It undoes the last `pack` or `unpack`
operation.

Example: Undoing the last operation.
```
regolith apply-filter system_template -- undo
```

