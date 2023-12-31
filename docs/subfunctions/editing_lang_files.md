# Editing Lang Files

## Editing `.lang` files
Subfunctions disregard the syntax of mcfunction files, allowing you to leverage them for editing various file types. While it might not be practical for most files in the pack, there are cases where modifying `.lang` files could prove beneficial.

When using subfunctions as a standalone filter, the `edit_lang_files` option is initially disabled. However, you can enable it to utilize subfunctions syntax within language files.

For users employing subfunctions with the System Template, the `subfunctions` option for `.lang` files is also disabled by default. You can enable it in the `_map.py` file within the mapping for your `.lang` file.

Editing `.lang` files does come with some limitations. Certain features tailored for `.mcfunction` files are disabled in this context:
- {ref}`function<subfunctions-function>`, {ref}`definefunction<subfunctions-definefunction>` and {ref}`schedule<subfunctions-schedule>`
- {ref}`functiontree<subfunctions-functiontree>`
- {ref}`unpacking<subfunctions-unpacking>` (UNPACK:...)
- {ref}`Subfunction comments<subfunctions-comments>` (`##`)

This capability provides a convenient way to apply subfunctions' concise syntax for tasks specific to language files, enhancing your workflow in certain scenarios.