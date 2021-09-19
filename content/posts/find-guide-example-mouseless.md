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

These expressions can be:

* Test expression - They are the most common. They're used to filter the search.
* Action expression - You can act on each file of the search result using these.
* Global options - They'll change the behavior of test and action expressions.
* Positional options - They only change the behavior of test and action expressions directly following them.
* Operators - They're boolean operators, like AND and OR, to manage the relationships between the different expressions.

When using multiple expressions without specifying any operator, the AND operator is implicitly used.

## Test Expressions

* Often used to filter folders and files
* Return true or false

* `-empty` - empty files

* Name
    * `-name` - Search by filename (and not filepath) with shell pattern
        * Shell pattern: `*`, `?`, `[]`
        * `find -name "README.md"`
        * Case sensitive by default
    * `-wholename` - Search by filepath
        * `find -wholename "./Documents/README.md"`
    * `-regex` - Match regular expression pattern (with more metacharacters)
        * Match the whole path
        * -regextype
            * Used BEFORE `-regex` option
            * Display all possible type of regex
                * `find . -regextype aslkjsldfkj`
            * For example finding all md or log files
                * `find . -regextype "egrep" -regex '.*(md|log)$'`
                * Without regextype
                    `find . -regex '.*\(md\|log\)$'`
    * For case insensitive add a `i` before
        * `find -iname "readme.md"`

* Permissions
    * `-writable`
    * `-executable`
    * `-readable`
    * `-perm` - Need to be the exact permissions
        * Octal
            * `find . -perm 644`
        * find files writable by user symbolic
            * `find . -perm /u=w`
        * find files writable by user OR readable by group
            * `find . -perm /u=x,g=r`
        * find files writable by user AND readable by group
            * `find . -perm -u=w,g=w`
* Type
    * Can filter by type of file
        * Regular file
            * `find . -type f`
        * Directory
            * `find . -type d`
        * Symlink
            * `find . -type l`
* User
    * `-user <username>`
        * Files own by username
            * `find -user "hypnos"`
            * `find -user "root"`

### Exercises

* Output all the files in the folder Documents (folder included) using find
    * `find Documents`

* Find all the log files from your working directory
    * `find . -name "*.log"`

* Find all the log files from your working directory owned by your current user
        * `find . -name "*.log" -user "hypnos"`
    * If you have only one user on your Linux system, it's likely with id 1000
        * `find . -name "*.log" -user "1000"`

* Can you find the REGULAR files from your working directory owned by your current user which is readable, writable, and executable?
    * `find -user "1000" -perm "777" -type f`

## Actions

* `-delete`
    * `find . -name ".hidden" -delete`

* Run a command on Search result
    * `-exec command ;`
        * `find . -exec basename '{}' ';'`
        * Can be useful to use `bash -c` or `sh -c` to use parameter expension
            * Display filenames without extension
            * `find Documents -exec bash -c 'basename "${0%.*}"' '{}' ';'`
    * `-execdir command`
        * As if the command was run in the starting point
        * `find find -exec basename '{}' ';'`
    * `-execdir command ;`
    * `-ok`
        * `-exec` with prompt
        * `find . -ok rm '{}' ';'`
    * `-okdir`
        * `find Documents -ok rm '{}' ';'`

* Output
    * `-ls`
        * Print files like ls output
        * `find . -ls`
    * `-print`
        * `find . -print`
    * `-print0`
        * `find Documents -type f -print | xargs wc -l`
        * But what happens if there is a filename with spaces? (create it in Documents)
            * `find Documents -type f -print | xargs wc -l`
            * errors!
            * `find Documents -type f -print0 | xargs -0 wc -l`
    * `-printf`
        * `find . -printf "%d %p \n"`
        * `%d` for depth in file tree
        * `%p` for filename
        * `%P` for filename without starting point
        * `%M` for permissions

* Writing to file
    * Add a `f` before
        * `-fls`
        * `-fprint`
        * `-fprint0`
        * `-fprintf`

* Others
    * `-quit`
        * Can be useful to search only one file
        * `find . -name "*.log" -print -quit`

## Operators

* Negation or !
    * Display every files except `.hidden`
    * `find . ! -name ".hidden"`

* `-or` or `-o`
    * Using only `-name` to find different files
        * `find . -name "*.log" -or -name "*.md"`

* `-and` or `-a`
    * The default

* `,` (comma)
    * Useful for using different sets of expression but only traversing the filesystem once
    * For example
        * `find . -name "*.log" -fprint log_files ',' -name "*.md" -fprint md_files` 

# Exercises

* Find every directory from the working directory and write them into the file "dirs"
    * `find . -type d -fprint dirs`

* Find every files with extension ".log" and create prompts to delete these files (but don't delete any)
    * `find . -name "*.log" -ok rm {} ';'`

* Can you write to the file "log_files" every absolute paths to log files, only the relative path to markdown files in the file "md_files", and the relative paths of everything else (excluded log files and markdown files) in the file "other_files"
th
    * `find . -name "*.log" -fprint log_files ',' -name "*.md" -fprint md_files  ',' ! -name "*.log" ! -name "*.md" -fprint other_files`

* Can you rename every regular files using find, adding ".bak" extension
    * `find . -type f -exec mv {} {}.bak ';'`
