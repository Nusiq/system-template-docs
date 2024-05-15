(debugging)=
# Debugging

If you specify the `log_path` property in the `config.json` file, `system_template` can create a log file for you after its successful runs. The log file contains information about the generated files, which can be helpful during debugging, when multiple files are merged into one, and you want to understand which systems participated in the process. The code below provides an example log with additional comments for clarification:

```json
{
    // The path of the file that has been created
    "BP/entities/pig_green.behavior.json": {
        // The list of the files that were used to create/modify the target file
        "sources": [
            {
                // The name of a file that was used to create/modify the target file
                "path": "data/system_template/colorful_pigs/1_pig_.behavior.py",

                // The status describes how the file was used to create/modify the
                // target file. It can be one of the following:
                // - "Merged." - The file was merged with an already existing file
                //   using the "merge" option (for JSON files).
                // - "Appended content to the end." - The content of was added to the
                //   end of the already existing file.
                // - "Inserted content at the start." - The content of was added to the
                //   start of the already existing file.
                // - "Skipped." - The file wasn't used. It was skipped because the
                //   target file already existed and the mapping used the "skip" policy
                //   for handling the conflicts.
                // - "Overwritten." - The file ovwerwrote the already existing file.
                // - "Created." - The target didn't exist so the new file was created.
                // - "Skipped (export once enabled)." - The file wasn't used because
                // of the "export_once" option being enabled, and the file was already
                // exported once.
                "status": "created"
            },
            {
                // Other source...
            }
        ]
    },
  // Other files...
}

```

If you're adding the `log_path` to your `config.json` file, remember to include it in the `.gitignore` file as well. Otherwise, you'll end up committing the log file to your repository.
