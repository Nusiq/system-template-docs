# Syntax

## Inline Function Definition

In the early versions of Subfunctions, the filter was exclusively used to define functions within functions (hence the name "Subfunctions"). There are three ways to achieve this: using the `definefunction` keyword, the `function` keyword, or the `schedule` keyword. All of them create a new `.mcfunction` file, but with distinctions: `function` instantly calls the function inside the file containing it, `schedule` schedules the function, and `definefunction` only creates the file.

(subfunctions-function)=
### `function` - Subfunctions defined and executed instantly

#### Syntax
```text
[any_code] function <[function_name]>:
    [function_body]
- any_code: usually an execute command, but since subfunctions don't parse
  mcfunction files, you can put there any string.
- function_name: the name of the new function to create that follows the
  pattern: [A-Za-z0-9_]+. The file is created inside a folder with the same
  name as the root function.
- function_body: multiline body of the function. The body ends with the line
  that doesn't have enough indentation (similar to Python).
```
#### Description
The `function` command has an extended syntax. You can use the `function` keyword combined with a subfunction name between `<` and `>` symbols. It creates a subfolder with the same name as the root function and the subfunction inside it named after the provided name.

Inside the root function, the code of the subfunction is replaced with a subfunction call. The filter accepts any code that isn't a comment and that ends with `function <subfunction name>:`. The subfunction must use symbols valid for the function name.

#### Example

*BP/functions/xxx/yyy/zzz.mcfunction*
```text
# Some code
function <aaa>:
    # The code of the subfunction
    execute @a ~ ~ ~ function <bbb>:
        # The code of the nested subfunction
# Some other code
```

The file above would resolve into:
*BP/functions/xxx/yyy/zzz.mcfunction*
```text
# Some code
function xxx/yyy/zzz/aaa
# Some other code
```

*BP/functions/xxx/yyy/zzz/aaa.mcfunction*
```text
execute @a ~ ~ ~ function xxx/yyy/zzz/aaa/bbb
```

*BP/functions/xxx/yyy/zzz/aaa/bbb.mcfunction*
```text
# The code of the nested subfunction
```

(subfunctions-definefunction)=
### `definefunction` - Definition of a subfunction without execution
#### Syntax
```text
definefunction <[function_name]>:
    [function_body]
- function_name: the name of the new function to create that follows the
  pattern:[A-Za-z0-9_]+. The file is created inside a folder with the same name
  as the root function.
- function_body: multiline body of the function. The body ends
  with the line that doesn't have enough indentation (similar to Python).

```
#### Description
Subfunction definitions can be created using the `definefunction <subfunction name>:` pattern. You can't combine `definefunction` with any other commands (like `execute`). The `definefunction` line must start with the `definefunction` keyword or with indentation.

(subfunctions-schedule)=
### `schedule` - Subfunctions defined in a schedule command
#### Syntax
```text
[any_code] schedule [any_schedule_options] <[function_name]>:
    [function_body]

- any_code: usually an execute command or chain of execute commands, but since
  subfunctions don't parse mcfunction files, you can put any string here.
- any_schedule_options: any options valid for the schedule command (anything
  that goes after the 'schedule' keyword and before the function name).
- function_name: the name of the new function to create that follows the
  pattern: [A-Za-z0-9]+. The file is created inside a folder with the same name
  as the root function.
- function_body: multiline body of the function. The body ends with the line
  that doesn't have enough indentation (similar to Python).
```
#### Description
The `schedule` command can be used to create subfunctions in the same way as the `function` command. If you put the function name between `<` and `>` symbols, it creates a subfolder with the same name as the root function and the subfunction in it named after the provided name.

Inside the root function, the code of the subfunction is replaced with a subfunction call (using the schedule command).

#### Example
*BP/functions/xxx/yyy/zzz.mcfunction*
```text
# Some code
schedule on_area_loaded 1 2 3 11 22 33 <aaa>:
    # The code of the subfunction
```

The file above would resolve into:
*BP/functions/xxx/yyy/zzz.mcfunction*
```text
# Some code
schedule on_area_loaded 1 2 3 11 22 33 xxx/yyy/zzz/aaa
```

*BP/functions/xxx/yyy/zzz/aaa.mcfunction*
```text
# The code of the subfunction
```

### Calling subfunctions from `definefunction` and `function`
Since the pattern of creating the subfunction names is known, you can call them like any other function.

