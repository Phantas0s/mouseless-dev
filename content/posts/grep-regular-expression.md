+++
date = "2021-04-01"
draft = true
title = "grep: The Basics"
aliases = [
    "grep-beginner-mouseless",
]
image = "/images/2021/grep/grep.jpg"
description = "The basics of grep : output line numbers, filenames, number of matches, or even inverting matches... and more!"
+++

If you're dealing with plain text all day, regular expressions are the secret tool which will step up your efficiency. Even if these patterns look weird, it's not as complicated as it seems.

In this article, we'll use GNU grep in the shell to see how these regular expressions work. To know if you have GNU grep, you can run in your terminal `grep --version`. You should then see something like this:

[{{< picture src="/images/2021/grep/grep.jpg" webp="/images/2021/grep/grep.webp" alt="mindmap of grep" >}}](/images/2021/grep/grep.jpg)

If you don't have GNU grep, I would recommend installing it.

This article is a summary of the following video I made. I would recommend you to watch it first, it's more complete with many examples and exercises:

{{< youtube LIVBktatfQI >}}

The second part:

{{< youtube frVuSFFv4Tk >}}

If you find the videos I have on this channel helpful, don't hesitate to subscribe and to like them. It would then appear to other Youtubers to help even more!

I also recommend you to:

1. [Read my article about grep](/grep-basics-mouseless/) (or [watch the video](https://www.youtube.com/watch?v=SEkdPKcws3s)) to be familiar with the tool itself.
2. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/02_grep_regex)
3. Fire up your beautiful shell
4. Try each command you'll see in this article

You won't learn much if you only read (or watch a video) passively.

In this article we'll see together how to:

Regex with Grep, metacharacters, and escaping
Regex flavors (Basic, extended, and Perl regex)
egrep / fgrep / pgreg
Matching a word ("\b" or "-w")
Matching from the beginning of the line ("^")
Matching till the end of the line ("$")
Matching any character with "."
Matching whitespaces using "\s"
Character class "[ ]"
Quantifiers: fixed repetitions { }
Alternatives and groups
Quantifiers, the return: ?, * and +
Greedy quantifiers

Last thing and we'll dive into the colorful regex world: I assume that you use grep with the option `--color=auto`. If not, I would advise you to create this alias:

```
alias grep='grep --color=auto'
```

Now grep will be nice looking.

## What's a Regular Expression?

### Basics

A regular expression (also called "regex") is a *pattern* composed of characters. This pattern gives you The Tremendous Power™ to match the content you want to match. For example, to search some content, to replace it, or to split a block of text.

Many CLIs you can use in the shell to manipulate some content often give you the opportunity to use regex. Knowing the basics will allow you to build upon it step by step and, believe me, you won't regret it.

### Metacharacters

Try to run the following in your shell:

```
grep "[2020-07-23" pacman.log
```

Unfortunately, you'll get an error. By default, grep expect a regex, and `[` is called a *metacharacter*. It's a character which has a meaning. It can represent a wide range of character if it's used with its friend `]`.

There are many words beginning by meta: metalearning (learning about learning), metadata (data about data). In the same spirit, a regex metacharacter is a character about one or more characters.

The metacharacters used in a regex are the following: 

```
$()*+.?[\^{|
```

What about `]` and `}`? There are metacharacters only when `[` or `{` are used respectively.

When another character is used, it will match its literal form. For example, `a` matches `a`, or `1` matches `1`. You'll see it's true if you run:

```
grep "2020" pacman.log
```

Let's take an example to summarize the difference between the literal form of a character and its metacharacter counterpart:

* The literal form of `$` is `$`.
* The metacharacter `$` means "end of the line".

## Escaping Regular expression

What if you try to search the character `[`, that is, you don't want to use the metacharacter `[` but you want to match its literal form?

You can escape it using a backslash `\` as follows:

```
grep "\[2020-07-23" pacman.log
```

Another way is to use the grep option `-F`:

```
grep "[2020-07-23" pacman.log
```

This solution is better because, when you begin to write pattern of text for matching it in your beautiful content, it's always more readable to avoid using backslashes everywhere. You might see around the Internet some people also using the command `fgrep`, but it's deprecated.

Not every CLI will offer an option for you to write pattern which should only matched literally, so you won't have any choice except escaping your patterns in these cases.

## Regular Expression Flavors

There is no standards for regular expression. Every programming language will implement regexes differently, even if the principles and the basic metacharacters we saw are the same. For grep, you can use three different flavor of regexes:

* Basic regex flavor (by default) - The characters `?+{|()` are not metacharacters. Using them will match their literal form. To use the metacharacters, you need to escape them.
* Extend regex flavor (option `-E`) - All the usual regex metacharacters are available.
* Perl regex flavor (option `-P`) - Add more metacharacters. Only available to GNU grep.

If you don't want to use any metacharacters, you can add the option `-F`.

In short:

1. Each time I want to use a regex, I add the option `-P` to my command. If you need your script to be portable across a large number of system, use `-E`.
2. If you don't want to use a regex, use the option `-F`.

Last thing: you might cross `egrep` or `fgrep` on the Wild Internet. These are equivalent to `grep -E` and `grep -F` respectively, but you shouldn't use them. They're deprecated.

## Beginning and End of Line

Let's begin our exploration of these metacharacters with these two ones:

`^` - Beginning of the line
`$` - End of the line

If we try to search every logs from 2020 in the file pacman.log, we could run the following:

```
grep -E "2020" pacman.log
```

It doesn't really work: when you look at the first result, we have some logs from 2019 because the substring "2020" are part of the logs' messages, but not from the date of the logs themselves. 

We should in that case ensure that `2020` should only be at the beginning of the line. Here's a solution:

```
grep -E "^\[2020" pacman.log
```

We are basically saying: "I want to match every line where there is the characters "[2020" at the beginning of the line. We escape the character `[` with backslash `\` to say to grep: "I want to use `[` as a character, not a metacharacter".

It's the same thing if you want to match something at the end of the line. For example, if you want to match "done" only at the end of the line, you can do:

```
grep -E "done$" haskell.log
```

## Matching a Whole Word

Sometimes, we want to match a whole word and not a substring. A word is defined as a string of character which can contains the range of letter from a to z (lowercase), A to Z (uppercase), digits, and underscores.

For example, `this_is_a_w0rd` is considered... a word. The string `this_is_a_w0rd and_this_one2` is composed of two words. To understand what I mean, you can try to run:

```
grep -E "Warn" haskell.log
```

You'll see that part of the word "Warning" (the substring `Warn`) is matched. What if you want to only match the whole word "Warn" and not the substring?

You can add the regular expression token `\b`. Yes, regexes don't only have metacharacters, they have also tokens. How great!

```
grep -E "\bWarn\b" haskell.log
```

There is only one line of output this time, because there is only one whole word "Warn" in the entire input file.

You an also add the option `-w` for the same effect:

```
grep -E -w "Warn" haskell.log
```

## Matching characters

### Matching Any Single Character

If you want to match a single character, you can use `.`. For example:

```
grep 'th.' pacman.log
```

It will match `the` and the substring `tha` from `zathura`.

## Matching range of character

* Matching range of character with `[]`
    * Character class
    * Match single character out of multiple character
        * `grep -E "[do]list" haskell.log `
    * Inside, four character has a special function:
        * `\^-]`
        * `\` escape a character
        * For others, special meaning depend on their position
            * Possible not to escape them
            * ... but you should or difficult to understand
        * `^` negate character class if placed just after opening bracket
            * grep -E "[^o]list" haskell.log  
        * `-` create a range
            * `grep -E "[a-o]list" haskell.log`
            * `grep` -3 

### Exercise

* Try to match any character followed by "list" in pacman.log
    * `grep -E ".list" haskell.log`
* Try to match "list" followed by an "s", a a space or a hyphen (-) in pacman.log
    * `grep -E "list[s\s\-]"  pacman.log`
    * `grep -E ".list" haskell.log`
* Try to match everything which is not a letter (uppercase or lowercase) and not a number
    * `grep -E "[^a-zA-Z0-9]" pacman.log`

