---
layout: lesson
root: .
title: The Unix Shell
subtitle: Files and Directories
minutes: 15
---
### Learning Objectives
*   Explain the similarities and differences between a file and a directory.
*   Translate an absolute path into a relative path and vice versa.
*   Construct absolute and relative paths that identify specific files and directories.
*   Explain the steps in the shell's read-run-print cycle.
*   Identify the actual command, flags, and filenames in a command-line call.
*   Demonstrate the use of tab completion, and explain its advantages.

# Navigating files and directories in the shell

The part of the operating system responsible for managing files and directories
is called the **file system**.
It is composed of files,
which hold information,
and directories (or *folders*),
which hold files or other directories. Several shell commands can be used to create, inspect, rename, and delete files and directories. While you can do many of these things in the desktop environment of your own computer, these commands are fundamental for navigating the file system of remote machines and supercomputers.


Start by opening a shell window. You can do this by opening the Terminal in MacOS or Linux, or using an application such as Git Bash or Cygwin in Windows.

```shell
$
```

The dollar sign is a **prompt** that shows that the shell is waiting for input. Your shell might show something more elaborate, like the name of your computer and your current directory followed by the prompt.

##### Command: `whoami`

Type the command `whoami` and press the *Enter* key (sometimes marked *Return*) to send the command to the shell.

In response, the shell will print the command’s output. The command `whoami` prints the ID of the current user,
i.e.,
it shows us who the shell thinks we are:

```shell
$ whoami

nelle
```

When we type `whoami`, the shell:

1.  finds a program called `whoami`,
2.  runs that program,
3.  displays that program's output, then
4.  displays a new prompt to tell us that it's ready for more commands.


##### Command: `pwd`

We can find out where we are in the file system by running a command called `pwd`
(which stands for "print working directory").
Our **current working directory** (the directory in which we are) 
is the directory where the computer assumes we want to run commands,
unless we explicitly specify something else (If you run a command that creates a new file, for example, the file will, by default, be created in the current working directory).

```shell
$ pwd

/users/nelle
```

The computer's response is `/users/nelle`,
which is Nelle's **home directory**. The output to the `pwd` command in your own computer will almost surely be different (since your name is probably not Nelle!), and it might say that you are not in your home directory.

> ### Alphabet Soup
> 
> If the command to find out who we are is `whoami`, the command to find
> out where we are ought to be called `whereami`. So why is it `pwd`
> instead? The usual answer is that in the early 1970s, when Unix was
> first being developed, every keystroke counted: the devices of the day
> were slow and backspacing on a teletype was so painful that cutting the
> number of keystrokes and thus cutting the number of typing mistakes was
> actually a win for usability. The reality is that commands were added to
> Unix one by one, without any master plan, by people who were immersed in
> the jargon (and likely had a strange sense of humor). The result is as
> inconsistent as the roolz uv Inglish speling, but we're stuck with it.


## The file system

To understand what a "home directory" is,
let's have a look at how the file system as a whole is organized.
At the top is the **root directory**,
which holds everything that is inside the file system.
We point to the root directory using a slash character `/`.
This is the leading slash in `/users/nelle` - it indicates that the **path** to that folder starts at the root directory. Paths that start at the root are called **absolute paths**.

The root directory contains multiple other directories, depending on your operating system and the particular configuration of your computer. Tracing back from the output of `pwd`, we know that Nelle’s root directory (indicated by the first `/`) contains at least one directory, the folder "users" (`/users`). The directory `users` in turn contains at least one directory, a folder named "nelle” (`/users/nelle`).

Notice that there are two meanings for the `/` character.
When it appears at the front of a path,
it refers to the root directory. When it appears *inside* a name,
it just serves as a separator.

##### Command: `ls`

We can see what’s inside the current working directory using the command `ls`, which stands for "listing":

```shell
$ ls

creatures  molecules           pizza.cfg
data       north-pacific-gyre  solar.pdf
Desktop    notes.txt           writing
```

The command `ls` prints the names of the files and directories in the current directory in alphabetical order,
arranged neatly into columns.
We can make its output more comprehensible by using the **flag** `-F`,
which tells `ls` to add a trailing `/` to the names of directories. Note that there is a space between `ls` and `-F`:
without it,
the shell thinks we're trying to run a command called `ls-F`,
which doesn't exist:

```shell
$ ls -F

creatures/  molecules/           pizza.cfg
data/       north-pacific-gyre/  solar.pdf
Desktop/    notes.txt            writing/
```

