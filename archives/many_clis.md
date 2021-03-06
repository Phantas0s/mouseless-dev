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

## alias - 2021-05-01

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

## man - 2021-05-02

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

## type - 2021-05-03

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

## sort - 2021-05-04

$> sort [input]

Sort lines of the input which can be a file. How surprising!

[04/31]

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

## uniq - 2021-05-05

$> uniq [input]

Report or omit consecutive and repeated lines.

[05/31]

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

# Options `-c` `c`ount number of occurences and add it as prefix
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

## wc 2021-05-06

$> wc [input]

wc (for `w`ord `c`ount) can output count of newlines, words, characters, and bytes of a given input. The input can be a file.

[06/31]

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

## tree 2021-05-07

$> tree [directory]

Output files and folders recursively as a tree.

[07/41]

```
# Output files & folder (from working directory if no path specified)
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

## tee 2021-05-08

$> tee [file]...

Read from standard input and write to both standard output and file(s). This time, input can't be a file.

[08/31]

```
# Create and write to files and to standard output
$> echo "hello" | tee super_file mega_file
hello
$> cat super_file
hello
$> cat mega_file
hello

# Option `-a` to `a`ppend to files
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

## cut - 2021-05-09

$> cut [file]...

Cut fields (sections) for each line of its input and output them. The input can be a file.

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
tab_delimiters_on_this_line      200     2020-20-20      field

# Cutting multiple fields
$> cut -f 1,3 -d ' ' cut_this
space_delimiters_on_this_line 2018-02-04
space_delimiters_on_this_line 2017-02-04
tab_delimiters_on_this_line      200     2020-20-20      field

# Cutting a range
$> cut -f 2-4 -d ' ' cut_this
800 2018-02-04 a_filed
400 2017-02-04 another_field
tab_delimiters_on_this_line      200     2020-20-20      field

# Cutting field and everything after it
$> cut -f 2- -d ' ' cut_this
800 2018-02-04 a_filed
400 2017-02-04 another_field
tab_delimiters_on_this_line      200     2020-20-20      field

# Changing the delimiter for the output
$> cut -f 1-3 -d ' ' --output-delimiter=',' cut_this
space_delimiters_on_this_line,800,2018-02-04
space_delimiters_on_this_line,400,2017-02-04
tab_delimiters_on_this_line      200     2020-20-20      field
```

## tr 2021-05-10

$> tr [string1] [string2] [input]

This CLI can `tr`anslate, delete, or squeeze characters. The string `string1` is always mandatory.

```
# Replace characters from string1 to string2. Both strings mandatory.
$> echo "hello" | tr "el" "yz"
hyzzo
$> echo "if()" | tr "()" "{}"
if{}

# Option `-d` to `d`elete characters 
$> echo "hello" | tr -d "l"
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


## ls 2021-05-11

$> ls [files]...

List information about the files given as argument (the working directory without argument).

