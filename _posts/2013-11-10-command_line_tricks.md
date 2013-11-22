---
layout: post
title: Command Line Tricks
category: unix
tag: blog
---

As a vim user, I spend most of my day working with the command line.
Here are some a few tricks I've learned over the years that I couldn't live without. 

### Unix tools philosophy

First a small word about the unix tools philosophy, unix programs were design from the start to have a single purpose.
Instead of a huge programs that tries to do everything, unix tools are a set of small programs that do single things, they can then be easily combine to do more complex operation with the pipe `|`, and '&&' or i/o redirection `> <`

### General

Some general tricks to start.

* I *highly* recommend [Zsh](http://www.zsh.org/), it's an improved unix shell, you can watch a [Railscast Episode](http://railscasts.com/episodes/308-oh-my-zsh) about it.
* use `^W` to cut a line
* use `^Y` to paste
* use `^A` to move the cursor to the beginning of the line

### History

You will often find yourself repeating the same commands.
Using the arrow key is probably the most easy way to navigate your command history, but it is highly unefficient.

Here a few tricks:

* You can search your history with autocompletion with `^R`. (Where ^ means the command key)
* You can use `!!` to repeat the last command.
* You can get the last variable with `!$`
* You can repeat the last command that start with _string_ with `!string`
* You can view you entire history with `history` to repeat a command, you can use `!number` where _number_ is the number in front of the command. You can search your history by piping the command to grep like so `history | grep string_to_search`

### Navigation

Navigation is something that is important to master in order to work effectively at the command line.

The directory stack is a great tool to navigate quickly between different folders.
You can view the current stack with the `dirs` command.
To add the current dir to the stack use the `pushd` command.
To retrieve the first element of the stack use `popd`

* Go back to your latest location with `cd -`
* Configure your $CDPATH to point to a place you navigate frequently. I personaly point it to the root my projects folder.

### Substitution

You can substitute part of a command by wrapping the original and the new variable in {}.
For example, let's say I want to move a file to a different folder, rather than using this command:

`mv dir/original/a.txt dir/new/a.txt`

I could write:

`mv dir/{original,new}/a.txt`

---

That's all for now, please share your favorite tricks!