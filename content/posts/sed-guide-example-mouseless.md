+++
date = "2021-04-13"
draft = false
title = "A Practical Guide of GNU sed With Examples"
image = "/images/2021/sed/sed.jpg"
aliases = [
    "sed-beginner-mouseless"
]

description = "The basics of sed: how to delete precise lines and many ways to substitute whatever content you want using the command-line."
+++

The useful tool sed is a CLI (Command Line Interface) allowing you to edit some content line by line. But first, to be sure you have GNU sed installed on your computer, you can run in your terminal `sed --version`. If you don't have GNU sed (but BSD for example), I would recommend you to install its GNU counterpart. You'll have access to many more options making your life in the shell easier.

This article is a summary of these two videos. They are more detailed and they have some exercises to stick the knowledge in your wonderful brain:

{{< youtube R-3EahsCmpg >}}

The second part:

{{< youtube BtZB-fndkzM >}}

If you find the videos on my Youtube channel helpful, don't hesitate to subscribe and to hit the like button. It would then appear to other Youtubers to help even more!

I would also recommend you to:

1. Download [the file nginx.conf](https://github.com/Phantas0s/mouseless-dev-youtube/tree/master/01_grep). Every example are based on it.
2. Fire up your beautiful shell.
3. Try each example you'll see in this article.

[You won't learn much](https://thevaluable.dev/learning-developer-efficiently-effectively/) if you only read (or watch a video) passively.

If you want to dig deeper, I wouldn't recommend the manual of sed (command `man sed`), it's pretty short and not that interesting. Instead, try to run `info sed`.

## Basics

This weird name sed means `s`tream `ed`itor. It allows you to edit streams of content. Here's how to use it:

```
sed '<script>' <input>
```

The content you give as input will be copied and edited depending on the sed script. It can be divided in three parts:

```
<address><command><option>
```

What's all of these shenanigans?

* The *address* allows you to match the input lines you want to edit.
* The *command* determines how the content will be edited. It's always a single letter.
* The *options* are only available for some commands. They modify their default behaviors.

Only the command is required, the rest is optional.

By default, `sed` will never modify the input you give. Instead, it will:

1. Copy the line it's processing in a buffer (called *the pattern space*)
2. Edit the line depending on the script (command, address, and option(s))
3. Output the edited line

This cycle repeat for every line of the input. For example, here's a very basic sed command:

```bash
sed 'd' nginx.conf
```

The input is the file `nginx.conf` and the script is only an action, `d`. It means that you'll `d`elete every line of the file `nginx.conf`. Nothing will be output, which make this command useless. How cool is that?

### The Address

The address part of a sed script allows you to select the line you want to edit. By default, sed will apply the command on every line of the input if the address is not specified.

This address can be a:

* Line number
* Range of lines
* Every *nth* line (line where to start followed by a tilda `~` and the repetition)
* Regex (surrounded by two slashes `/`)

For example:

* `sed '1d' nginx.conf` - Delete the first line
* `sed '1,102d' nginx.conf` - Delete a range of line, from line 1 to line 102 (included)
* `sed '/on/d' nginx.conf` - Delete each line when part of them match the regex pattern `on`
* `sed '10,/on/d' nginx.conf` - Delete the 10th line if part of it matches the regex pattern `on`
* `sed '0~2d' nginx.conf` - Delete every even line (delete every two lines)
* `sed '1~2d' nginx.conf` - Delete every odd line (delete every two lines after the first one)

It's possible to negate the address using a bang `!`. It will invert the address. For example:

* `sed '1!d' nginx.conf` - Delete every line *except* the first one.
* `sed '/on/!d' nginx.conf` - Delete every line *except* the ones matching the regex pattern `on`.

### Using Multiple Scripts

You can use more than one script per sed command using a semicolon `;`. For example:

```bash
sed '1d;/on/d;$d' nginx.conf
```

There are three scripts executed here:

* `1d` - Delete the first line.
* `/on/d` - Delete every line having matching the substring `on`.
* `$d` - Delete the last line.

## The Substitute Command

I love to delete stuff to make everything simpler, but it would be nice to be able to perform more operations with sed. Let's look at THE awesome command everybody's using: the `s`ubstitute command.

### The Basics

Here's the general way to use it:

```
s/<pattern>/<replacement>/<flag>
```

This command will try to match the regular expression `<pattern>` on each line of your input and replace it with the `<replacement>`. For example, if you want to replace the string "on;" by "off;" you can do:

```bash
sed 's/on;/off;/' nginx.conf
```

By default, if your pattern match two substrings on the line, only the first one will be substituted. You can try to run this command for example:

```bash
sed 's/in/on/' nginx.conf
```

Only the first `in` on each line is replaced, not the ones afterward. If you want to replace every `in`, you need to use the flag `g` (for `g`lobal):

```bash
sed 's/in/on/g' nginx.conf
```

No more silly `in` in our output!

### Reusing the Pattern in the Replacement

You can reuse the pattern you're replacing using the character "&" in your replacement. For example:

```bash
sed 's/on;/&off;/' nginx.conf
```

It will replace every first occurrence of "on;" with "on;off;" on each line.

### Changing the Separators

Using the slash `/` as a separator between your pattern, your replacement, and your flag can bring some problems, especially if you want to replace some URLs. You'll need to escape every single slashes in the URLs themselves for sed to understand what slashes are separators and what slashes aren't.

For example, the following won't work. Our sed is not smart enough to know what slash is used as a separator:

```bash
sed 's/http://server.com/ftp://ftpserver/' nginx.conf
```

Instead, you need to escape every slash which are *not* separators:

```bash
sed 's/http:\/\/server.com/ftp:\/\/ftpserver/' nginx.conf
```

This becomes painful to write and quite difficult to read. But fear not! There's a better solution. You can actually use another separator, like `%` or `#` for example, as follows:

```bash
sed 's#http://server.com#ftp://ftpserver#' nginx.conf
sed 's%http://server.com%ftp://ftpserver%' nginx.conf
```

This is a life savior each time I use sed with URLs.

### Writing Every Line Substituted in a File

If you want to `w`rite everything you substitute in a file, you can use the flag `w` as follows:

```bash
sed 's/on;/&off;/w output_file' nginx.conf
```

It will create a file `output_file` with these two lines:

```bash
sendfile     on;off;
tcp_nopush   on;off;
```

## Command-Line Options

Let's see now what option we can use with our new friend sed.

### Writing Directly The Input File

As we saw, sed doesn't modify the input by default. If you want to do that, you can add the option `-i` for editing your input file `i`n-place. Note that it won't output anything anymore.

Before trying, I recommend you to copy your file "nginx.conf" (running something like `cp nginx.conf nginx2.conf` in your shell) to keep the original.

Here's an example for replacing a file `i`n-place:

```bash
sed -i '1d' nginx2.conf
```

With GNU grep, you can create automatically a backup adding to the option `-i` the suffix your new file should have. For example:

```bash
sed -i.backup '1d' nginx.conf
```

This command will

1. Create a copy of "nginx.conf" called "nginx.conf.backup"
2. Delete the first line of the file nginx.conf

Instead of `.backup` you can use any string your imagination can create.

### Using multiple scripts

We saw already how to use multiple scripts using a semicolon ';'. We can do the same with the option `-e`.  For example these two commands are equivalent:

```bash
sed -e '1d' -e '/on/d' -e '$d' nginx.conf
sed '1d;/on/d;$d' nginx.conf
```

I think using the option `-e` instead of semicolons `;` makes the command a bit easier to read.

### Scripts From a File

To run a script from a file, you can use the option `-f`. You can try to run the following for example:

```bash
echo "1d;/on/d;$d" > script
sed -f script nginx.conf
```

It can be handy for long and complex sed scripts.

### Regexes

You'll have the choice of two regex engines with GNU sed:

* The basic regex engine (by default)
* The `E`xtended regex engine (option `-E`).

The `E`xtended regex engine `-E` include these metacharacters: '?', '+', '()', '{}', and '|'. If you don't use the option `-E`, you would need to escape these metacharacters to use them, making the whole regex more difficult to read.

For example, these two commands are equivalent:

```bash
sed '/on\|nginx/d' nginx.conf
sed -E '/on|nginx/d' nginx.conf
```

I always use `-E` when I want to use regex metacharacters. It makes everything easier.

### Silent

Don't worry, I don't ask you to remain silent. You can make all the noise you want!

With the sile`n`t option `-n`,  sed doesn't output anything anymore. Said like that, it doesn't look very useful. But you can use the command `p` (for `p`rint) to output exactly what you want.

For example, to output only the first line of your input, you can run:

```bash
sed -n "1p" nginx.conf
```

## In a Nutshell: a Mindmap of sed

[{{< picture src="/images/2021/sed/sed.png" webp="/images/2021/sed/sed.webp" alt="mindmap of sed" >}}](/images/2021/sed/sed.jpg)

## Using sed in Practice

It's nice to learn all of that, but how can you use sed in real life? Here's a video showing you a problem I had and I solved nicely using sed and other CLIs:

{{< youtube 4DwXYAAgKKU >}}

