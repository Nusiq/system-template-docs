(grouped-systems)=
# Grouped Systems
The Grouped Systems feature allows you to consolidate multiple systems, creating a shared scope for them. To establish a group, create a directory and add a `_group_scope.json` file inside it. This feature is useful for systems that share common configurations and offers an organized approach to code structure.

**Example project structure:**
```
📁 system_template
  🗒️ auto_map.json
  📁 [_shared]: ...
  📁 [group]
    🗒️ _group_scope.json
    📁 [_shared]: ...
    📁 [system1]: ...
    📁 [system2]: ...
  📁 [system3]
```

In this example, `system1` and `system2` belong to the same group, while `system3` is independent and does not belong to any group.

Groups influence a system's behavior in the following ways:
1. Systems in the same group can access variables from the `_group_scope.json` file.
2. Systems in the same group can access variables from the {ref}`"_shared" directory<shared-files>` of the group (they can also access variables from the global `_shared` directory).
3. When using {ref}`AUTO mapping<auto-mapping>` with one of the `SUBFOLDER` mapping modes, the system's name begins with the group's directory name (excluding the group name itself). In the example above, the names of systems `system1` and `system2` are `system1` and `system2` respectively. If you were to remove the `_group_scope.json` file, the names would change to `group/system1` and `group/system2` respectively. The system's name is relevant for mapping because some of the mapping modes use it in the target path.
