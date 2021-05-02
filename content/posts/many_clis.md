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
⏰ It's CLI time.

In May, I'll give you a GNU command-line tool each day. I'll try to make it neat for every living soul. I like hard mode, even when I lose.

Help me carving history with some shell madness. Gimme your tricks for each CLI to spread love in the Universe. 💕🧵

```
system> Arch Linux
shell> Zsh
commands> mostly GNU

advice> If you have a BSD command, install the GNU version
```

## alias 

$> alias [name]="[command]"

An alias can represent a particular command, often used for:

- Defining a short name for a long, complicated command
- Always running a command with specific options

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

# Use a backslash "\" to ignore the alias
# Delete a file without confirmation
$> \rm super_file_3
```

## type 

$> type [command]

Write a description of the command's type.

```
# Every type of command

## Built in command
$> type cd
cd is a shell builtin

## External command
$> type bash
bash is /usr/bin/bash

## Alias
$> alias ll="ls -lah"
$> type ll
ll is an alias for ls -lah

## Shell function
$> type imgtowebp
imgtowebp is a shell function from /home/hypnos/.dotfiles/zsh/scripts.zsh

## Shell reserved word
$> type while
while is a shell keyword
```

## nl

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

## sort

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

## uniq - AFTER SORT

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

## tree

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

## wc

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

## tee

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

## mkdir (After TREE)

$> mdkir [directory]

Create directory(ies) if they don't already exist

```
# Create one directory maximum, throw an error if directory exists
$> mkdir my_dir

# Option `-p` to create parents as needed. Doesn't throw an error if directories exist
$> mkdir -p my_dir/my_other_dir
$> mkdir -p more_dirs/level_1/{level_2_a/{level_3_a,level_3_b},level_2_b}
$> tree
├── more_dirs
│   └── level_1
│       ├── level_2_a
│       │   ├── level_3_a
│       │   └── level_3_b
│       └── level_2_b
└── my_dir
    └── my_other_dir

# Option `-m` to set the file mode (permissions).
$> mkdir -m 777 free_for_all
$> ls -og
total 12
drwxrwxrwx 2 4096 Apr 25 17:22 free_for_all
drwxr-xr-x 3 4096 Apr 25 17:13 more_dirs
drwxr-xr-x 3 4096 Apr 25 17:13 my_dir

8 directories, 0 files
```

## bc

$> bc [input]

A `b`asic `c`alculator in your wonderful shell.

```
# Give inputs to bc
$> bc
2+2
4
. + 2
6

# Variable `scale` to control... the `scale`
$> echo "scale=10; 1.2345 * 2.2345" | bc
2.75849025
$> echo "scale=3; 1.2345 * 2.2345" | bc
2.7584

# Option `-l` initialize the scale to 20
$> echo "2/3" | bc
0
$> echo "2/3" | bc -l
.66666666666666666666

## Variable `ibase` to set the `i`nput `base` system
# Hexadecimal conversion
$> echo 'ibase=16; FFFFFF' | bc
16777215
$> 

## Variable `obase` to set the `o`utput `base` system
$> echo 'obase=16; ibase=16; 000FFF+FFF000' | bc
FFFFFF
$> echo 'obase=2; ibase=2; 1 + 1' | bc
10
```

## cut

$> cut [file]...

This CLI can `cut` fields (sections) of each lines of a file and output them. The default section delimiter is TAB (not SPACE).

```
$> cat cut_this
space_delimiters_on_this_line 800 2018-02-04 a_filed
space_delimiters_on_this_line 400 2017-02-04 another_field
tab_delimiters_on_this_line      200     2020-20-20      field

# Only one of these option is mandatory: `-b`, `-c`, or the most used `-f`

# Option `-f` to cut precise `f`ields
$> cut -f 2 cut_this
space_delimiters_on_this_line 800 2018-02-04 a_filed
space_delimiters_on_this_line 400 2017-02-04 another_field
200

# Option `-d` to give another delimiter than the default TAB. It must be a single character.
$> cut -f 2 -d ' ' cut_this
800
400

# Cutting multiple fields
$> cut -f 1,3 -d ' ' cut_this
space_delimiters_on_this_line 2018-02-04
space_delimiters_on_this_line 2017-02-04

# Cutting a range
$> cut -f 2-4 -d ' ' cut_this
800 2018-02-04 a_filed
400 2017-02-04 another_field

# Cutting field and everything after it
$> cut -f 2- -d ' ' cut_this
800 2018-02-04 a_filed
400 2017-02-04 another_field