```
# Option `--color` add colors to the output.
# I would recommend to create this alias: 
# alias ls="ls --color=auto"

# Output files, folders, and links in current directory
$> ls
file  my_dir  symlink

# Output files, folders, and links in directory "my_dir"
$> ls my_dir
01.md  02.md  03.txt  subdirectory

# Using glob to output every file with extension "md" in directory "my_dir"
$> ls my_dir/*.md
my_dir/01.md  my_dir/02.md

# Using globs "**" with Bash parse one directory level
$> ls **/*.md 
my_dir/01.md  my_dir/02.md

# Using globs "**" with Zsh parse every directory level
$> ls **/*.md
my_dir/01.md  my_dir/02.md  my_dir/subdirectory/01.md  my_dir/subdirectory/02.md

# Option `-a` to list `a`ll files and directories including hidden files (file beginning with dot "." or dotfile)
$> ls -a
.  ..  directory  file_1  .hidden  symlink

# Option `-l` use a `l`ong list format
$> ls -l
drwxr-xr-x 2 hypnos wheel 4096 May  4 20:17 directory
-rw-r--r-- 1 hypnos wheel    0 May  4 20:17 file_1
lrwxrwxrwx 1 hypnos wheel    6 May  4 20:17 symlink -> file_1

# Option `-h` display a `h`uman readable size with option `-l`. 
# Many CLIs have this `-h` option.
$> ls -lh
drwxr-xr-x 2 hypnos wheel 4.0K May  4 20:17 directory
-rw-r--r-- 1 hypnos wheel    0 May  4 20:17 file
lrwxrwxrwx 1 hypnos wheel    4 May  4 20:22 symlink -> file

# Option `-I` use a shell pattern to `i`gnore entries
$> ls -I "*.md" my_dir
03.txt  subdirectory

# Option `-t` sort by `t`ime (newest first)
$> ls -lt
drwxr-xr-x 3 hypnos wheel 4096 May  4 20:35 my_dir
lrwxrwxrwx 1 hypnos wheel    4 May  4 20:22 symlink -> file
-rw-r--r-- 1 hypnos wheel    0 May  4 20:17 file

# Option `-S` sort by file`s`ize (largest first)
$> ls -lSh
total 4.0K
drwxr-xr-x 3 hypnos wheel 4.0K May  4 20:35 my_dir
lrwxrwxrwx 1 hypnos wheel    4 May  4 20:22 symlink -> file
-rw-r--r-- 1 hypnos wheel    0 May  4 20:17 file
```

## mkdir 2021-05-12

$> mdkir [directory]

Create directory(ies) if they don't already exist.

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

## jobs - 2021-05-13

$> jobs [job_id]

Display status of processes started in the current shell session. Useful when there are processes in the background.

```
# Open okular (a PDF reader) and send it to the background (thanks to &)
$> okular my_pdf.pdf &
[1] 73514

# List jobs started in the current shell session
$> jobs
[1]  + running    okular my_pdf.pdf

# Stop the process (send the signal STOP)
$> killall -STOP okular
$> jobs
[1]  + suspended (signal)  okular my_pdf.pdf

# Continue the process (send the signal CONT)
$> killall -CONT okular
$> jobs
[1]  + running    okular my_pdf.pdf

# Send the process to the foreground
$> fg %okular
[1]  + continued  okular my_pdf.pdf

# Use CTRL+z to suspend and send the process to the background again
^Z
zsh: suspended  okular my_pdf.pfg
$> jobs
[1]  + suspended (signal)  okular my_pdf.pdf

# Allowing a process to continue running in the background with bg
$> vim
^Z
zsh: suspended vim
$> jobs
[1]  + suspended  okular my_pdf.pdf
[2]  - suspended  vim
$> bg %vim
[2]  - continued  vim
[2]  + suspended (tty output)  vim
```

## touch - 2021-05-14

$> touch [file...]

Change the file access and modification time of each file. Create the files if they don't exist.

```
# Create a file
$> touch super_file
$> ls -l
-rw-r--r-- 1 hypnos wheel 0 May  1 17:54 super_file

# Modify the file's timestamp
$> touch super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:55 super_file

# Option `-f` to change the `d`ate of the file
$> touch -d "2021-04-30" super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 Apr 30 18:54 super_file

# Option `-a` to change the `a`ccess time and change time
$> touch -a super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 Apr 30 18:54 super_file
$> ls -lu super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:59 super_file

# Option `-m` to change the `m`odification time and change time
$> touch -m super_file
$> ls -l super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 18:01 super_file
$> ls -lu super_file
-rw-r--r-- 1 hypnos wheel 0 May  1 17:59 super_file
```

## nl - 2021-05-15

$> nl [input]

`n`umber `l`ines of a given input. As often, the input can be a file.

[02/31]

```
$> cat nl_number_line
This is a line

Great Scott! This is a line too

# Number lines
$> nl nl_number_line
     1  This is a line

     2  Great Scott! This is a line too

# Option `-b` (`b`ody numbering) to add a style

## Style `a` to number `a`ll lines 
$> nl -b a nl_number_line
     1  This is a line
     2
     3  Great Scott! This is a line too

## Style `p` to number lines only when it matches a `p`attern (regex)
$> nl -b p'Scott.*' nl_number_line
       This is a line

     1  Great Scott! This is a line too

# Option `-i` to `i`ncrement lines with a count
$> nl -i 10 -b a nl_number_line
     1  This is a line
    11
    21  Great Scott! This is a line too
```

