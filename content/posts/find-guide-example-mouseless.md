+++
date = "2021-06-25"
draft = true
title = "A Practical Guide of GNU find With Examples"
image = ""
+++

The CLI (Command-Line Interface) find is great to search recursively any file given a set of expressions. That's not all: you can also use it to perform actions on the files found.

To know if you have GNU find, you can run in your terminal `find --version`. If your version is different (BSD, for example), I would recommend to rely on GNU find instead. In general, GNU tools are better than their Unix counterpart.

This article is a summary of two videos I've recorded. I would recommend to watch them first, it's a more complete description of find with examples and exercises:

{{< youtube nJ7NvcPnVE4 >}}

{{< youtube mSEVOZWtlJc >}}

If you find the videos helpful, don't hesitate to hit the like button and to subscribe to the channel. It would help me to help more!

I also recommend to: 

1. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/08_find)
2. Fire up your beautiful shell
3. Try each command you'll see in this article

You won't learn much if you only read (or watch videos) passively.

In this article we'll see together:

* The basics of find
* How to search files by name
* How to search files by type
* How to search files by permissions
* How to search files by users
* How to delete automatically the files found
* How to run any command on the files found
* How to manipulate the resulting output
* How to write and format the output automatically to files
* What are find's operators you can use

Hope you're ready to find with me!

## Basics of find

Here's the basic command-line structure:

```
find <options> <starting_point> <expressions>
```

What are these elements?

* `<options>` - We won't speak about find's options in this article. They're mainly about symlinks and search optimization.
* `<starting_point>` - List of directories where you want to search. They'll be the root of the search.
* `<expressions>` - They filter the search or perform an action on the results. They begin with a single dash `-`.

You can form with expressions a query to search what you want to find. Some of them need a value.

These expressions can be of different types:

* Test expression - They are the most common. They're used to filter the search.
* Action expression - You can act on each file of the search result using these.
* Global options - They'll change the behavior of test and action expressions.
* Positional options - They only change the behavior of test and action expressions directly following them.
* Operators - They're boolean operators, like AND and OR, to manage the relationships between the different expressions.

When using multiple expressions without specifying any operator, the AND operator is implicitly used. 

Here's a basic example:

```
find /home/user -name "super-file" -perm 664 
```

* `/home/user` - Starting point of the search.
* `-name` - A test expression.
* `"super-file"` - Value of the expression `-name`.
* `-perm` - Another test expression.
* `664` - The value of the expression `-perm`.

Nothing difficult here!

## Test Expressions

Test expressions are used to filter the folders and files. I list here the most useful ones.

### Filtering Empty File

The first expression is easy: `-empty` will search only empty files and directories. Next!

### Filtering by Names

Filtering your files and folders by name is the most common use of find. 

#### Filtering by File Name

The expression `-name <value>`  filter every files and directories by filename. You can't use regular expressions for the `<value>`, but shell patterns (also called *globbing*) are authorized, like  `*`, `?`, or `[]`. 

For example: `find . -name "*.log"` to find all the log files from your working directory.

#### Filtering by File Path

The expression `-path <value>` filter every files and directories by their file paths. Like `-name`, it doesn't accept regexes but shell patterns.

#### Filtering Using a Regex

If you want to use a regex, the expression `-regex <value>` is here for you. It will filter every file and directory file paths. 

You can also use the positional option `-regextype` *before* `-regex`, to specify the regex engine you want to use. To output a list of regex engines supported, you can run `find . -regextype dummy`.