(subfunctions-comments)=
## Comments
A double hash `##` is used to comment out the rest of the line. Comments that start with `##` are not copied to the compiled file, unlike normal mcfunction comments (single hash `#`).

## Function Trees

Function trees are binary structures that allow you to run Minecraft commands based on a scoreboard value. Since the introduction of the Scripting API into Minecraft they are not as useful as they used to be, but if you prefer to use function based workflow, they provide a performant way of converting scoreboard values into commands.

```{note}
Function trees generate a lot of files; each step in the search tree is a separate file. Searching through large ranges of values (more than a few hundred) is not recommended.
```

(subfunctions-functiontree)=
### `functiontree` - a binary tree of functions
#### Syntax
```text
functiontree <[function_name]><[selector] [scoreboard] [start]..[stop]>:
    [function_body]
- function_name: a base name of the new functions to create (must follow the
  pattern [A-Za-z0-9_]+), the files are created inside a folder with the same
  name as the root function and are named after function_name with the addition
  of a suffix that represents
  the scoreboard range they search.
- selector: the selector or a fake player name used for generating execute
  commands.
- scoreboard: the name of the scoreboard used for function tree.
- start: the starting value of the range.
- stop: the end value of the range.
- step: the incrementation step (optional).
- function_body: multiline body of the function. The body ends
  with the line that doesn't have enough indentation (like in the Python
  programming language).
```
#### Description
`functiontree` allows you to quickly search values of a `scoreboard` for `selector` using a binary tree made out of function files calling each other. It also adds a variable to the scope named after the `scoreboard`. The value of the variable is the same as the value of the `scoreboard` at the moment of calling the function. It can be accessed using `eval` (see documentation below).

#### Example
This is an example of a simple implementation of getting elements from a table created from 100 scoreboards that uses `tab_index` scoreboard to point at the index of the table to be accessed and `table_io` to copy the value from the table.

Source: *BP/functions/table.mcfunction*
```text
functiontree <table_get><@s tab_index 0..100>:
    scoreboard players operation @s table_io = @s t_`eval:tab_index`
```

Compiled code: *BP/functions/table.mcfunction*
```text
execute if score @s tab_index matches 0..49 run function table/get_0_49
execute if score @s tab_index matches 50..99 run function table/get_50_99
```

Example branch: *BP/functions/table/get_0_49.mcfunction*
```text
execute if score @s tab_index matches 0..24 run function table/get_0_24
execute if score @s tab_index matches 25..49 run function table/get_25_49
```

Example leaf: *BP/functions/table/get_1_2.mcfunction*
```text
execute if score @s tab_index matches 1..1 run scoreboard players operation @s table_io = @s t_1
execute if score @s tab_index matches 2..2 run scoreboard players operation @s table_io = @s t_2
```

## Variables and Code Evaluation
Subfunctions can define variables and use them in Python expressions whose value is evaluated and inserted into the function code. If you're using Subfunctions as a module of System Template, all of the variables defined in {ref}`all of the scopes<scopes-and-variables>` are available. If you're using Subfunctions as an independent filter, you can define variables in the scope file, whose path is defined in the {ref}`filter configuration<subfunctions-filter>`.

### `var` - defining variables for later use in the same function
#### Syntax
```text
var [identifier] = [expression]
- identifier: the name of the variable.
- expression: expression whose value is assigned to the variable.
```
#### Example
```text
var pi = 3.14
```

### `>` - evaluating expression without assigning it to a variable
#### Description
This is almost the same as `var` but there is no variable to add to the scope. This is useful for modifying existing variables (for example expanding lists).
#### Syntax
```text
> [expression]
- expression: the expression to be evaluated.
```
#### Example
```text
> my_list.append(2)
```

### `eval` - static code generation based on simple expressions
#### Syntax
```text
`eval:[math_expression]`

- math_expression: the expression to be evaluated and inserted instead to
  the function of eval. 
```
#### Description
Eval is used to generate code based on basic math expressions. It can also access the variables from `for` and `functiontree`.

## Loops

Writing Mcfunction code often requires repeating the same or very similar code multiple times. To make it easier, subfunctions provide a few ways to generate code in a loop.

