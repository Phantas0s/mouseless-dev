+++
date = "2021-11-21"
draft = false
title = "A Practical Guide of GNU find With Examples"
image = "/images/2021/find/find.jpg"
+++

The CLI (Command-Line Interface) find is great to search recursively any file, given a set of expressions. That's not all: you can also use it to perform actions on the files found.

To know if you have GNU find, you can run in your terminal `find --version`. If your version is different (BSD, for example), I would recommend relying on GNU find instead. In general, GNU tools are better than their Unix counterpart.

This article is a summary of two videos I've recorded. I would recommend watching them first, it's a more complete description of find with examples and exercises:

{{< youtube nJ7NvcPnVE4 >}}

{{< youtube mSEVOZWtlJc >}}

If you find the videos helpful, don't hesitate to hit the like button and to subscribe to the channel. It would help me to help more!

I also recommend to:

1. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/08_find).
2. Fire up your beautiful shell.
3. Try each command you'll see in this article.

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
* How to write and format the output in files
* What are find's operators you can use

Let's go finding together!

## The Basics of find

Here's the basic command-line structure:

```
find <options> <starting_point> <expressions>
```

What are these elements?

* `<options>` - We won't speak about find's options in this article. They're mainly about symlinks and search optimization.
* `<starting_point>` - List of directories where you want to search. They're the root of the search.
* `<expressions>` - The expressions filter the search or perform actions on the files found. They begin with a single dash `-`.

You can create a query with expressions, to search what you want to find. Most of them need a value. These expressions can be of different types:

* **Test expressions** - They are the most common. They're used to filtering the search.
* **Action expressions** - You can act on each file of the search result using these.
* **Operators** - They're boolean operators, like AND and OR, to manage the relationships between the different expressions.
* **Global options** - They'll change the behavior of test and action expressions.
* **Positional options** - They only change the behavior of test and action expressions directly following them.

When using multiple expressions without specifying any operator, the AND operator is implicitly used.

For you to understand my obscure rambling, here's a basic example:

```
find /home/user -name "super-file" -perm 664 
```

Let's decompose this command:

* `/home/user` - Starting point of the search.
* `-name` - A test expression.
* `"super-file"` - Value of the expression `-name`.
* `-perm` - Another test expression.
* `664` - The value of the expression `-perm`.

In short, we're searching in the directory `/home/user` and all its subdirectories a file named "super-file" which has the permissions `664`.

## Test Expressions

Test expressions are used to filter the folders and files. I list here the most useful ones.

### Filtering Empty File

The first expression is easy: `-empty` will search only empty files and directories. It doesn't need a value. 

Next!

### Filtering by Names

Filtering our files and folders by name is the most common use of find.

#### Filtering by File Name

The expression `-name <value>` filter files and directories by filename. You can't use regular expressions for the `<value>`, but shell patterns (also called *glob operators*) are authorized, like  `*`, `?`, or `[]`. 

For example: the command `find . -name '*.log'` finds all the log files from your working directory. It's equivalent to `find -name '*.log'`, but I always try to specify the starting point to be as clear as possible.

#### Filtering by File Path

The expression `-path <value>` filter files and directories by their file paths. Like `-name`, it doesn't accept regexes but shell patterns.

#### Filtering Using a Regex

If you want to use a regex, the expression `-regex <value>` is here for you. It will filter files paths. 

You can also use the *positional option* `-regextype` *before* `-regex`, to specify the regex engine you want to use. To output a list of regex engines supported, you can run `find . -regextype dummy`.

