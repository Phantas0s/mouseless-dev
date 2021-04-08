+++
date = "2021-04-15"
draft = true
title = "GNU sed For Beginners"
image = ""

description = ""
+++

The tool sed is a really good CLI (Command Line Interface) allowing you to perform action on your content line by line. But first, to be sure you have GNU sed, you can run in your terminal `sed --version`. If you don't have GNU grep (but BSD for example), I would recommend you to install the GNU counterpart. You'll have access to many more options.

This article is a summary of two videos. I would recommend you to watch them, it's more complete with many examples and exercises. Here's the first part:

{{< youtube R-3EahsCmpg >}}

If you find the videos I have on this channel helpful, don't hesitate to subscribe and to like them. It would then appear to other Youtubers to help even more!

I also recommend you to: 

1. Download [these files](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/01_grep)
2. Fire up your beautiful shell
3. Try each command you'll see in this article. 

You won't learn much if you only read (or watch a video) passively.

If you want to dig deeper, I wouldn't recommend the manual of sed (command `man sed`), it's pretty short and not that interesting. Instead, try to run `info sed`.

## Basics

This weird name sed means `s`tream `ed`itor. It allows you to edit streams of data. Here's how to use it:

```
sed '<script>' <input>
```

The script is the main element here. Its format:

```
<address><command><option>
```

What's all of that?

* The *address* allows you to match the input lines you want to transform.
* The *command* determines how the content will be transformed. It's always a single letter.
* The *options* are only available for some command. They modify their default behaviors.

Only the command is required here.

By default, `sed` will never modify its input. Instead, it will process each line as follows:

1. Copy the line in a buffer (called *the pattern space*)
2. Edit it depending of the script (command, address, and option(s))
3. Output the edited line

For example, here's a very basic sed command:

```
sed 'd' nginx.conf
```

The script is only an action, `d`. It means that you'll `d`elete every line of your input, the file `nginx.conf`. Nothing will be output, which make this command pretty useless. How cool is that?

### The Address

The address part of a sed script allows you to select the line you want to edit. By default, sed will apply the command on every line of the input if the address is not specified.

It can be a:

* Line number
* Range of lines (from and to separated with a comma)
* Every *nth* line (line where to start followed by a tilda `~` and the )
* Regex (surrounded by two slashes `/`)

For example:

* `sed '1d' nginx.conf` - Delete the first line
* `sed '1,102d' nginx.conf` - Delete a range of line, from line 1 to line 102 (included)
* `sed '/on/d' nginx.conf` - Delete each line where the regex pattern `on` is matched
* `sed '10,/on/d' nginx.conf` - Delete the 10th line if part of it match the regex pattern `on`
* `sed '0~2d' nginx.conf` - Delete every even line.
* `sed '1~2d' nginx.conf` - Delete every odd line.

* How sed works:
    1. Go through a line of the input one by one
    2. Copy the line in a buffer (the pattern space)
    3. Apply the script on the pattern space 
    4. Spit the resulting pattern space and delete it
    5. Continue to the next line

It's possible to negate the address using a bang `!`. In practice it inverts the address.

For example:

* `sed '1!d' nginx.conf` - Delete every line except the first one.
* `sed '/on/!d' nginx.conf` - Delete every line except the ones matching the regex pattern `on`.

### Using Multiple Scripts

You can use more than one script per sed command using a semicolon `;`. For example:

```
sed '1d;/on/d;$d' nginx.conf
```

There are three scripts executed here:

* `1d` - Delete the first line.
* `/on/d` - Delete every line having matching the substring `on`.
* `$d` - Delete the last line.

## Command-Line Options

### Writing Directly The Input File

As we saw, sed doesn't modify the input by default. If you want to do that, you can add the option `-i` for edit files `i`n-place.

Before trying, I recommend to copy your file (say "nginx2.conf") to keep the original. For example:

```
sed -i '1d' nginx2.conf
```

With GNU grep, you can create this backup automatically by prefixing the optino `-i` with the prefix you want the backup filename to take.

For example:

```
sed -i.backup '1d' nginx.conf
```

It will create a copy of nginx.conf called nginx.conf.backup and then delete the first line of the file nginx.conf. You can use any string your want instead of `.backup`.

### Using multiple scripts

We saw already how to use multiple scripts using a semicolon ';'. We can do the same with the command option `-e`.

For example the following is equivalent to `sed '1d;/on/d;$d' nginx.conf`:

```
sed -e '1d' -e '/on/d' -e '$d' nginx.conf
```

I think using the option `-e` insteand of semicolon `;` makes everything a bit clearer.

### Script from a file

To run a script for a file, you can use the option `-f`.

You can try to run the following for example:

```
echo "1d;/on/d;$d" > script
sed -f script nginx.conf
```

### Regex

* Have the choice between two regex engines
    * By default: Basic regular expression 
    * `-E` Extended regular expression
    * similar to grep -E
        * Compared to the basic regex, are metacharacter: : '?', '+', '()', '{}', and '|'.

* No PERL engine

### Silent

* Display no output
    * `sed -n '1,102d' nginx.conf`
* New command: p
* To print the first line 
    * Could think doing `sed "1p"`
    * sed -n "1p" nginx.conf

### Exercises

Output only the lines which have the pattern "on" from the file nginx.conf
    * `sed -n '/on/p' nginx.conf`
    * `sed '/on/!d' nginx.conf`

Output only the lines beginning by two whitespace characters from the file nginx.conf.
    * `sed -En '/^\s{2}/p' nginx.conf`
    * `sed -E '/^\s{2}/!d' nginx.conf`
        * Need to use -E

Copy the file pacman.conf and delete directly in the copy the first two lines
    * `cp pacman.conf pacman2.conf`
    * `sed -i '1,2d' pacman2.conf`

## Summary: a Mindmap of grep

[{{< picture src="/images/2021/grep/grep.png" webp="/images/2021/grep/grep.webp" alt="mindmap of grep" >}}](/images/2021/grep/grep.png)

## Using grep in Practice

It's nice to learn all of that, but how can you use grep in real life? Here's a video showing you a problem I had and I solved nicely using grep and other CLIs:

{{< youtube N2YsWcyEpr8 >}}

