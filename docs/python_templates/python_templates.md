(python-templates)=
# Python Templates

Python templates are Python files with syntax similar to JSON. The {ref}`The _map.py File<the-mappy-file>` file can be considered a Python template (the concept is the same). In the System Template, you can leverage Python files to dynamically generate the content of any JSON file. Simply map the Python file (with the `.py` extension) to the target JSON file (with `.json` or `.material` extension). The Python file undergoes evaluation, and the resulting output is then saved as the JSON file.

Python Templates must adhere to a specific structure, containing only one expression that evaluates to a dictionary or a list, both of which must be JSON serializable. The outcome of this evaluation is subsequently stored as the content of the associated JSON file.

While Python Templates in System Template are based on the Pytemplate Regolith filter, it's essential to note that Pytemplate has its distinct features not present in System Template. For more in-depth information on Pytemplate, please refer to its [GitHub page](https://github.com/Nusiq/regolith-filters/tree/master/pytemplate).
