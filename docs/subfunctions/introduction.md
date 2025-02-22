(subfunctions)=
# Subfunctions Introduction

```{warning}
This documentation applies to **Subfunctions 2.1.0**
```

Subfunctions provide extended syntax for `.mcfunction` files. They let you define functions within functions, and use a special syntax to call them. Additionally, they let you use code generation features, like loops, binary function trees (based on scoreboards), variables and conditionals. Subfunctions also let you generate code in the `.lang` files.

Subfunctions can be used as an {ref}`independend Regolith filter<subfunctions-filter>`, or as a module of {ref}`System Template<system-template-filter>`.

If you are using Subfunctions with System Template, the working directory during the evaluation is set to the root of the system. This way you can easily access the files of the system by using relative paths.