## bc - 2021-05-16

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

## ln - 2021-05-17

$> ln [TARGET] [LINK_NAME]

Create a link to [TARGET] with name [LINK_NAME]

$> ln [TARGET]... [DIRECTORY]

Create links to [TARGET] in [DIRECTORY]

```
$> ls
dir  file  other_file

# Option `-s` create a symbolic link (symlink)
$> ln -s dir symlink_dir
$> ls -l
drwxr-xr-x 2 hypnos wheel 4096 May 12 20:32 dir
drwxr-xr-x 2 hypnos wheel 4096 May 12 20:32 file
-rw-r--r-- 1 hypnos wheel    0 May 12 20:33 other_file
lrwxrwxrwx 1 hypnos wheel    3 May 12 20:32 symlink_dir -> dir

# Option `-f` remove existing destination files
$> ln -s file other_file
ln: failed to create symbolic link 'other_file': File exists

$> ln -sf file other_file
$> ls -l
drwxr-xr-x 2 hypnos wheel 4096 May 12 20:32 dir
-rw-r--r-- 1 hypnos wheel    0 May 12 20:36 file
lrwxrwxrwx 1 hypnos wheel    4 May 12 20:37 other_file -> file
lrwxrwxrwx 1 hypnos wheel    3 May 12 20:32 symlink_dir -> dir

# Option `-i` for `i`nteractive: to prompt whether to remove  the destinations
$> ln -si file other_file
ln: replace 'other_file'? y

# Give a directory as second argument to create a link in that directory
# Option `-t` for the same result
$> ln -s $HOME dir
$> ls -l dir
lrwxrwxrwx 1 hypnos wheel 12 May 12 20:46 hypnos -> /home/hypnos

$> ln -s -t dir $HOME/tests
$> ls -l dir
total 0
lrwxrwxrwx 1 hypnos wheel 12 May 12 20:46 hypnos -> /home/hypnos
lrwxrwxrwx 1 hypnos wheel 18 May 12 20:48 tests -> /home/hypnos/tests
```

## tail - 2020-05-18

$> tail [file]...

Print the last 10 lines of the input (can also be files).

```
$> tail tail_that
Line 5
Line 6
Line 7
Line 8
Line 9
Line 10
Line 11
Line 12
Line 13
Line 14

# Option `-n` display the `n` last `n`umber of lines
$> tail -n 3 tail_that
Line 12
Line 13
Line 14

# You can give multiple files as input
$> tail -n 2 nl_number_line tail_that
==> nl_number_line <==

Great Scott! This is a line too

==> tail_that <==
Line 13
Line 14

# Option `-q` makes tail `q`uiet
# The filenames won't be displayed
$> tail -q -n 2 nl_number_line tail_that

Great Scott! This is a line too
Line 13
Line 14

# Option `-n` with a `+` display all file since a precise line
$> tail -n +12 tail_that
Line 12
Line 13
Line 14

# Option `-f` output added lines as they're written by another process
# Use the file descriptor by default (so the file can be renamed without problem)
# Very useful for log files

$> tail -f -n 3 tail_that
Line 12
Line 13
Line 14
this line is written by another process

# Option `-F` output added lines as they're written by another process
# Use the file name by default (so a log file is still read even if it's rotated)

# Option `--retry` keep trying to open a file if it's inaccessible
# Useful when a log file doesn't exists yet

# Option `--pid=PID` terminates tail when the process with the specified PID ends

# If you use option `-f` with multiple files, using `-q` won't display the name of the file read
```

## stat - 2020-05-19

$> stat [file]...

Display file status

[19/31]

