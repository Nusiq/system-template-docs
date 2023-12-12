(plugins)=
# Plugins

The plugins are Python files stored in `_plugins` foler. There are two types of plugins:
- global plugins - stored in `<filters-data>/system_template/_plugins` folder. These are shared between all systems.
- local plugins - stored in `<filters-data>/system_tempalte/<system>/_plugins` folder. These are specific to the system.

Plugins are executed using `exec` function, and then their local scope is merged with the scope of the system. If the local plugin defines functions/variables with the same name as the global plugin, the local one will be used.

It is recommended to use the plugins only for defining functions. The data should be defined in the _scope.json files and in the global scope file of system_template. Plugins can run arbitrary code, but it is not recommended to use them for anything else than defining functions.

When you do a fresh install of the system_template filter in your project, the default global plugins folder will contain one file - `_default.py`. This file defines a plugin that imports following objects and modules from Python's standard library:
- `math`
- `random`
- `uuid`
- `pathlib.Path`

```{note}
Plugins are one the main benefits of using System Template instead of its modules directly. If you're using Subfunctions or JSON Template alone, you're limited to the functions provided by each module. If you're using System Template, you can add your own functions to the system by defining them in the plugins.
```
