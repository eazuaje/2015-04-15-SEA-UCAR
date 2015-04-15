---
layout: lesson
root: .
title: The Unix Shell
subtitle: Loops
minutes: 15
---
### Learning Objectives

*   Write a loop that applies one or more commands separately to each file in a set of files.
*   Trace the values taken on by a loop variable during execution of the loop.
*   Explain the difference between a variable's name and its value.
*   Explain why spaces and some punctuation characters shouldn't be used in files' names.
*   Demonstrate how to see what commands have recently been executed.
*   Re-run recently executed commands without retyping them.

Wildcards and tab completion are two ways to reduce typing (and typing mistakes).
Another is to tell the shell to do something over and over again, instead of typing it in repeatedly.

Suppose we have several hundred genome data files named `basilisk.dat`, `unicorn.dat`, and so on.
In this example,
we'll use the `creatures` directory which only has two example files,
but the principles can be applied to many more files at once.
We would like to modify these files, but also save a version of the original files and rename them 
as `original-basilisk.dat` and `original-unicorn.dat`.
We can't use:

```shell
$ mv *.dat original-*.dat
```

because that would expand to:

```shell
$ mv basilisk.dat unicorn.dat original-*.dat
```

This wouldn't back up our files, instead we get an error

```shell
mv: target `original-*.dat' is not a directory
```

This a problem that arises when `mv` receives more than two inputs. When this happens, it 
expects the last input to be a directory where it can move all the files it received. 
Since there is no directory named `original-*.dat` in the `creatures` directory, we get an 
error.

Instead, we can use a **loop**
to do the same operation once for each entry in a list.
Here's a simple example that displays the first three lines of each file in turn:

```shell
$ for filename in basilisk.dat unicorn.dat
  do
     head -3 $filename
  done

COMMON NAME: basilisk
CLASSIFICATION: basiliscus vulgaris
UPDATED: 1745-05-02
COMMON NAME: unicorn
CLASSIFICATION: equus monoceros
UPDATED: 1738-11-24
```

When the shell sees the keyword `for`,
it knows it is supposed to repeat a command (or group of commands) once for each thing in a list.
In this case, the list is the two filenames.
Each time the loop goes around,
the name of the thing currently being operated on is assigned to
the **variable** called `filename`.
Inside the loop,
we get the variable's value by putting `$` in front of it:
`$filename` is `basilisk.dat` the first time through the loop,
`unicorn.dat` the second,
and so on.

By using the dollar sign we are telling the shell interpreter to treat
`filename` as a variable name, not as some text or external command, and substitute its value on its place. When using variables, it is also 
possible to put the names into curly braces to clearly delimit the variable
name: `$filename` is equivalent to `${filename}`, but is different from
`${file}name`. You may find this notation in other people's programs.

Finally,
the command that's actually being run in each file is the command `head`,
so this loop prints out the first three lines of each data file in turn.

> ### Follow the Prompt
>
> The shell prompt changed from `$` to `>` as we were
> typing in our loop. The prompt `$` indicates that the shell is waiting for input, while the prompt `>` is different to remind
> us that we haven't finished typing a complete command yet.

We have called the variable in this loop `filename`
to make its purpose clearer to human readers.
The shell itself doesn't care what the variable is called, though.
If we wrote this loop as:

```shell
for x in basilisk.dat unicorn.dat
do
    head -3 $x
done
```

or:

```shell
for temperature in basilisk.dat unicorn.dat
do
    head -3 $temperature
done
```

it would work exactly the same way.
*Don't name your variables this way, please!*
Programs are only useful if people can understand them,
so meaningless names (like `x`) or misleading names (like `temperature`)
increase the odds that the readers will think the program is supposed to do something that it doesn't.

Here's a slightly more complicated loop:

```shell
for filename in *.dat
do
    echo $filename
    head -100 $filename | tail -20