### `for` - generating code in a loop
#### Syntax
```text
for <[variable] [start]..[stop] [step]>:
    [body]
- variable: the variable used in the for loop added to the scope for `eval`.
- start: starting value of range.
- stop: end value of range.
- step: the incrementation step (optional).
- body: multiline body of the for block. The body ends
  with the line that doesn't have enough indentation (like in Python
  programming language).
```
#### Example
Source:
```text
for <i 0..25 5>:
    say hello `eval:i`*100=`eval:i*100`
```

Compiled code (added to the same function):
```text
say hello 0*100=0
say hello 5*100=500
say hello 10*100=1000
say hello 15*100=1500
say hello 20*100=2000
```

### `foreach` - generating code from collections
#### Syntax
```text
foreach <[index] [variable] [expression]>:
    [body]
- variable: the variable used in the for loop added to the scope for `eval`
- index: a name of the variable used to keep track of the item index in the
  collection (starting from index 0). The value is added to the scope and
  accessible in `eval`.
- body: multiline body of the for block. The body ends
  with the line that doesn't have enough indentation (like in Python
  programming language)
```
#### Example
Source:
```text
foreach <i animal ["cat", "dog", "parrot"]>:
    say `eval:i` `eval:animal`
```

Compiled code (added to the same function):
```text
say 0 cat
say 1 dog
say 2 parrot
```

## Conditional Statements
Currently, Subfunctions support only one conditional statement - `if` statement.

### `if` - generating code based on condition
#### Syntax
```text
if <[expression]>:
    [body]
- expression: the expression with a condition that decides whether the body
  of the if block should be included in the function.
- body: multiline body of the if block. The body ends with the line that
  doesn't have enough indentation (like in the Python programming language)
```

#### Description
The `if` statement executes the body of the block only if the expression evaluates to `True`. The expression can be any valid Python expression. Currently, the `if` statement doesn't support `else` or `else if` statements.

(subfunctions-unpacking)=
## Unpacking Functions
Unpacking subfunctions is a feature that allows you to have a single file that doesn't get converted into a function, but instead, it gets converted into multiple functions defined using the `definefunction` command. There are two types of unpacking functions: `UNPACK:HERE` and `UNPACK:SUBFUNCTION`.

### `UNPACK:HERE` and `UNPACK:SUBFUNCTION`
#### Description
`UNPACK:HERE` and `UNPACK:SUBFUNCTION` are special annotations that you can put at the first line of the mcfunction file. The files with these annotations are deleted from the pack. They can be used to create multiple function definitions in one place using `definefunction`. Using `function <>:` and normal commands in files with these annotations, outside of `definefunction` code block is not allowed.

- `UNPACK:HERE` unpacks the functions defined with `definefunction` in the same path as the source file. It ignores the name of the source file.
- `UNPACK:SUBFUNCTION` doesn't modify the default behavior of `definefunction`. It unpacks the functions into a subfolder with the same name as the source file.

## Assertions

When writing a complex code in a subfunction, you may want to check if the data is what you expect it to be. For example, you may want to check if a variable is a specific type or if it has a specific value. You can do this using assertions. When your code is dynamically generated based on provided scope, you may want to check if the scope fulfills some conditions. You can do this using assertions.

### `assert` - breaking the execution based on the condition
#### Syntax
```text
assert [expression]
- expression: the expression which decides whether the execution should
  be broken.
```

#### Description
The `assert` checks if the expression is true. If it is not, the execution breaks,and the error is printed in the console. This way, you can catch errors in your code in a predictable way before they cause problems in the generated code (which may be hard to debug). Assertions don't have any effect on the generated code.

## Indentation Rules

In normal mcfunction files, indentation doesn't matter. Subfunctions use the indentation to group the code in the same way as in Python. As shown in the examples above, the indentation is used to determine the scope of blocks like `for`, `definefunction`, `functiontree`, etc.

Apart from all of these situations, where the indentation has a syntactic meaning, there is one additional case in which the indentation is allowed: You can create indented blocks of code if they have a comment at the top used as a header. Here is an example:
```text
# This is a comment about the block of code
    say first command
    say second command
    say third command
```
Without the comment or the use of the syntax that requires indented blocks, you can't use arbitrary indentation:
```text
say This is not allowed!
    say This line will create an error!
    say second command
    say third command
```
This limitation lets Subfunctions detect errors like typos in the lines that can be used to start an indented block (for example `define_function` instead of `definefunction`).
```text
define_function <my_function>:
    say Subfunctions would detect an error here!
```