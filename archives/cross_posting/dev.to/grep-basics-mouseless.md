---
title: The Basics of grep - Filtering All The Content You Want
published: false
description: The basics of grep : output line numbers, filenames, number of matches, or even inverting matches... and more!
tags: #mouseless #commandline #productivity
cover_image: https://themouseless.dev/images/2021/grep/grep.jpg
canonical_url: https://themouseless.dev/posts/grep-basics-mouseless/
------

The tool grep is a fantastic CLI (Command-Line Interface) allowing you to filter any *pattern* for a given *input*, including a file.

To know if you have GNU grep, you can run in your terminal `grep --version`. If you don't have GNU grep, I would recommend installing it.

This article is a summary of the following video I made. I would recommend you to watch it first, it's more complete with many examples and exercises:

{% youtube SEkdPKcws3s %}

If you find the videos I have on this channel helpful, don't hesitate to subscribe and to like them. It would then appear to other Youtubers to help even more!

I also recommend you to: 

1. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/01_grep)
2. Fire up your beautiful shell
3. Try each command you'll see in this article

You won't learn much if you only read (or watch a video) passively.

In this article we'll see together how to:

* Colorize grep output
* Case insensitive patters
* Output lines with pattern matched
* Output lines with pattern not matched
* Output only the pattern matched
* Output lines numbers
* Output the match count
* Hiding filenames
* Always output filenames
* Only output filenames
* Only output filenames without matches
* Output lines after the match
* Output lines before the match
* Output lines before and after the match
* Excluding files
* Piping grep
* Summary: a mindmap of grep
* Using grep in practice

## Colors!

I would advise you to colorize the output of grep using the option `--color` as following: 

```bash
grep --color=auto
```

But it's a pain to use this option each time you use grep. You can define an alias to make grep always colorful:

```bash
alias grep="grep --color=auto"
```

It's now a firework machine.

## Output Lines With Pattern Matched

Here's the general way to use grep:

```bash
grep "<pattern>" <input>
```

The `<pattern>` is a regular expression and the `<input>` can be what you enter in your terminal or a file. For example:

```bash
grep "package" pacman.log
```

You'll see this output:

![Displaying lines with pattern matched](https://themouseless.dev/images/2021/grep/01_displaying_lines_pattern_matched.webp)

You can see that the pattern `package` is part of these two lines; that's why they are output. Incredible! It would be nice if we could do that in our daily life. I already know what words I would filter.

Note that the pattern wouldn't be colorized without the option `--color` set to `auto` as we saw above.

## Output Lines With Pattern Not Matched

If you want to in`v`ert the match, that is, only output the lines which *don't match* the pattern, you can use the option `-v` as follows:

```bash
grep -v "package" pacman.log
```

This time, you'll output all the file except the two lines containing the pattern `package`.

## Output Only The Pattern Matched

It can be useful to `o`nly output the match and not the whole line. You can do that with the option `-o`

```bash
grep -o "package" pacman.log
```

## Output Line Numbers

If you to output the lines and the line `n`umbers, you need to use the option `-n`. For example:

```bash
grep -n "package" pacman.log
```

The line numbers will be prefixed to each line of the output.

## Only Output Match Count

You can also *only* output the match `c`ount with the option `-c`:

```bash
grep -c "package" pacman.log
```

## Case Insensitive Patterns

By default, to match a pattern, you'll need to know what letters of the pattern are uppercase or lowercase. You can match your pattern without worrying about the case if you use the option `-i`:

```bash
grep -i "pacman" *.log
```

You can see that grep can filter more than one file. The glob `*` has a special meaning: it represents 0 or more character. Said differently, we want every file in our working directory ending up with `.log`. The following is equivalent:

```bash
grep -i "pacman" haskell.log pacman.log
```

## Filenames

### Hiding Filenames

As you saw, grep will output the filenames of the occurrence matches if you use more than one file as input. To `h`ide them, you can use the option `-h`. An example:

```bash
grep -h "package" *.log
```

### Always Output Filenames

If you want to output the filename where there's a match, even if you gave only one file as input, you can use the option `-H`. As usual, here's an example you can run in your terminal:

```bash
grep -H "package" pacman.log
```

For some option, the uppercase variant will do the inverse of the lowercase one, like `-h` and `-H`.

### Only Output Filenames

To only output the filenames where the patterns match, you can use the option `-l`:

```bash
grep -l "package" *.log
```

### Only Output Filenames Without Matches

You might have guessed it: the option `-L` only output the filename where the pattern *doesn't* match:

```bash
grep -L "package" *.log
```

## Output Lines Around The Match

Outputing the lines before or after your match can be useful to get a bit of *context* and understand the result of your filtering.

### Output Lines After The Match

To output a variable number of lines `A`fter the line with a match, you can use the option `-A`:

```bash
grep -A 3 "package" pacman.log
```

### Output Lines Before The Match

Similarly, if you want to output the lines `B`efore the match, you can use the option `-B`:

```bash
grep -B 3 "package" pacman.log
```

### Output Lines Before And After the Match

You can even output some lines before *and* after the match with the option `-C`:

```bash
grep -C 3 "package" pacman.log
```

In this example, the second match of `package` won't output the 3 lines after because it's the end of the file after the line containing the match.

Did you see the pattern? `A`fter, `B`efore, `C`ontext: it's `ABC`!

## Excluding Files

We always gave the files we wanted to filter to grep's input, but what if we want everything *except* a couple of files? For that, we can use the option `--exclude`:

```bash
grep "package" --exclude="pacman.log" *.log
```

## Piping grep

You can directly give the output of a command to the input of grep if you want to filter it. Actually, you can do that with every CLI accepting an input using pipes. 

For example, if I run `ps` (a CLI to output the processes running on my machine), I get this:

```
    PID TTY          TIME CMD
   2026 pts/9    00:11:36 nvim
   2431 pts/11   00:01:11 hugo
   2584 pts/10   00:00:01 tmuxp
   3009 pts/18   00:00:01 nvim
   3241 pts/20   00:00:17 taskell
   3484 pts/22   00:00:11 taskell
   3663 pts/21   00:00:00 nvim
```

What if I want only the lines containing the pattern `nvim`? I can do the following:

```bash
ps -a | grep "nvim"
```

It means that I give to the input of grep the output of the command `ps -a`. Here's the result:

```
   2026 pts/9    00:11:36 nvim
   3009 pts/18   00:00:01 nvim
   3663 pts/21   00:00:00 nvim
```

Pipes are one of the reason why the command line is so powerful: you can pipe small CLIs doing one thing and you'll have crazy results!

## In a Nutshell: a Mindmap of grep

[![summary how to use grep in mind map](https://themouseless.dev/images/2021/grep/grep.jpg)](https://themouseless.dev/images/2021/grep/grep.jpg)

It's nice to learn all of that, but how can you use grep in real life? Here's a video showing you a problem I had and I solved nicely using grep and other CLIs:

{% youtube N2YsWcyEpr8 %}

---------

## Becoming Mouseless

Do you want to build a [Mouseless Development Environment](https://themouseless.dev) where the Linux shell has a central role?

[![building your mouseless development environment](https://themouseless.dev/images/small_cover.webp)](https://themouseless.dev)

Switching between the keyboard and the mouse costs cognitive energy. This book will guide you step by step to set up a Linux-based development environment that keeps your hands on your keyboard.

Take the brain power you've been using to juggle input devices and focus it where it belongs: on what you create.

---------