done
```

The shell starts by expanding `*.dat` to create the list of files to process.
The **loop body**
then executes two commands for each of those files.
The first, `echo`, just prints its command-line parameters to standard output.
For example:

```shell
$ echo Hello World!
```

prints:

```shell
Hello World!
```

In our loop, since the shell substitutes `$filename` for the value of the variable (the name of the file),
`echo $filename` just prints the name of the file.
Note that we can't write this as:

```shell
for filename in *.dat
do
    $filename
    head -100 $filename | tail -20
done
```

because when `$filename` expanded to `basilisk.dat` the first time through the loop, the shell would try to run `basilisk.dat` as a program and it would return an error.

The combination of the `head` and `tail` commands selects lines 81-100 from whatever file is being processed.

> ### Spaces in Names
> 
> Filename expansion in loops is another reason you should not use spaces in filenames.
> Suppose our data files are named:
> 
> ```
> basilisk.dat
> red dragon.dat
> unicorn.dat
> ```
> 
> If we try to process them using:
> 
> ```shell
> for filename in *.dat
> do
>     head -100 $filename | tail -20
> done
> ```
> 
> the shell will expand `*.dat` to create:
> 
> ```
> basilisk.dat red dragon.dat unicorn.dat
> ```
> 
> With older versions of Bash 
> (and most other shells),
> `filename` will then be assigned the following values in turn:
> 
> ```
> basilisk.dat
> red
> dragon.dat
> unicorn.dat
> ```
>
> That's a problem: `head` can't read files called `red` and `dragon.dat`
> because they don't exist,
> and won't be asked to read the file `red dragon.dat` as it's supposed to.
> 
> We can get around this issue **quoting** the use of the variable:
> 
> ```
> for filename in *.dat
> do
>     head -100 "$filename" | tail -20
> done
> ```
>
> but it's simpler just to avoid using spaces (or other special characters) in filenames.

Going back to our original file renaming problem,
we can solve it using this loop:

```shell
for filename in *.dat
do
    mv $filename original-$filename
done
```

This loop runs the `mv` command once for each filename.
The first time,
when `$filename` expands to `basilisk.dat`,
the shell executes:

```shell
mv basilisk.dat original-basilisk.dat
```

The second time, the command is:

```shell
mv unicorn.dat original-unicorn.dat
```

> ### Measure Twice, Run Once
> 
> A loop is a way to do many things at once --- or to make many mistakes at
> once if it does the wrong thing. One way to check what a loop *would* do (instead of actually running them)
> is to echo the commands it would run every time it looped around.
> For example, we could write our file renaming loop like this:
> 
> ```shell
> for filename in *.dat
> do
>     echo mv $filename original-$filename
> done
> ```
> 
> Instead of running the command `mv`, this loop runs the command `echo` and prints out the rest of the line, with the variables expanded to their values:
> 
> ```shell
> mv basilisk.dat original-basilisk.dat
> mv unicorn.dat original-unicorn.dat
> ```
> 
> In the shell, we can then use up-arrow to
> redisplay the loop, back-arrow to get to the word `echo`, delete it, and
> then press "enter" to run the loop with the actual `mv` commands. This
> isn't foolproof, but it's a handy way to see what's going to happen while
> you're still learning how loops work.

## Nelle's Pipeline: Processing Files

Nelle is now ready to process her data files.
Since she's still learning how to use the shell,
she decides to combine the required commands in stages.
Her first step is to make sure that she can select the right files --- remember,
these are ones whose names end in 'A' or 'B', rather than 'Z'. Starting from her home directory, Nelle types:

```shell
$ cd north-pacific-gyre/2012-07-03
$ for datafile in *[AB].txt
> do
>     echo $datafile
> done

