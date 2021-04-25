+++
date = ""
draft = true
title = ""
image = ""
aliases = [
    ""
]

description = "The basics of sed: how to delete precise lines and many ways to substitute whatever content you want using the command-line."
+++

Today is the first day for THE challenge.

In May, I'll give you a CLI each day. I'll try to make it neat for every living soul. I like hard mode, even when I lose.

Help me carving history with some shell madness. Gimme your tricks for each CLI to spread love in the Universe. 💕🧵

## Alias - 2021-05-01

$> alias [name]="[command]"

An alias can represent a particular command, often defining a short name for a long, complicated command. It can also run a command with specific options.

[01/31]

```
# Create the files for the demo
$> touch super_file_1 super_file_2 super_file_3

# Delete a file without confirmation
$> rm super_file_1

# Create an alias to always delete files with confirmation
$> alias rm="rm -i"

# Delete a file with confirmation
$> rm super_file_2
rm: remove regular empty file 'super_file_2'? y

# Ignore the alias using a slash \ (default behavior of rm)
$> \rm super_file_3
```

## nl - 2021-05-02

$> nl [input]

Number Lines of an input which can be a file, as often.

[02/31]

```
# Look at my cool file
$> cat nl_number_line
This is a line

Great Scott! This is a line too

# Number lines
$> nl nl_number_line
     1  This is a line

     2  Great Scott! This is a line too

# Option `-b` (`b`ody numbering) with style

## Style `a` to number `a`ll lines 
$> nl -b a nl_number_line
     1  This is a line
     2
     3  Great Scott! This is a line too

## Style `p` to number lines only when it matches a regex
$> nl -b p'Scott.*' nl_number_line
       This is a line

     1  Great Scott! This is a line too

# Option `-i` (line `i`ncrement)
$> nl -i 10 -b a nl_number_line
     1  This is a line
    11
    21  Great Scott! This is a line too
```

## sort - 2021-05-03

$> sort [input]

Sort lines of the input which can be a file. How surprising!

[03/31]

```
$> cat sort_that
korn_or_corn      800  2018-02-04
guide_to_seashell 3000 2021-01-01
how_to_terminal   200  2019-12-25

# Sort lines alphabetically
$> sort sort_that
guide_to_seashell 3000 2021-01-01
how_to_terminal   200  2019-12-25
korn_or_corn      800  2018-02-04

# Option `-r` `r`everse the sort
$> sort -r sort_that
korn_or_corn      800  2018-02-04
how_to_terminal   200  2019-12-25
guide_to_seashell 3000 2021-01-01

# Option -k sort via `k`ey (field)
$> sort -k 3 sort_that
korn_or_corn      800  2018-02-04
how_to_terminal   200  2019-12-25
guide_to_seashell 3000 2021-01-01

# Option -n do a `n`umeric sort:
$> sort -n -k 2 sort_that
how_to_terminal   200  2019-12-25
korn_or_corn      800  2018-02-04
guide_to_seashell 3000 2021-01-01

## Without `-n`
$> sort -k 2 sort_that
how_to_terminal   200  2019-12-25
guide_to_seashell 3000 2021-01-01
korn_or_corn      800  2018-02-04
```

## uniq - 2021-05-04 (AFTER SORT)

$> uniq [input]

Report or omit consecutive and repeated lines.

[04/41]

```
$> cat uniq_this
A LINE!
OH! LINE!
Wow! A line
Wow! A line
A LINE!

# Omit consecutive lines by defaults
$> uniq uniq_this
A LINE!
OH! LINE!
Wow! A line
A LINE!

# Option -u only output `u`nique lines
$> uniq -u uniq_this
A LINE!
OH! LINE!
A LINE!

# Options `-c` `c`ount number of occurences and add it as prefix.
$> uniq -c uniq_this
      1 A LINE!
      1 OH! LINE!
      2 Wow! A line
      1 A LINE!

# Option `-d` only print repeate`d` lines
$> uniq -d uniq_this
Wow! A line

# Option `-f` compare only a specfic `f`ield (separated by whitespace(s))
$> uniq -f 1 uniq_this
A LINE!
Wow! A line
A LINE!

# Use sort to delete all repeated non-consecutive lines
$> sort uniq_this | uniq
A LINE!
OH! LINE!
Wow! A line
```

## tree - 2021-05-05

$> tree [directory]

Display file and folders recursively in a tree.

[05/41]

```
# Output files and folder from working directory
$> tree
.
├── my_dir
│   └── my_sub_file
└── my_file

1 directory, 2 files

# Option `--noreport` hide the report at the end of the tree
$> tree --noreport
.
├── my_dir
│   └── my_sub_file
└── my_file

# Option `-a` to display `a`ll files (even hidden ones)
$> tree -a --noreport
.
├── my_dir
│   └── my_sub_file
├── my_file
└── .my_hidden_file

# Option `-d` list `d`irectory only
$> tree -d --noreport
.
└── my_dir

# Option `-L` allow you to precise how many directory levels to output
$> tree -L 1 --noreport
.
├── my_dir
└── my_file

# Option `-P` output only files matching a `p`attern. Use `-I` to `i`gnore files matching a pattern.
$> tree -P 'my_sub*' --noreport
.
└── my_dir
    └── my_sub_file

# Option `-u` print the `u`sername, `-g` the `g`roup name, `-h` the size in `h`uman readable format.
$> tree -ugh --noreport
.
├── [hypnos   wheel    4.0K]  my_dir
│   └── [hypnos   wheel       0]  my_sub_file
└── [hypnos   wheel       0]  my_file

# Option `-J` output some JSON
$> tree -J
[{"type":"directory","name": ".","contents":[
    {"type":"directory","name":"my_dir","contents":[
      {"type":"file","name":"my_sub_file"}
    ]},
    {"type":"file","name":"my_file"}
  ]},
  {"type":"report","directories":1,"files":2}
]
```

## wc - 2021-05-06

$> wc [input]

wc can output count of newlines, words, characters, and bytes of a given input. The input can be a file.

```
$> cat wc_word_count
This file™
Is meant→
To be counted!

# Count lines, words, and bytes respectively
$> wc wc_word_count
 3  7 40 wc_word_count

# Give multiple files as input
$> wc wc_word_count wc_word_count
 3  7 40 wc_word_count
 3  7 40 wc_word_count
 6 14 80 total

# Options `-c` print the byte `c`counts, `-m` the characters count, `-l` the `l`ines count, `-w` the `w`ords count
$> wc -m wc_word_count
36 wc_word_count
```

[06/41]

## tee - 2021-05-07

$> tee [file]...

Read from standard input and write to both standard output and file(s). This time, input can't be a file.

```
# Create and write to files and to standard output
$> echo "hello" | tee super_file mega_file
hello
$> cat super_file
hello
$> cat mega_file
hello

# Append to files
$> echo "bonjour" | tee -a super_file 
bonjour
$> cat super_file
hello
bonjour

# Redirect the output to another command and write it to a file 
$> cat super_file | tee super_output | wc -l
2
$> cat super_output
hello
bonjour
```

## jobs

## IDEAS

$> gimmeyourmoney > file-which-launch-at-startup
