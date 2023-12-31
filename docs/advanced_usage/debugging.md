(debugging)=
# Debugging

If you specify the `log_path` property in the `config.json` file, `system_template` can create a log file for you after its successful runs. The log file contains information about the generated files, which can be helpful during debugging, when multiple files are merged into one, and you want to understand which systems participated in the process. The code below provides an example log with additional comments for clarification:

```json
[
  {
    // The path of the file that has been created
    "target": "BP/entities/pig_green.behavior.json",

    // The list of the files that were used to create/modify the target file
    "sources": [
      {
        // The name of a file that was used to create/modify the target file
        "path": "data/system_template/colorful_pigs/1_pig_.behavior.py",

         //  Can be: 'merged', 'skipped', 'overwritten', 'created'
         // - merged - the file was merged with the target file
         // - skipped - the file was skipped (not used, target already exists)
         // - overwritten - the file completely overwrote the target file
         // - created - the file was used to create the target file (target didn't exist)
        "status": "created"
      },
      {
        // Other source...
      }
    ]
  },
  // Other files...
]
```

If you're adding the `log_path` to your `config.json` file, remember to include it in the `.gitignore` file as well. Otherwise, you'll end up committing the log file to your repository.
