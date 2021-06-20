+++
date = "2021-04-13"
draft = true
title = "A Practical Guide for GNU awk With Examples"
image = "/images/2021/sed/sed.jpg"
aliases = [
    "sed-beginner-mouseless"
]
+++

The awk utility can execute programs in the awk programming language. This programming language is specialized in manipulating plain text. 

First, to be sure you have GNU awk installed on your computer, you can run in your terminal `awk --version`. If you don't have GNU awk (but BSD for example), I would recommend you to install its GNU counterpart. You'll have access to many more options that way.

This article is a summary of these two videos. They are more detailed and they have some exercises to help you remember everything:

{{< youtube _Xbi9AtU3mc >}}

The second part:

{{< youtube YhWscDnmCBQ >}}

To please the Mighty Youtube Algorithm, don't hesitate to like my videos, subscribe to my channel, send me some love letters, and pray for my soul.

I would also recommend you to:

1. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/06_awk). Every example are based on them.
2. Fire up your beautiful shell.
3. Try each example you'll see in this article.

I'm sorry to be the one to tell you that but [you won't learn much](https://thevaluable.dev/learning-developer-efficiently-effectively/) if you only read (or watch a video) passively.

If you want to dig deeper, I the awk manual can be summoned with the usual `man awk` in your shell.

Let's begin.

## Basics

As I was saying above, the name "awk" can refer to a CLI or a programming language. The CLI itself can do two basic things:

1. Split the input files in sequence of *records*.
2. Break down each record in sequence of *fields*.

By default, a record is a line and fields are separated with whitespaces or newline characters. These defaults can be changed as we'll see below.

The awk programming language is a sequence of patterns with corresponding actions. The actions are executed on the input of the awk CLI if the records match the patterns. Said differently, the structure of a simple awk command-line looks like this:

```
awk '<pattern> { action }'
```

At a minimum you can have only one pattern, only one action, or multiple patterns with multiple actions. No action and not pattern is not allowed. 

This is not the complete structure of an awk command, but we'll add more information as we progress in our little adventure.

There are only two types in the awk programming language: strings (surrounded with double quotes `"`), and numbers.

## The Print Action

The print action allow you to... print records. For example:

```bash
awk '{ print }' lsblk
```

Here, `lsblk` is a file we give to awk as input. You'll get this output

```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0 465.8G  0 disk
sda1          8:1    0   200M  0 part /boot
sda2          8:2    0    16G  0 part [SWAP]
sda3          8:3    0    60G  0 part /
sda4          8:4    0 389.6G  0 part /home
```

The print action is the default action used if you don't give any to awk. 

What if we want to print the first field of each record? 

```
awk '{ print $1 }' lsblk
```

The output will be:

```
NAME
sda
sda1
sda2
sda3
sda4
```

Remember: by default a record is a line and a field is a string beginning and ending with a whitespace or a newline character. Here, we ask awk to print the first record of every line.

The records are one-indexed, that's why the first record is `$1` and not `$0`. What's `$0`? Well, the best is to try it:

```
awk '{ print $0 }' lsblk
```

You'll get this output:

```
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0 465.8G  0 disk
sda1          8:1    0   200M  0 part /boot
sda2          8:2    0    16G  0 part [SWAP]
sda3          8:3    0    60G  0 part /
sda4          8:4    0 389.6G  0 part /home
```

What happened here? The symbol `$0` represents the entire record, that's why each record are output.

We can print more than one field. For example:

```
awk '{ print $1,$2,"hello" }' lsblk
```

I let you run this command in your shell to see the result. Using a comma `,` between what you want to output will automatically add a space in the output itself.

## Expressions

Now that we saw the most basic action, let's look at the expressions you can use in patterns or actions. First, let's add a basic pattern to our awk command:

```
awk '/sda/ { print $1 }' lsblk
```

Here's the output:

```
sda
sda1
sda2
sda3
sda4
```

We want here to print the first field of every record. The pattern `/sda/` will filter the different records: only the records matching the regular expression `sda` will be used.

We can use other basic expression in the pattern too. For example, if we want the record to be *exactly* `sda` to print then its first field, we can do:

```
awk '$1 == "sda" { print $1,$2,$5 }' lsblk
```

Here's the output:

```
sda 8:0 0
```

You can try in your shell the following. These are not difficult to understand if you have some experience with programming languages using a C-like syntax:

```
awk '$2 == 4+4 ":0"  { print $1,$2,$5 }' lsblk
awk '$2 == 4+4 ":0" && $3 < 2' lsblk
awk '$2 == 4+4 ":0" && $3 < 2  { print $1,$2,$5 }' lsblk
```

Note here that a space in the pattern (between `4+4` and `:0` for example) represents a concatenation. For example, these two commands are equivalent:

```
awk '$1 == "sda" { print $1,$2,$5 }' lsblk
awk '$1 == "s" "d" "a" { print $1,$2,$5 }' lsblk
```

## Variables

You can use different variables in the awk programming language. Here are some I find the most useful:

* `NF` - `N`umber of `F`ield
* `NR` - `N`umber of `R`ecord

### NR or Number of Record

An example is often better than a long chunk of boring text:

```
awk '{print $1,$4,NF}' lsblk
```

Here's the output: 

```
NAME SIZE 7
sda 465.8G 6
sda1 200M 7
sda2 16G 7
sda3 60G 7
sda4 389.6G 7
```

As you can see, `NF` display the total number of field for each record, even if you only output only some of them.

Practically, `NV` can be useful to use in the pattern to filter records:

```
awk 'NF > 6 {print $1,$4,NF}' lsblk
```

This will output:

```
NAME SIZE 7
sda1 200M 7
sda2 16G 7
sda3 60G 7
sda4 389.6G 7
```

Here, we only display the records which have more than 6 fields.

### NR or Number of Record

The variable `NR` can be useful to print the record number, with this command line for example:

```
awk '{print NR,$1,$4}' lsblk
```

Here's the output: 

```
1 NAME SIZE
2 sda 465.8G
3 sda1 200M
4 sda2 16G
5 sda3 60G
6 sda4 389.6G
```

If you want to print only specific records, you can use a pattern. For example:

```
awk 'NR != 1 { print "My disk",$1,"is",$4 }' lsblk
```

The output will skip the first record:

```
My disk sda is 465.8G
My disk sda1 is 200M
My disk sda2 is 16G
My disk sda3 is 60G
My disk sda4 is 389.6G
```

## Options

Here's a couple of useful options you can use with awk:

### Changing the Fields Separator

As we saw already above, the default field separator is a whitespace or a newline. You can change this using the option `-F` (for `f`ield).

It's useful if you want to manipulate a CSV with commas `,` as separators for example:

```
awk -F "," '{print $2}' product_sold.csv
```

Here's a shorten version of the output:

```
price
20.00
20.00
20.00
20.00
10.00
20.00
12.12
```

### Changing the Value of a Variable

The option `-v` can change the value of a `v`ariable. By default, the value of the variable `NR` is 0, but you can set it to 10 for example:

```
awk -v NR=10 '{ print NR,$1 }' lsblk
```

Here's the output:

```
11 NAME
12 sda
13 sda1
14 sda2
15 sda3
16 sda4
```

You can also create a variable you can use in the pattern or the action. For example:

```
awk -v "my_var=10" '{ print NR,$1,my_var }' lsblk
```

Here's the output:

```
1 NAME 10
2 sda 10
3 sda1 10
4 sda2 10
5 sda3 10
6 sda4 10
```

Is it really useful in real case scenario? It's an excellent way to pass to your actions or patterns some external shell variable. For example, the following won't work:

```
MYVAR="sda"
awk '$1 ~ $MYVAR {print $1,$4}' lsblk
```

This won't work, because `$MYVAR` is not expanded by the shell in that case. We need to pass the shell variable in our awk program:

```
MYVAR="sda"
awk -v var="$MYVAR" '$1 ~ var {print $1,$4}' lsblk
```

This last example will work. The expression `~` means that the first record represented by the symbol `$1` needs to match the regular expression of `var` (with the value `sda`). We can't use the notation `/var/` with variables to match a regular expression.

### Writing an awk Program in a File

If you're awk program begins to be too complicated to write in the shell, you can use the option `-f` to write it in a file. For example:

```
echo '{print NR,$1,$4}' > my_awk_program
awk -f my_awk_program lsblk
```

## Special Patterns: BEGIN and END

There are two special patterns you an use in an awk program which are really useful: 

`BEGIN` - Run actions before processing the input.
`END` - Run actions after processing the input.

You can use multiple `BEGIN` and `END` patterns. In that case, the actions associated will be executed in order.

### The Special Pattern BEGIN

As always, let's try to uncover the mystery of `BEGIN` with an example. Let's try to run the following:

```
awk '{ print $1,$4 }' lsblk
```

Here's the output:

```
NAME SIZE
sda 465.8G
sda1 200M
sda2 16G
sda3 60G
sda4 389.6G
```

What if we want to replace the headers `NAME` and `SIZE` with `Disk` and `Size` respectively, and adding a new line between the headers and the results?

```
awk 'BEGIN {print "Disk","Size","\n"}
     NR != 1 { print $1,$4 }' lsblk
```

What happens here?

* We have two patterns, each of them linked to one action each.
* The first pattern `BEGIN` executes the linked action `print "Disk","Size","\n"` before processing the input. This action output the headers we want.
* The second pattern `NR != 1` executes the linked action `print $1,$4` only if the record is not the first one. As a result, we don't output the headers from the input.

Here's the output:

```
Disk Size

sda 465.8G
sda1 200M
sda2 16G
sda3 60G
sda4 389.6G
```

### The Special Pattern END

The pattern `END` is the inverse of `BEGIN`: it executes some options *after* processing the INPUT.

For example, if we want to change the header with `BEGIN` and count the number of partitions from the file "lsblk", we can do that:

```
awk 'BEGIN { print "Disk","Size","\n"; TP=0 }
     NR != 1 { print $1,$4 }
     /sda[0-9]/ { TP=TP+1 }
     END {print"";print "TOTAL PARTITIONS:",TP}' lsblk
```

Let's decompose what's happening here:

* We have 4 patterns linked to 4 different actions.
* The first action contains a semi-colon `;`. It allows us to execute two different statements in the same action.
* The first action print our headers and initialize a variable `TP`.
* The pattern `NR != 1` will filter the first record. The action will output some fields of every other records.
* The pattern `/sda[0-9]/` will filter every record which doesn't match the regex. The action will increment the variable TP.
* The pattern `END` will make sure to execute its action after processing the input. It will first print an empty string (the equivalent of printing a newline) and, in another statement, print a string with the value of the variable `TP`.

These pattern / action pairs are separated with newlines for readability purposes, but we can separate them with whitespaces too.

## Computations From a CSV

We can now compute useful information from a CSV, for example [product_sold.csv](https://github.com/Phantas0s/mouseless-dev-youtube/blob/master/07_awk/product_sold.csv).

For example, we could count every products sold for 20 euros.

```
awk -F "," 'NR != 1 && $2 == 20 {product = product + 1}
            END {print product,"products costing 20euro have been sold"}' product_sold.csv
```

We could also compute the total of sales as follows:

```
awk -F "," 'NR != 1 {product = product + $2}
            END {print "Total of sales is",product}' product_sold.csv
```

From there the sky's the limit!

## In a Nutshell: a Mindmap of sed

[{{< picture src="/images/2021/sed/sed.png" webp="/images/2021/sed/sed.webp" alt="mindmap of sed" >}}](/images/2021/sed/sed.jpg)

## Using sed in Practice

It's nice to learn all of that, but how can you use sed in real life? Here's a video showing you a problem I had and I solved nicely using sed and other CLIs:

{{< youtube 4DwXYAAgKKU >}}

