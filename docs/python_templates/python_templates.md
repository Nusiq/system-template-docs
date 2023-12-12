(python-templates)=
# Python Templates

Python templates are Python files with syntax similar to JSON. The {ref}`The _map.py File<the-mappy-file>` file can be considered a Python template (the concept is the same). In System Template, you can use Python files to generate the content of any JSON file. You just need to map the Python file (with the `.py` extension) to the JSON file (with `.json` or `.material` extension). The Python file will be evaluated and the result will be saved as the JSON file. The Python Templates must have only one expression that evaluates to a dictionary or a list which is JSON serializable. The result of the evaluation will be saved as the JSON file.

The Python Templates are based on the Pytemplate Regolith filter, however, Pytemplate has its own features that are not available in System Template. You can read more about Pytemplate on its [GitHub page](https://github.com/Nusiq/regolith-filters/tree/master/pytemplate).