```
$> stat stat_me
  File: stat_me
  Size: 8               Blocks: 8          IO Block: 4096   regular file
Device: 804h/2052d      Inode: 10892061    Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/  hypnos)   Gid: (  998/   wheel)
Access: 2021-05-17 19:22:14.352929595 +0200
Modify: 2021-05-17 19:22:29.242961250 +0200
Change: 2021-05-17 19:22:29.242961250 +0200
Birth: 2021-05-17 19:22:14.352929595 +0200

# Option `-c` output data your want

## Output owner name, owner id, group owner name, group owner id 
$> stat -c "%U %u %G %g" stat_me
hypnos wheel 1000 998

## Output permissions in octal and human format
$> stat -c "%a %A" stat_me
644 -rw-r--r--
```

## pwd - 2021-05-20

$> pwd

`p`rint the name of the `w`orking `d`irectory

```
$> pwd
/home/hypnos/tests

# Option `-P` display the target if the working directory is a symlink
$> pwd -P
/home/hypnos/Documents/projects/mouseless/blog/many_clis/tests

# If you change the working directory in your a shell script, use $PWD
$> echo $PWD
/home/hypnos/tests
```

## grep - 2020-05-21

$> grep [pattern] [input]

Search for pattern in the input. The input can be one or more files.

```
$> cat grep_that
This text will be filtered
With the nice grep tool
GLORY to grep!

# Output the lines matching the pattern
$> grep "tool" grep_that
With the nice grep tool

# Option `-v` output the lines NOT matching the pattern (in`v`ert the match)
$> grep -v "tool" grep_that
This text will be filtered
GLORY to grep!

# Option `-o` output the pattern matched `o`nly, not the entire line
$> grep -o "tool" grep_that
tool

# Option `-i` for pattern with case `i`sensitive
$> grep -i "glory" grep_that
GLORY to grep!

# Option `-n` output the line `n`umbers
$> grep -n "tool" grep_that 
2:With the nice grep tool

# Option `-h` always `h`ide filename
# Option `-H` always output filename
$> grep -H "tool" grep_that
grep_that:With the nice grep tool

# Option `-l` on`l`y output fi`l`ename
$> grep -l "tool" grep_that another_file yet_another_file
grep_that

# Option `-L` on`l`y output fi`l`ename without match
$> grep -L "tool" grep_that another_file yet_another_file
another_file
yet_another_file

# Option `-A n`, `-B n`, `-C n` output n lines `a`fter, `b`efore, or both, depending on the matched `c`ontext
$> grep -A 1 "tool" grep_that 
With the nice grep tool
GLORY to grep!

$> grep -B 1 "tool" grep_that 
This text will be filtered
With the nice grep tool

$> grep -C 1 "tool" grep_that 
This text will be filtered
With the nice grep tool
GLORY to grep!

# Useful grep alias
alias grep="grep --color=auto"
```

## sed - 2020-05-22

$> sed [script] [input]...

Tool `ed`iting a `s`tream of content. Said differently, it transforms the input and produce an output.

A sed script is structured as follow: [address][command][option]
```
$> cat sed_file
This file will be edited with sed
What a fantastic command-line editor!
See https://themouseless.dev/posts/sed-guide-example-mouseless/

# An address (`1`) to select a line
# A command (`d`) to delete this line
$> sed '1d' sed_file
What a fantastic command-line editor!
See https://themouseless.dev/posts/sed-guide-example-mouseless/

# Address selecting lines according to a regular expression
$> sed '/command-line/d' sed_file
This file will be edited with sed
See https://themouseless.dev/posts/sed-guide-example-mouseless/

# Address as a range
$> sed '2,3d' sed_file
This file will be edited with sed

# Option `-e` `e`xecute another script
$> sed -e '1d' -e '3d' sed_file
What a fantastic command-line editor!

# Command `s` substitute a pattern with a replacement
$> sed -e 's/file/stream/' -e '2,3d' sed_file
This stream will be edited with sed

## Use `&` to reuse pattern in replacement
$> sed -e 's/file/&-stream/' -e '2,3d' sed_file
This file-stream will be edited with sed

## You can change the separator
$> sed -e 's#themouseless.dev/posts/sed-guide-example-mouseless/#gnu.org/software/sed/manual/sed.html#' -e '1,2d' sed_file
See https://gnu.org/software/sed/manual/sed.html

# Option `-i` write file `i`n place
$> sed -i 's/file/stream/' sed_file
$> cat sed_file
This stream will be edited with sed
What a fantastic command-line editor!
See https://themouseless.dev/posts/sed-guide-example-mouseless/

# Command `p` `p`rint lines a second time, option `-n` sile`n`t the output expect for line `p`rinted
$> sed -n `1p` sed_file
This file will be edited with sed
```

