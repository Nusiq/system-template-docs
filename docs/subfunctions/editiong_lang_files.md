# Editiong Lang Files

## Editing `.lang` files
Subfunctions ignore the syntax of mcfunction files which means that you could use it for editing any other type of files. It doesn't make much sense to edit most of the files in the packs, but in some cases it can be useful to modify the `.lang` files.

If you're using subfunctions as a standalone filter. By default the `edit_lang_files` option is disabled but you can enable it and you'll be able to use subfunctions syntax in language files.

If you're using subfunctions with system template, the `subfunctions` option is for the `.lang` files is also disabled by default. You can enable it in the `_map.py` file in the mapping of your `.lang` file.

Editing `.lang` files has some restrictions. The features that are designed to
work specificly with `.mcfunction` files are disabled:
- function
- definefunction
- funcitontree
- UNPACK:HERE
- UNPACK:SUBFUNCTION
- subfunction comments (`##`)