# Changing the delimiter for the output
$> cut -f 1-3 -d ' ' --output-delimiter=',' cut_this
space_delimiters_on_this_line,800,2018-02-04
space_delimiters_on_this_line,400,2017-02-04
```

## tr

$> tr [string1] [string2] [input]

This CLI can `tr`anslate, delete, or squeeze characters. The string `string1` is always mandatory.

```
# Replace characters from string1 to string2. Both strings mandatory.
$> echo "hello" | tr "el" "yz"
hyzzo
$> echo "if()" | tr "()" "{}"
if{}

# Option `-d` to `d`elete characters 
$> echo "hello" | tr "l"
heo

# Option -s to squeeze repeated characters
$> echo "hello        how are        you?" | tr -s " "
hello how are you?

# Using sets of characters
$> echo "hello what's up" | tr "[a-z]" "[A-Z]"
HELLO WHAT'S UP
$> echo "hello what's up" | tr "[ol2e]" "[0-3]"
h3110 what's up

```

## jobs

$> jobs [job_id]

Display status of jobs started in the current shell session. Useful when there are processes in the background.

```
# Open okular (a PDF reader) and send it to the foreground with '&'
$> okular my_pdf.pdf &
[1] 73514

# List jobs started in current shell session
$> jobs
[1]  + running    okular my_pdf.pdf

# Stop the process (send the signal STOP)
$> killall -STOP okular
$> jobs
[1]  + suspended (signal)  okular my_pdf.pdf

# Continue the process (send the signal CONT)
$> killall -CONT okular
$> jobs
[1]  + running    okular 2020_lohnsteuerbescheinigung.pdf

# Send the process to the foreground
$> fg %okular
[1]  + continued  okular 2020_lohnsteuerbescheinigung.pdf

# Use CTRL+z to suspend and send the process from the foreground to the background
^Z
zsh: suspended  okular my_pdf.pfg
$> jobs
[1]  + suspended (signal)  okular my_pdf.pdf

# Allow a process continue running in the background with bg
$> vim
zsh: suspended vim
$> jobs
[1]  + suspended  okular my_pdf.pdf
[2]  - suspended  \vim
$> bg %vim
[2]  - continued  \vim
[2]  + suspended (tty output)  \vim
$> jobs
[1]  - suspended  okular 2020_lohnsteuerbescheinigung.pdf
[2]  + suspended (tty output)  \vim
```

## man (after less?)

$> man [section] [page]

Display the system manual using a pager. The argument [page] is mandatory.

```
# Open the manual page for the command ls
$> man ls

# Open the manual page for the command man
$> man man

# Option `-f` output a short description
# Equivalent to the command "whatis"
$> man -f cd
cd (1p)              - change the working directory

# Option `-k` uses a regex to search in short descriptions
# Equivalent to the command "apropos"
$> man -k "^ls"
ls (1)               - list directory contents
ls (1p)              - list directory contents
lsattr (1)           - list file attributes on a Linux second extended file system
lsb_release (1)      - manual page for FSG lsb_release v1.4
lsblk (8)            - list block devices
lscpu (1)            - display information about the CPU architecture
...

# Option `-H` displays the man page in a browser
$> man -Hfirefox ls

# Section 1 for executable programs or shell commands
$> man 1 cd

# Section 4 for special files (usually in the directory /dev)
$> man 4 pts

# Section 5 for configuration
$> man 5 ssh_config

# Section 8 for system administration commands
$> man 8 mount

# Different sections in order will be tried if non is precised
# By default on Linux: 1 n l 8 3 0 2 5 4 9 6 7
# See "/etc/man_db.conf"
$> man ssh_config

# Displaying man in Vim
# Option `-R` sets option `r`ead only
# Option `-M` sets option not `m`odifiable
# Option `+` runs an Ex command at startup
$> man man | vim -MR +"set filetype=man"
```

## ls

...

## touch (After ls)

$> touch [file...]

Change the file access and modification time of each file. Create the files if they don't exist.

```
# Create a file
$> touch super_file
$> ls -l
-rw-r--r-- 1 hypnos wheel 0 May  1 17:54 super_file

# Modify the file's timestamp (done one minute after creating the file)
$> touch super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:55 super_file

# Option `-f` to change the `d`ate of the file
$> touch -d "2021-04-30" super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 Apr 30 18:54 super_file

# Option `-a` to change the `a`ccess and change time
$> touch -a super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 Apr 30 18:54 super_file
$> ls -lu super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:59 super_file

# Option `-m` to change the `m`odification and change time
$> touch -m super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 18:01 super_file
$> ls -lu super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:59 super_file

```

# Option 

## IDEAS

* grep, sed, tr
* ps, kill, pwd, tail, stat

* $> gimmeyourmoney > file-which-launch-at-startup