## find 2020-05-23

$> find [starting_point] [expression]...

Search a directory tree from a starting point (the working directory if not precised). Expressions can filter or act on the results.

```
$> find .
.
./a_dir
./a_dir/a_file
./a_file
./a_symlink

# Expression `-name` filter by name
$> find . -name "a_file"
./a_dir/a_file
./a_file

# Expression `-wholename` filter by filepath
$> find . -wholename "./a_dir/a_file"
./a_dir/a_file

# Expression `-type` filter by filetype
# `d` for `d`irectory
# `f` for regular `f`ile
# `l` for symlink
$> find . -type f
./a_dir/another_file
./a_file

# Expression `-regex` filter with a `reg`ular `ex`pression

## Without the expression `-regextype`, use Emacs Regular Expression 
$> find . -regex "*.file"
./a_dir/a_file
./a_file

## With the expression `-regextype` BEFORE `-refex`, can specify another regex engine
$> find . -regextype help
find: Unknown regular expression type ‘help’; valid types are ‘findutils-default’, ‘ed’, ‘emacs’, ‘gnu-awk’, ‘grep’, ‘posix-awk’, ‘awk’, ‘posix-basic’, ‘posix-egrep’, ‘egrep’, ‘posix-extended’, ‘posix-minimal-basic’, ‘sed’.

$> find . -regextype egrep -regex "*.(file|dir)"
find . -regextype egrep -regex ".*(file|dir)"

# Expression `-perm` filter with exact permissions

## Octal form
$> find . -perm 644
./a_dir/a_file
./find
./a_file

## Symbolic form
$> find . -perm u=r,g=r

# Expression `-size` filter by `size`
# Can be used to find files in a size range
$> find . -size +1k -size -10M
.
./a_dir

# Expression `-exec` `exec`ute a command on each file one by one
# The '{}' is replaced by the current file
# For example, one of the command executed here will be "wc -l ./a_file"
$> find . -type f -exec wc -l {} ';'
0 ./a_dir/a_file
0 ./find
2 ./a_file
```

## who - 2021-05-24

$> who

Show what users are logged on, and what tty or pts (pseudo terminal) they're running.

```
$> who
hypnos   tty1         2021-05-24 06:45
hypnos   pts/2        2021-05-24 06:45 (tmux(1449).%0)
hypnos   pts/3        2021-05-24 06:45 (tmux(1449).%1)
hypnos   pts/4        2021-05-24 06:45 (tmux(1449).%2)
hypnos   pts/8        2021-05-24 06:47 (tmux(1449).%6)
...

# Option `-b` for time of last system `b`oot
$> who -b
system boot  2021-05-24 06:44
```

## id - 2021-05-25

$> id [user]...

Print information about a user (or the current user if the argument [user] is not specified).

```
$> id
uid=1000(hypnos) gid=998(wheel) groups=998(wheel),974(docker)

# Option `-g` to print `g`roup ID
$> id -g
998

# Option `-G` to print all `g`roup IDs
$> id -G
998 974

# Option `-u` only print the `u`ser ID
$> id -u
1000

# Option `-n` needs to be used with one of the options above 
# Print the `n`ame of user or group
$> id -un
hypnos

$> id -Gn
wheel docker
```

## whereis - 2021-05-26

$> whereis name...

Locate the binary, source, and manual page for one or more commands.