For example, `find . -regextype "egrep" -regex '.*(md|log)$'` will find every markdown and log files using `egrep`, the extended regular expression engine ([ERE](https://linux.die.net/man/1/egrep)).

#### Case Insensitive

If you want your expression's value to be case insensitive, you can add the prefix `i` to these expressions. For example `-iname`, `ipath`, or even `-iregex`.

### Filtering by Permissions

You can also filter files if they're whether `-writable`, `-executable`, or `-readable` for your current user. If you want more granularity for your filter, you can use `-perm <value>`, where `<value>` can be:

* A string beginning with `/` and followed by a series of rules using the OR boolean operator. For example, `-perm /u=w,g=e` (writable by the owner, *and* executable by the group).
* A string beginning with `-` and followed by a series of rules using the AND boolean operator. For example, `-perm -u=w,g=e` (writable by owner, *or* executable by the group).
* An octal number, for example: `644`.

### Filtering by Type of File

Here are the three values you can use with `-type <value>`, to filter your search by type of file:

* `f` - Regular file
* `d` - Directory
* `l` - Symlink

For example, if you want to find only directories named `log` from your working directory, you can run `find . -name "log" -type d`.

### Filtering by Owner or Group

To filter files depending of their owners, you can sue the expression `-user <value>` where `<value>` is a username. You've guessed it, to filter by group, use `-group <value>`.

## Action Expressions

With find, you're not only doing searches, you can also perform some actions on the results.

### Deleting Files

You can easily delete every files and directories found using the `-delete` option. For example, the command `find . -name "test*" -delete` will delete all files and directory which names begin with `test`. 

Be careful however: you can't get the deleted files back! I never use it personally, it's too frightening.

### Running a Command on Each Result

With find, you can run whatever command you want on each file found. The following expressions will help you for this mighty goal.

#### Running a Command in the Working Directory

The expression `-exec` allow find to run a command in your current directory. The characters `{}` are used to indicate where you want to insert the result of the search in the command you want to run. You also need to use `;` to end the command (it allows you to add other commands afterward, even if anybody use that). 

For example:

* `find . -exec basename '{}' ';'` - Run the command `basename` for every result of the search.
* `find chapters -exec bash -c 'basename "${0%.*}"' '{}' ';'` - Use `bash -c` if you want to use some parameter expansion. We use here `${0%.*}` to delete the file extension of each result.

You can also use the expression `-ok`. It's the same as `-exec`, except that you'll be asked for a confirmation to run the command for each result.

#### Running a Command in the Starting Directory

The two expressions `-execdir` and `-okdir` work like `-exec` and `-ok` respectively, except that the commands won't run in your current working directory, but in the starting directory (the first argument of find).

For example, `find ~/Documents -exec bash -c 'basename "${0%*.}"' '{}' ';'` will run the command `bash -c 'basename "${0%*.}"'` on every result in the directory `~/Documents`.

### Changing the Output

You want to change the output of the results? The following expressions will be your best friends:

* `-print` - This is the default action used when you don't specify any. It simply prints every result.
* `-ls` - Output the results like the command `ls` would do.
* `-print0` - By default, the separator between the different results is a newline character. That's why each result are on a new line in your output. With `-print0`, it's a null character instead. It's quite useful if you want to pipe your results to `xargs -0`.
* `-printf` - Output files with different information you want. For example: `find . -printf %d %p` will print the depth of the file in the file tree (`%d`) and the filename (`%p`).

### Writing to a file

You can also use a bunch of action expressions to write find's output to a file. You just need to prefix the expression we saw above with a `f`, like `-fls`, `-fprint`, `-fprint0` or `-fprintf`.

## Operators

With find, you have access to a bunch of operators you can use with expressions. When you don't precise any, the operator `-and` is used implicitly between each expression. That is, every test expression needs to be true. You can also use:

* `!` - Negation. For example, `find . ! -name ".hidden"` will output every file except the ones named `.hidden`.
* `-or` or `-o` - For example `find . -name "*.log" -or -name "*.md"` will output every files with extensions `log` or `md`.
* `-and` or `-a` - For example, `find . -name "*.d" -and -type d` will find every directory which name finish with `.d`. It's equivalent to `find . -name "*.d" -type d`.
* `,` (comma) - adding a comma is useful to use different sets of expression while traversion the filesystem once. For example, `find . -name "*.log" -fprint log_files ',' -name "*.md" -fprint md_files` will write every markdown files in the file `md_files`, and every log file in the file `log_files`.

## In a Nutshell: a Mindmap of find

==== CONTINUE THE MINDMAP + REREAD

[![summary how to use grep in mind map](https://themouseless.dev/images/2021/grep/grep.jpg)](https://themouseless.dev/images/2021/grep/grep.jpg)