For example, `find . -regextype "egrep" -regex '.*(md|log)$'` will find every markdown and log files using `egrep`, the extended regular expression engine ([ERE](https://linux.die.net/man/1/egrep)).

#### Case Insensitive

If you want your expression's value to be case-insensitive, you can add the prefix `i` to these expressions. For example `-iname`, `ipath`, or even `-iregex`.

### Filtering by Type of File

Here are the three values you can use with `-type <value>`, to filter your search by file type:

* `f` - Regular file
* `d` - Directory
* `l` - Symlink

For example, if you want to find directories (and not regular files) named `log` from your working directory, you can run `find . -name "log" -type d`.

### Filtering by Permissions

You can also filter files if they're whether `-writable`, `-executable`, or `-readable` for the current user. If you want more granularity for your filter, you can use `-perm <value>`, where `<value>` can be:

* A string beginning with `/` and followed by a series of rules using the OR boolean operator. For example, `-perm /u=w,g=e` (writable by the owner, *and* executable by the group).
* A string beginning with `-` and followed by a series of rules using the AND boolean operator. For example, `-perm -u=w,g=e` (writable by owner, *or* executable by the group).
* An octal number, for example: `644`.

### Filtering by Owner or Group

To filter files depending of their owners, you can use the expression `-user <value>` where `<value>` is a username. 

You've guessed it, to filter by group, you can use `-group <value>`.

## Action Expressions

With find, you're not only searching, you can also perform some actions on the results.

### Deleting Files

You can easily delete files and directories found using the `-delete` option. For example, the command `find . -name "test*" -delete` will delete all files and directories when their names begin with `test`. 

Be careful however: you can't get the deleted files back! I never use it personally, it's too frightening.

### Running a Command on Each Result

You can also run whatever command you want on each file found. The following expressions enable us to reach this mighty goal.

#### Running a Command in the Working Directory

The expression `-exec` allow us to run a command in our working directory. The characters `{}` are used to indicate where you want to insert each result of the search in the command you want to run. You also need to use `;` to end the command (it allows you to add other commands afterward).

For example:

* `find . -exec basename '{}' ';'` - Run the command `basename` for every result of the search.
* `find chapters -exec bash -c 'basename "${0%.*}"' '{}' ';'` - Using `bash -c` allows us to expand parameters. We use here `${0%.*}` to delete the file extension of each result.

You can also use the expression `-ok`. It's the same as `-exec`, except that find will ask you if you really want to run the command. This confirmation will be asked on each result.

#### Running a Command in the Starting Directory

The two expressions `-execdir` and `-okdir` work like `-exec` and `-ok` respectively, except that the commands won't run in your current working directory, but in the starting directory (the first argument of find).

For example, `find ~/Documents -execdir bash -c 'basename "${0%*.}"' '{}' ';'` will run the command `bash -c 'basename "${0%*.}"'` in the directory `~/Documents`, and on each result.

### Changing the Output

You urgently want to change the output of find? The following expressions will become your best friends:

* `-print` - This is the default action used when you don't specify any. It simply prints every result.
* `-ls` - Output the results like the command `ls` would do.
* `-print0` - By default, the separator between the different results is a newline character. That's why each result are output on a new line. With `-print0`, the separator is a null character instead. It's quite useful if you want to pipe your results to `xargs -0`.
* `-printf` - Output files with the information you need. For example: `find . -printf %d %p` will print the depth of the file in the file tree (`%d`) and the filename (`%p`).

### Writing the Output to a File

You can also use a bunch of action expressions to write find's output to a file. You just need to prefix the expression we saw above with a `f`. For example: `-fls`, `-fprint`, `-fprint0` or `-fprintf`. The value of these expressions will be the file written.

## Operators

Find, additionally to expressions, allow you to use a bunch of operators. When you don't precise any, the operator `-and` is used implicitly between each expression. That is, every test expression needs to be true. You can also use:

* `!` - Negate the expression following it.
* `-or` or `-o` - Logical or.
* `-and` or `-a` - Logical and.
* `,` - adding a comma is useful to use different sets of expression while traversing the filesystem once. 

Here are some examples:

* `find . ! -name ".hidden"` - output every file except the ones named `.hidden`.
* `find . -name '*.log' -or -name '*.md'` - output every file with extensions `log` or `md`.
* `find . -name '*.d' -and -type d` - find directories which names finish with `.d`. It's equivalent to `find . -name '*.d' -type d`
* `find . -name '*.log' -fprint log_files ',' -name '*.md' -fprint md_files` will write every markdown files in the file `md_files`, and every log file in the file `log_files`.

## In a Nutshell: a Mindmap of find

[{{< picture src="/images/2021/find/find.jpg" webp="/images/2021/find/find.webp" alt="mindmap of find" >}}](/images/2021/find/find.jpg)