```
$> whereis ls
ls: /usr/bin/ls /usr/share/man/man1/ls.1p.gz /usr/share/man/man1/ls.1.gz

# Option `-b` only output the `b`inary
$> whereis -b ls
ls: /usr/bin/ls

# Option `-m` only output the `m`anual page
$> whereis -m ls
ls: /usr/share/man/man1/ls.1p.gz /usr/share/man/man1/ls.1.gz

# Option `-l` `l`ist the paths whereis is searching
$> whereis -l
bin: /usr/bin
bin: /usr/lib
bin: /etc
...
man: /usr/share/info

# Useful when a built-in command (or reserved shell word) has a binary with same name.
# In this example there is two time: a reserved word and GNU time (/usr/bin/time)
$> type time
time is a reserved word
$> whereis -b time
time: /usr/bin/time
```

## time - 2021-05-27

$> time command [arguments]...

Run the command with arguments. When the command terminate, output real time and CPU time.

```
$> time find "~/Documents"
/home/hypnos/Documents
...
/home/hypnos/Documents/cooking/mom_recipe.pdf
find ~/Documents  0.04s user 0.09s system 94% cpu 0.131 total

# Using GNU time

$> type time
time is a reserved word

$> whereis time
time: /usr/bin/time

## Option `-f` decides of the output `f`ormat
## %E - Elapsed real time (%e to have it in seconds)
## %M - Maximum memory used in Kbytes
## %K - Average total memory used in Kbytes
$> /usr/bin/time -f "Time: %E - Memory: %M - Avg Memory: %K" find ~/Documents
/home/hypnos/Documents
...
/home/hypnos/Documents/cooking/mom_recipe.pdf
Time: 0:00.78 - Memory: 3248 - Avg Memory: 0

## Option `-v` to have a `v`erbose output about everything time knows about
```

## xargs - 2021-05-28

$> xargs [options] [command] [initial-arguments]... [input]

Read whitespace-separated inputs and use it as arguments to a command. The input can't be a file.

```
$> ls *.sh
script_1.sh  script_2.sh  script_3.sh

# Give the three scripts as argument to wc -l (count the lines)
# Equivalent to "wc -l script_1.sh script_2.sh script_3.sh"
$> ls *.sh | xargs wc -l
   9 script_1.sh
  11 script_2.sh
  28 script_3.sh
  48 total

# Option `-0` use the ASCII character null as delimiter instead of whitespace
# It should always be used with files to handle filenames with spaces correctly
# The find argument `-print0` use the ASCII null character as delimiter for its output
$> find . -name "*.sh" -print0 | xargs -0 wc -l
  28 ./script_3.sh
  11 ./script_2.sh
   9 ./script_1.sh
  48 total

# Options `-a` re`a`d arguments from a file instead of input
$> cat list_files
./script_1.sh
./script_2.sh
./script_3.sh

$> xargs -a list_files wc -l 
   9 ./script_1.sh
  11 ./script_2.sh
  28 ./script_3.sh
  48 total

# Option `-I` creates a placeholder for the input passed to xargs
# In that case, the command runs for each argument
# Needs to add `-n` for it to work (or the command will only run once...)
$> find . -name "*.sh" -print0 | xargs -0 -I % grep % ./list_files
./script_3.sh
./script_2.sh
./script_1.sh

# Option `-n` decides number of argument given to the command
# In this example, equivalent to "wc -l script_1.sh && wc -l script_2.sh && wc -l script_3.sh"
$> find . -name "*.sh" -print0 | xargs -0 -n 1 wc -l
28 ./script_3.sh
11 ./script_2.sh
9 ./script_1.sh

# Option `-P` determine how many processes will run at a time (for concurrent work)
# Value of 0 create as much processes as possible
# Useful for tasks asking for a lot of resources


# You can use the command "bash -c" to chain commands with xargs
$> find . -name "*.sh" -print0 | xargs -0 -I $ bash -c "grep $ ./list_files && echo 'current script: $'"
./script_3.sh
current script: ./script_3.sh
./script_2.sh
current script: ./script_2.sh
./script_1.sh
current script: ./script_1.sh
```

## seq - 2021-05-29

$> seq [FIRST] [INCREMENT] LAST

Output a sequence of number using an increment

