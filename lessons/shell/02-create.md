---
layout: lesson
root: .
title: The Unix Shell
subtitle: Creating Things
minutes: 15
---
### Learning Objectives
*   Create a directory hierarchy that matches a given diagram.
*   Create files in that hierarchy using an editor or by copying and renaming existing files.
*   Display the contents of a directory using the command line.
*   Delete specified files and/or directories.

From the previous lesson, we now know how to explore files and directories. You probably already know how to create files and folders using the desktop environment of your operating system. These are tasks that we can also do through the command line, which will come in handy if you ever need to automate the process.

## Creating new directories

Let's go back to Nelle's home directory,
`/users/nelle`,
and use `ls -F` to see what it contains:

```shell
$ pwd

/users/nelle

$ ls -F

creatures/  molecules/           pizza.cfg
data/       north-pacific-gyre/  solar.pdf
Desktop/    notes.txt            writing/
```

##### Command: `mkdir`

Let's create a new folder called `thesis` in her home directory. We do this with the command `mkdir <path_to_new_directory>`:

```shell
$ mkdir thesis
```
The command `mkdir` stands for "make directory" and has no output.
The argument `thesis` is the path to the new directory. It is a relative path
(since it doesn't have a leading slash), so
the new directory is created in the current working directory:

```shell
$ ls -F

creatures/  north-pacific-gyre/  thesis/
data/       notes.txt            writing/
Desktop/    pizza.cfg
molecules/  solar.pdf
```
Nelle's home directory now contains a folder named `thesis`. However, there's nothing in it yet:

```shell
$ ls -F thesis
```

Because the directory is empty, the command `ls` returns nothing.

## Creating new files

> ### Touching Files
>
> One of the simplest ways to create an empty file through the command line is via the `touch` command.
> Change the working directory to `thesis` using `cd`,
> then *touch* an empty file called `empty.txt`:
>
> ```shell
> $ cd thesis
> $ touch empty.txt
> ```
>
> If we check the directory contents now,
>
> ```shell
> $ ls -F 
>
> empty.txt
> ```

Let's change our working directory to `thesis` using `cd`,
then run a text editor called `nano` to create a file called `draft.txt`:

```shell
$ cd thesis
$ nano draft.txt
```

> ### Which Editor?
> 
> When we call `nano` a text editor, we really do mean "text": it can
> only work with plain character data, not tables, images, or any other
> human-friendly media. We use it in examples because it doesn't require
> much training. It is not the only command-line text editor out there:
> many people use [Emacs](http://www.gnu.org/software/emacs/) or
> [Vim](http://www.vim.org/), both of which are completely unintuitive,
> even by Unix standards.
>
> Knowing how to create and modify text files from the command line is
> often necessary when working, for example, with remote machines and
> supercomputers. For real work (including writing code), however, you
> should use something more powerful. On Macs, you can use something like
> [TextWrangler](http://www.barebones.com/products/textwrangler/) or
> [Gedit](http://projects.gnome.org/gedit/) (also for Linux). On Windows, you may wish to
> use [Notepad++](http://notepad-plus-plus.org/).

Type in a few lines of text,
then use Control-O to write the file to the disk. The file will be saved in your current working directory.

Once the file is saved,
we can use Control-X to quit the editor and return to the shell.
(Unix documentation often uses the shorthand `^A` to mean "Control-A").
`nano` doesn't leave any output on the screen after it exits,
but `ls` now shows a file called `draft.txt` in the folder `thesis`:

```shell
$ ls

draft.txt
```

## Deleting files and directories

##### Command: `rm`

Let's tidy up by running `rm draft.txt`:

```shell
$ rm draft.txt
```

This command deletes files from the file system ("rm" is short for "remove").
If we run `ls` again, it shows no output, which tells us that our file is gone:

```shell
$ ls
```

> ### Deleting Is Forever
> 
> Unlike desktop environments, the Unix shell doesn't have a trash bin from which we can recover "deleted"
> files (until we "Empty trash" and really delete them). Instead,
> when we delete files in the command-line using `rm`, they are
> immediately unhooked from the file system and the storage space
> they take up on the disk is made available for recycling. Because the data
> itself is not instantaneously erased, there are tools that can find and
> recover deleted files. There's no guarantee they'll
> find the deleted file you are looking for, though, since the computer may have already
> re-occupied the file's disk space with something else.

Repeat the steps above to re-create the file you just deleted. Then move up one directory to `/users/nelle`, where the folder `thesis` lives:

```shell
$ cd ..

$ pwd

/users/nelle

$ ls -F

creatures/  north-pacific-gyre/  thesis/
data/       notes.txt            writing/
Desktop/    pizza.cfg
molecules/  solar.pdf
```

Try to remove the entire `thesis` directory using `rm thesis`:

```shell
$ rm thesis

rm: cannot remove `thesis': Is a directory
```

##### Command: `rmdir`

The command `rm` is meant to be used on files, not directories, so it produces an error. The command for deleting foldrs is `rmdir`,
which is short for "remove directory".
It doesn't work yet either, though,
because the directory we're trying to remove isn't empty:

```shell
$ rmdir thesis

rmdir: failed to remove `thesis': Directory not empty
```

This little safety feature --- not deleting directories that contain files --- can save you a lot of grief,
particularly if you are a bad typist.
To get rid of the folder `thesis`, we must first delete all of the files inside it:

```shell
$ rm thesis/draft.txt

$ ls thesis
```

The directory is now empty, so `rmdir` can delete it:

```shell
$ rmdir thesis
```

> ### With Great Power Comes Great Responsibility
> 
> Removing all of the files in a directory before we can remove the
> directory can quickly becomes tedious. Instead of deleting every file individually, we can use `rm` with the
> `-r` flag (which stands for "recursive") to delete the folder and everything inside it:
> 
> ```
> $ pwd
>
> /users/nelle
>
> $ rm -r thesis
> ```
> 
> This removes everything inside the directory `thesis` (including files and folders inside any sub-directories), then the directory itself. The recursive flag is very handy, but it can do a lot of damage if used
> without care.

## Moving and renaming files and directories

Create the directory `thesis` and file `draft.txt` one more time.
(We can run `nano` from Nelle's home directory with the path `thesis/draft.txt`,
rather than going into the `thesis` directory and running `nano` on `draft.txt` there).

```shell
$ pwd

/users/nelle

$ mkdir thesis

$ nano thesis/draft.txt
$ ls thesis

draft.txt
```

##### Command: `mv`

Calling a file `draft.txt` isn't particularly informative. We can change the name of the file by using the command `mv`, 
which is short for "move":

```shell
$ mv thesis/draft.txt thesis/quotes.txt
```

The first parameter tells `mv` the current path of the file that we're "moving",
while the second parameter is the path that we want it to have after it moves.
In this case,
we're moving `thesis/draft.txt` to `thesis/quotes.txt`,
which has the same effect as renaming the file.
Sure enough,
`ls` shows us that `thesis` now contains one file called `quotes.txt`:

```shell
$ ls thesis

quotes.txt
```

Just for the sake of inconsistency,
`mv` also works on directories --- there is no separate `mvdir` command.

Let's move `quotes.txt` up into the current working directory, `/users/nelle`.
We use `mv` once again,
but this time we'll just use the name of a directory as the second parameter
to tell `mv` that we want to keep the filename
but put the file somewhere new. Because we want the file to go into the current working directory,
the directory name we use is the special directory name `.` that we mentioned earlier.

```shell
$ mv thesis/quotes.txt .
```

`ls` now shows us that `thesis` is empty:

```shell
$ ls thesis
```

> The target path for the command `mv` is relative to the current working directory,
> not the current position of the file we want to move. A very common mistake is to write:
>
> ```shell
> $ mv thesis/quotes.txt ..
> ```
>
> This command would move `/users/nelle/thesis/quotes.txt' to `/users/quotes.txt' because the
> target path `..` is one directory up from the current working directory.

## Copying files and directories

##### Command: `cp`

The `cp` command works very much like `mv`,
except it copies a file instead of moving it.
We can check that it did the right thing using `ls`
with two paths as parameters --- like most Unix commands,
`ls` can be given thousands of paths at once:

```shell
$ cp quotes.txt thesis/quotations.txt

$ ls quotes.txt thesis/quotations.txt

quotes.txt   thesis/quotations.txt
```

To prove that we made a copy and not just some alias,
let's delete the `quotes.txt` file in the current directory
and then run that same `ls` again.

```shell
$ rm quotes.txt

$ ls quotes.txt thesis/quotations.txt

ls: cannot access quotes.txt: No such file or directory
thesis/quotations.txt
```

This time it tells us that it can't find `quotes.txt` in the current directory,
but it does find the copy we made inside `thesis`.


### Challenge:

1. Suppose that you created a `.txt` file in your current directory to contain a list of the 
statistical tests you will need to do to analyze your data, and named it: `statstics.txt`.
After creating and saving this file you realize that you misspelled the filename! How would you
correct your mistake?


2. What is the output of the closing `ls` command in the sequence shown below?

```shell
$ pwd

/home/jamie/data

$ ls

proteins.dat

$ mkdir recombine

$ mv proteins.dat recombine

$ cp recombine/proteins.dat ../proteins-saved.dat

$ ls
```

  a. `proteins-saved.dat recombine`
  b. `recombine`
  c. `proteins.dat recombine`
  d. `proteins-saved.dat`

3. Suppose that you have these folders and files:

```shell
$ ls -F

analyzed/   raw/

$ ls analyzed

fructose.dat    sucrose.dat
```

What command(s) could you run so your folder looks like this?

```shell
$ ls -F

analyzed/  fructose.dat    raw/   sucrose.dat
```

3. What does `cp` do when given several filenames and a directory name, as in:

```shell
$ mkdir backup

$ cp thesis/citations.txt thesis/quotations.txt backup

```

4. What does `cp` do when given multiple filenames, as in:

```shell
$ ls -F

intro.txt    methods.txt    survey.txt

$ cp intro.txt methods.txt survey.txt
```

5. The command `ls -R` lists the contents of directories recursively,
i.e., lists their sub-directories, sub-sub-directories, and so on
in alphabetical order at each level.
The command `ls -t` lists things by time of last change,
with most recently changed files or directories first.
In what order does `ls -R -t` display things?