NENE01729A.txt
NENE01729B.txt
NENE01736A.txt
...
NENE02043A.txt
NENE02043B.txt
```

Her next step is to decide
what to call the files that the `goostats` analysis program will create.
Prefixing each input file's name with "stats" seems simple,
so she modifies her loop to do that:

```shell
$ for datafile in *[AB].txt
> do
>     echo $datafile stats-$datafile
> done
```
``` {.output}
NENE01729A.txt stats-NENE01729A.txt
NENE01729B.txt stats-NENE01729B.txt
NENE01736A.txt stats-NENE01736A.txt
...
NENE02043A.txt stats-NENE02043A.txt
NENE02043B.txt stats-NENE02043B.txt
```

She hasn't actually run `goostats` yet,
but now she's sure she can select the right files and generate the right output filenames.

Typing in commands over and over again is becoming tedious,
though,
and Nelle is worried about making mistakes,
so instead of re-entering her loop,
she presses the up arrow.
In response,
the shell redisplays the whole loop on one line
(using semi-colons to separate the pieces):

```shell
$ for datafile in *[AB].txt; do echo $datafile stats-$datafile; done
```

Using the left arrow key,
Nelle backs up and changes the command `echo` to `bash goostats`, which runs the program:

```shell
$ for datafile in *[AB].txt; do bash goostats $datafile stats-$datafile; done
```

When she presses enter,
the shell runs the modified command.
However, nothing appears to happen --- there is no output.
After a moment, Nelle realizes that since her script doesn't print anything to the screen any longer,
she has no idea whether it is running, much less how quickly.
She kills the job by typing Control-C,
uses up-arrow to repeat the command,
and edits it to read:

```shell
$ for datafile in *[AB].txt; do echo $datafile; bash goostats $datafile stats-$datafile; done
```

> ### Beginning and End
>
> We can move to the beginning of a line in the shell by typing `^A`
> (which means Control-A)
> and to the end using `^E`.

When she runs her program now,
it produces one line of output every five seconds or so:

```
NENE01729A.txt
NENE01729B.txt
NENE01736A.txt
...
```

1518 times 5 seconds,
divided by 60,
tells her that her script will take about two hours to run.
As a final check,
she opens another terminal window,
goes into `north-pacific-gyre/2012-07-03`,
and uses `cat stats-NENE01729B.txt`
to examine one of the output files.
It looks good,
so she decides to get some coffee and catch up on her reading.

> ### Those Who Know History Can Choose to Repeat It
> 
> Another way to repeat previous work is to use the `history` command to
> get a list of the last few hundred commands that have been executed, and
> then to use `!123` (where "123" is replaced by the command number) to
> repeat one of those commands. For example, if Nelle types this:
> 
> ```
> $ history | tail -5
>   456  ls -l NENE0*.txt
>   457  rm stats-NENE01729B.txt.txt
>   458  bash goostats NENE01729B.txt stats-NENE01729B.txt
>   459  ls -l NENE0*.txt
>   460  history
> ```
> 
> then she can re-run `goostats` on `NENE01729B.txt` simply by typing
> `!458`.

### Challenge:

1. Suppose that `ls` initially displays:

```
fructose.dat    glucose.dat   sucrose.dat
```

What is the output of:

```shell
for datafile in *.dat
do
    ls *.dat
done
```

Now, what is the output of:

```shell
for datafile in *.dat
do
    ls $datafile
done
```

Why do these two loops give you different outputs?

2. In the same directory, what is the effect of this loop?

```shell
for sugar in *.dat
do
    echo $sugar
    cat $sugar xylose.dat
done
```

3. In another directory, where `ls` returns:

```
fructose.dat    glucose.dat   sucrose.dat   maltose.txt
```

What would be the output of the following loop?

```shell
for datafile in *.dat
do
    cat $datafile > sugar.dat
done
```

4. Suppose we want to preview the commands the following loop will execute
without actually running those commands:

```shell
for file in *.dat
do
  analyze $file analyzed-$file
done
```

What is the difference between the the two loops below, and which one would we
want to run?

```shell
# Version 1
for file in *.dat
do
  echo analyze $file analyzed-$file
done
```

```shell
# Version 2
for file in *.dat
do
  echo "analyze $file analyzed-$file"
done
```

5. The `expr` does simple arithmetic using command-line parameters:

```shell
$ expr 3 + 5
8
$ expr 30 / 5 - 2
4
```

Given this, what is the output of:

```shell
for left in 2 3
do
    for right in $left
    do
        expr $left + $right
    done
done
```

6. Describe in words what the following loop does (it produces an error, yes, but what is it doing to produce that error?):

```shell
for how in frog11 prcb redig
do
    $how -limit 0.01 NENE01729B.txt
done