```
# By default, [FIRST] is 1 and [INCREMENT] is 1
$> seq 4
1
2
3
4

# Set INCREMENT to 2
$> seq 1 2 4
1
3

# Option `-s` to define a separator (default is newline) 
$> seq -s "," 4
1,2,3,4

# Option `-f` output with printf style `f`ormat

## Specify number of digits
$> seq -f "%.4f" 4
1.0000
2.0000
3.0000
4.0000

$> seq -f "%.4f" 1.2 0.5589 4.3
1.2000
1.7589
2.3178
2.8767
3.4356
3.9945

## Padding with 0
$> seq -f "%04g" 4
0001
0002
0003
0004
```

## diff - 2021-05-30

$> diff [files]...

Compare multiple files line by line

```
$> cat file_1
This is a file
With many differences
To test diff

$> cat file_2
This is a file
With SO many differences
To test        diff

$> diff file_1 file_2
< With many differences
< To test diff
---
> With SO many differences
> To test        diff

# Option `-y` show the difference side by side
$> diff -y file_1 file_2
This is a file                          This is a file
With many differences                 | With SO many differences
To test diff                          | To test        diff

# Options `-w` doesn't compare `w`histespaces
$> diff -w file_1 file_2
2c2
< With many differences
---
> With SO many differences

# Option `-u` for `u`nified format (format used by git diff)
$> diff -u file_1 file_2
--- file_1      2021-05-28 13:49:53.277345044 +0200
+++ file_2      2021-05-28 13:50:01.094019947 +0200
@@ -1,3 +1,3 @@
 This is a file
-With many differences
-To test diff
+With SO many differences
+To test        diff

# Option `-r` compare two directories recursively
$> ls dir_1
file_1  file_2  file_3
$> ls dir_2
file_1  file_2 file_4
$> diff -r dir_1 dir_2
Only in dir_1: file_3
Only in dir_2: file_4

# Option `-x` e`x`clude filenames that match a pattern
$> diff -r -x "file_3" dir_1 dir_2
Only in dir_2: file_4

# Option `-i` compare files and `i`gnore case
```

## split - 2021-05-31

$> split [file] [prefix]

Split a file into multiple files named [prefix]aa, [prefix]ab... Default [prefix] is "x"

```
# Option `-l` put precise count of `l`ines in each split
$> ls
file_1
$> wc -l file_1
5
$> split -l 2 file_1
$> ls
file_1  xaa  xab  xac
$> wc -l file_1 xaa xab xac 
  5 file_1
  2 xaa
  2 xab
  1 xac
 10 total

# Option `-n` create a precise `n`umber of files of same size
$> split -n 3 file_1
$> ls -lh
-rw-r--r-- 1 hypnos wheel 75 May 29 11:03 file_1
-rw-r--r-- 1 hypnos wheel 25 May 29 11:05 xaa
-rw-r--r-- 1 hypnos wheel 25 May 29 11:05 xab
-rw-r--r-- 1 hypnos wheel 25 May 29 11:05 xac
$> rm -f xaa xab xac

# Option `-b` create files with a precise `b`ytes size
# Use `k` for `k`ilobyte or `m` for `m`egabyte
$> split -b 20 file_1
$> ls -lh
-rw-r--r--  1 hypnos wheel   75 May 29 11:03 file_1
-rw-r--r--  1 hypnos wheel   20 May 29 11:18 xaa
-rw-r--r--  1 hypnos wheel   20 May 29 11:18 xab
-rw-r--r--  1 hypnos wheel   20 May 29 11:18 xac
-rw-r--r--  1 hypnos wheel   15 May 29 11:18 xad
$> rm -f xaa xab xac xad

# Option `-a` generate suffixes of nth length
$> split -a 5 -l 2 file_1
$> ls
file_1  xaaaaa  xaaaab  xaaaac
$> rm xaaaaa xaaaab xaaaac

# Option `--filter` write to a shell command
# Current filename is $FILE
$> rm -f xaa xab xac
$> split -l 3 --filter "wc -l" file_1 
1
2
2
$> ls
file_1
```

## IDEAS

* free, pgrep
* [xargs](http://widgetsandshit.com/teddziuba/2010/10/taco-bell-programming.html)

* $> gimmeyourmoney > file-which-launch-at-startup
