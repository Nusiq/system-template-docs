(json-template)=
# JSON Template Introduction

```{warning}
This documentation applies to **JSON Template 2.3.0**
```

The `json_template` filter is a tool that extends the capabilities of JSON. It introduces a new syntax that allows you to easily replace JSON keys and string values surrounded with backticks (`` ` ``) with the result of evaluating a Python expression. This is achieved using Python's `eval` function. JSON Template also allows you to generate similar JSON objects using loops, which lets you get rid of the boilerplate code. The syntax of JSON template is designed in such a way that the files you create will mostly follow the JSON schemas of the files that you work with (for examples loops don't add additional nesting). This means that you can have the benefits of generating code without losing the autocompletion hints from your IDE.

If you are using JSON Template with System Template, the working directory during the evaluation of the templates is set to the root of the system. This way you can easily access the files of the system by using relative paths.