This is a case where the slash is serving as a separator - a name with a trailing `/` indicates that it is not a file but a directory, which could have more files and directories inside.

Here,
we can see that `/users/nelle` contains seven **sub-directories**.
The names that don't have trailing slashes,
like `notes.txt`, `pizza.cfg`, and `solar.pdf`,
are files.

> ### What's In A Name?
> 
> You may have noticed that all of Nelle's files' names are "something dot
> something". The "dot something" part of the file name is known as the
> **filename extension**. Depending on your operating system and the
> settings of your computer, you might or might not see these extensions in the GUI.
> 
> We can give a file almost any name we choose. The filename extension is not required,
> but it is there to help the operating system (and the human!) understand the content
> of the file. For example, without an extension, the system won’t know which application
> to use to open the file. Most applications will automatically append a filename extension
> when a file is saved, saving us the trouble.
>
> The filename extensions are just a convention, albeit an important one. Files contain
> bytes: it's up to us and our programs to interpret those bytes
> according to the rules for PDF documents, images, and so on. Naming a PNG image of a
> whale as `whale.mp3` doesn't somehow magically turn it into a recording of whalesong,
> though it *might* cause the operating system to try to open it with a music player
> when someone double-clicks it.


We can look at the contents of other directories even when they are not the current working directory. We can look at what's in Nelle's `data` directory by running `ls -F data`,
i.e.,
the command `ls` with the **arguments** `-F` and `data`.
The second argument --- the one *without* a leading dash --- tells `ls` that
we want a listing of something other than our current working directory:

```shell
$ ls -F data

amino-acids.txt   elements/     morse.txt
pdb/              planets.txt   sunspot.txt
```

The output shows us that there are four text files and two sub-sub-directories.
Organizing things hierarchically in this way helps us keep track of our work:
it's possible to put hundreds of files in our home directory,
just as it's possible to pile hundreds of printed papers on our desk,
but it's a self-defeating strategy.

We spelled the name of the directory as `data`, without a trailing slash. The slash was added by `ls` to the directory names because we used the `-F` flag to help us tell things apart, but it is not part of the folder names. Either `data` or `data/` would have worked with the `ls` command, though. The shell can still understand what we want.

