(grouped-systems)=
# Grouped Systems
Grouped systems feature allows you to put multiple systems together and create a shared scope for them. You can create a group by creating a directory and adding a `_group_scope.json` file inside it. This feature is useful for creating systems that share common configuration but it's also a nice way to organize your code. 

_Example project structure:_
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
In this example system1 and system2 belong to the same group. The system3 is not part of any group.

Groups change system's behavior in the following ways:
1. Systems in the same group can access variables from the `_gruop_scope.json` file.
2. Systems in the same group can access variables from the {ref}`"_shared" directory<shared-files>` directory of the group (they can also access variables from the global `_shared` directory).
3. Whe using {ref}`AUTO mapping<auto-mapping>` with one of the `SUBFOLDER` mapping modes, the name of the system starts from the group's directory name (with the name of the group excluded). In the example above, the name of the systems `system1` and `system2` are `system1` and `system2` respectively, but if you would remove the `_group_scope.json` file, the names would change to `group/system1` and `group/system2` respectively. The name of the system is relevent for mapping because some of the mapping modes use it in the target path.
