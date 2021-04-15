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

{{< youtube SEkdPKcws3s >}}

# Grep And Regex

* Continue grep from previous video
    * I encourage you to watch it
* I would encourage you: 
    * Download the files in the description
    * Open a terminal 
    * Try with me
    * From time to time questions, you can pause video and solve

## Definition 

* A regular expression
    * Specific kind of pattern of characters allowing you to match text from left to right

* Try to search `[2020-07-23` in pacman.log
    * doesn't work
    * `[` is a reserved character used for regular expression

* Character used to create regular expression
    * metacharacters
    * `$()*+.?[\^{|`
    * Absent are `]}-`
        * `]-` are metacharacters only after `[`
        * `}` only after }

## Escaping Regular expression

* How to do our search?
    * escape with backslash
        * `grep "\[2020-07-23"`
    * using `grep -F` (fixed string)
        * `grep -F "[2020-07-23"`
        * maybe you see `fgrep`
            * but deprecated

* Grep understand three types of regular expression 
    * Basic regular expression (by default)
        * `grep "pacman" pacman.log`
    * Extended regular expression
        * `grep -E "pacman" pacman.log`
    * Perl Regular expression
        * Won't speak about it

* Difference between basic regular expression
     * Characters `?+{|()` are not considered as metacharacter
        * Need to escape it to make them metacharacter

* Always use `grep -E` for regular expression and `grep -F` if you don't

## Matching whole word

* A word is a to z, A to Z, digits, and underscore
* Regular expression token `\b`
    * grep -E "Warn" haskell.log
    * grep -E "\bWarn\b" haskell.log
    * grep -E "\bWarning\b" haskell.log
* Another way
    * grep -E -w "Warn" haskell.log
    * grep -E -w "Warning" haskell.log

## Beginning / End of Line

* If we try to search every log in 2020
    * `grep -E "2020" pacman.log`
        * Doesn't work. We have result from `2019` as well
    * need to use `^`
    * `grep -E "^\[2020" pacman.log`
* If we try to see what tasks where done (with done at the end of the line) 
    * `grep -E "done" haskell.log`
        * Doesn't work
    * `grep -E "done$" haskell.log`

### Exercise

* In haskell.log, only lines beginning with "HTTP"
    * grep -E "^HTTP" pacman.log
    * Only 3 results normally
* In pacman.log, only the whole word "sane"
* In pacman.log, only the whole word "sane" but at end of line

## Matching characters

* Matching any single character: `.`
    * For example match only pacman when there is an option to it
    * `grep -E "-." pacman.log`
        * Doesn't work, `-.` taken as an option
            * Even if not in our metacharacters
        * Can escape it
        * Can add `--`
    * `grep -E -- "-." pacman.log`
        * Problem: everything with `-` + other character is match
    * `grep -E -- " -." pacman.log`
    * Works but better to use metacharacter for space
    * `grep -E -- "\s-." pacman.log`

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