> ### Parameters vs. Arguments
>
> According to [Wikipedia](https://en.wikipedia.org/wiki/Parameter_(computer_programming)#Parameters_and_arguments),
> the terms argument and **parameter**
> mean slightly different things.
> In practice,
> however,
> most people use them interchangeably or inconsistently, so that is what we’ll do.

If we run `ls -F /data` (*with* a leading slash) we get a different answer,
because `/data` is an **absolute path**:

```shell
$ ls -F /data

access.log    backup/    hardware.cfg
network.cfg
```
The leading `/` tells the computer to follow the path from the root of the filesystem,
so it always refers to exactly one directory,
no matter where we are when we run the command.

Nelle happens to have a folder in the root directory named `data`, so she got a list of files in return to that command. If you don’t have a folder by that name, the shell will return an error indicating that the file or directory doesn’t exist.

If we wanted to see what’s inside the sub-sub-directory `pdb` of the sub-directory `data` of Nelle’s home directory (without changing our working directory!), we would write:

```shell
$ ls -F data/pdb
```

This command would return a list of all of the files within that folder. The path to the directory `pdb` doesn’t start with a slash because it’s a **relative path**, i.e., it tells `ls` how to find something from where we are,
rather than from the root of the file system. The absolute path for the directory `pdb` is `/users/nelle/data/pdb`.

### Challenge:

1. Using the command `ls` and relative paths, sketch the directory tree that starts at your home directory. It is probably very big, so don’t do it all! Follow a couple of branches down at least two levels.

2. Using the command `ls` and absolute paths, sketch the directory tree that starts at your root directory.

##### Command: `cd`

We can use `cd`, followed by a path to a directory, to change our working directory.
`cd` stands for "change directory",
which is a bit misleading:
the command doesn't change the directory itself,
it changes our *address* in the file system, from the point of view of the the shell.

```shell
$ cd data
```

The command `cd` doesn't print anything to the screen.
If we run the command `pwd`, we can see that our current working directory is now `/users/nelle/data`, and when we run `ls`,
it lists the contents of that folder:

```shell
$ pwd

/users/nelle/data

$ ls -F

amino-acids.txt   elements/     morse.txt
pdb/              planets.txt   sunspot.txt
```

We used `cd` to go down the directory tree, into a sub-directory one level farther from the root. How do we go back up? We could use an absolute path to take us back to Nelle’s home directory:

```shell
$ cd /users/nelle
```

It's almost always simpler to use `cd ..` to go up one level:

```shell
$ pwd

/users/nelle/data

$ cd ..
```

The `..` is a special directory name meaning
"the directory containing this one",
or more succinctly,
the **parent** of the current directory.
Sure enough,
if we run `pwd` after running `cd ..`, it shows that we're back in `/users/nelle`:

```shell
$ pwd

/users/nelle
```

The special directory `..` doesn't usually show up when we run `ls`. Files and directories with names that start with a *dot* (`.`) are **hidden**. If we want to list them, we can give `ls` the `-a` flag (which stands for "all"):

```shell
$ ls -F -a

./                  creatures/          notes.txt
../                 data/               pizza.cfg
.bash_profile       molecules/          solar.pdf
Desktop/            north-pacific-gyre/ writing/
```

When we give a command more than one flag, we can write them separately (`-F -a`, as we did above), or together (`-Fa`), in any order.

The `-a` flag forces `ls` to show us file and directory names that begin with a dot. Nelle’s home directory includes the sub-directory `..` (which points one level up the directory tree, to the parent directory), as well as another special directory that's just called `.`. Every directory in a Unix file system (and also in Windows) contains the two special directories `.` and `..`. The sub-directory name `.` stands for "this directory”, i.e. it acts as a sub-directory that brings you back to the directory that contains it. It may seem redundant to have a name for this, but we'll see some uses for it soon.

A directory might contain other directories and folders that are hidden (with names that start with a dot). Nelle’s home directory has a file called `.bash_profile` (your home directory probably does as well). This file usually contains settings to customize the shell. There may also be similar files called `.bashrc` or `.bash_login`.

### Challenge:

1. Move up and down your file system using the command `cd` and the special directory name `..` (Hint: use `pwd` to keep track of your location and `ls` to see where you could go).

2. How could you move two levels up the directory tree (from `/users/nelle/data` to `/users`, for example) using a single command? (Hint: the special subdirectory `..` exists in every directory)

Just like paths can indicate that they start at the root directory by using a leading slash (`/users/nelle`, for example), a leading tilde (`~`) is a shortcut to the current user’s home directory and can be used to indicate that paths start there. To get back to our user’s home directory, we type:

```shell
$ cd ~
```

If Nelle did this and ran the command `pwd`, she would see that her current working directory is `/users/nelle`.


### Nelle's Pipeline: Organizing Files

Knowing just this much about files and directories,
Nelle is ready to organize the files that the protein assay machine will create.
First,
she creates a directory called `north-pacific-gyre`
(to remind herself where the data came from).
Inside that,
she creates a directory called `2012-07-03`,
which is the date she started processing the samples.
She used to use names like `conference-paper` and `revised-results`,
but she found them hard to understand after a couple of years.
(The final straw was when she found herself creating
a directory called `revised-revised-results-3`.)

Nelle names her directories "year-month-day",
with leading zeroes for months and days,
because the shell displays file and directory names in alphabetical order.
If she used month names,
December would come before July;
if she didn't use leading zeroes,
November ('11') would come before July ('7').

Each of her physical samples is labelled according to her lab's convention
with a unique ten-character ID,
such as "NENE01729A".
This is what she used in her collection log
to record the location, time, depth, and other characteristics of the sample,
so she decides to use it as part of each data file's name.
Since the assay machine's output is plain text,
she will call her files `NENE01729A.txt`, `NENE01812A.txt`, and so on.
All 1520 files will go into the same directory.

## Tab completion

If she is in her home directory,
Nelle can see what files she has using the command:

```shell
$ ls north-pacific-gyre/2012-07-03/
```

This is a lot to type,
but she can let the shell do most of the work.
If she types:

```shell
$ ls nor
```

and then presses tab,
the shell automatically completes the directory name for her:

```shell
$ ls north-pacific-gyre/
```

If she presses tab again,
Bash will add `2012-07-03/` to the command,
since it's the only possible directory to go into. This is called **tab completion**,
and we will see it in many other tools as we go on.

If there are two or more names that could correspond to the bit the user has already typed, pressing tab does nothing. Pressing tab again will bring up a list of all the possibilities. Continue typing the name of the directory you want until there’s only one possibility for tab completion to choose.
