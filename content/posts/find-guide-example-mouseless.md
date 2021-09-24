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

### Filtering by Name

Filtering your files and folders by name is the most common use of find. 

* `-name <value>`  - Filter every files and directories by filename. You can't use regular expressions for the `<value>`, but shell patterns (also called *globbing*) are authorized, like  `*`, `?`, or `[]`. For example: `find . -name "*.log"` to find all the log files from your working directory.
* `-path <value>` - Filter every files and directories by filepath. Like `-name`, it doesn't accept regexes but globbing.
* `-regex <value>` - Filter every files and directories filepath using a regex. You can use the positional option `-regextype` *before* `-regex`, to specify the regex engine you want to use. To output a list of regex engines supported, you can run `find . -regextype dummy`

If you want your expression's value to be case insensitive, you can add the prefix `i` to these expressions. For example `-iname`, `ipath`, or even `-iregex`.

### Filtering by Permissions

You can also filter files if they're whether `-writable`, `-executable`, or `-readable` for your current user. You can also use `-perm <value>`, where `<value>` can be:

* A string beginning with `/` and followed by a series of rules using the OR boolean operator. For example, `-perm /u=w,g=e` (writable by user, and executable by group).
* A string beginning with `-` and followed by a series of rules using the AND boolean operator. For example, `-perm -u=w,g=e` (writable by user, or executable by group).
* An octal number, for example: `644`.

### Filtering by Type of File

Here are the three values you can use with `-type <value>`, to filter your search by type of file:

* `f` - Regular file
* `d` - Directory
* `l` - Symlink

For example, if you want to find only directories called `directory` from your working directory , you can run `find . -name "directory" -type d`.

### Filtering by Owner

Finally, you can filter files depending of their owners, using the expression `-user <value>` where `<value>` is a username.

## Action Expressions

With find, you're not only doing searches, you can also perform some actions on the results.

### Deleting Files

You can easily delete every files and directories found using the `-delete` option. For example, the command `find . -name "test*" -delete` will delete all files and directory which names begin with `test`. 

Be careful however: you can't get the deleted files back! That's why I don't use this expression at all.

### Running a Command on Each Result

This is a very powerful functionality of find: you can run whatever command you want on each file found! You can use a couple of expressions for doing that:

* `-exec` - The command use your current working directory to run the command. The characters `{}` are used to indicate where you want to insert the result of the search in the command. Note also that you need to use `;` to end the command (it allows you to add other commands afterward). For example: 
    * `find . -exec basename '{}' ';'` - Run the command `basename` for every result of the search.
    * `find ~/Documents -exec bash -c 'basename "${0%*.}"' '{}' ';'` - Use `bash -c` if you want to use some parameter expansion (like `${0%.*}` not to display files' extensions). Even if `Documents` is the starting point for the search, the command will be executed in the context of your working directory.
* `-execdir` - Works like `-exec`, except that the context of the command won't be your current working directory, but the starting point of the search.

===

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
