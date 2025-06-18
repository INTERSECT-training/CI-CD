---
title: "Exit Codes"
teaching: 5
exercises: 10
---

:::::::::::::::::::::::::::::::::::::: questions 

  - What is an exit code?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

  - Understand exit codes
  - How to print exit codes
  - How to set exit codes in a script
  - How to ignore exit codes
  - Create a script that terminates in success/error

::::::::::::::::::::::::::::::::::::::::::::::::




One very important, foundational concept of  Continuous Integration / Continuous Deployment (CI/CD) tooling is exit codes. These signal to the many CI/CD tools if a job is successful or failing. The connection between exit codes and CI/CD pipelines is programming language agnostic and useful for any project understanding and debugging CI/CD issues.

# Start by Exiting

How does a general task know whether or not a script finished correctly or not? You could parse (`grep`) the output:

```bash
ls nonexistent-file
```

```output
ls: cannot access 'nonexistent-file': No such file or directory
```


But every command outputs something differently. Instead, scripts also have an (invisible) exit code:

```bash
ls nonexistent-file
echo $?
```



```bash
ls: cannot access 'nonexistent-file': No such file or directory
2
```

The exit code is `2` indicating failure. What about on success? The exit code is `0` like so:

```bash
echo
echo $?
```

```output
0
```


But this works for any command you run on the command line! For example, if I mistyped `git status`:

```bash
git stauts
echo $?
```


```output
git: 'stauts' is not a git command. See 'git --help'.

The most similar command is
  status
1
```

and there, the exit code is non-zero -- a failure.

# Printing Exit Codes

As you've seen above, the exit code from the last executed command is stored in the `$?` environment variable. Accessing from a shell is easy `echo $?`. What about from python? There are many different ways depending on which library you use. Using similar examples above, we can use the `getstatusoutput()` call:

::::::::::::::::::::::::::::::::::::: callout 

## Printing Exit Codes via Python

To enter the Python interpreter, simply type `python` in your command line.

Once inside the Python interpreter, simply type `exit()` then press enter, to exit.

::::::::::::::::::::::::::::::::::::::::::::::::

```python
>>> from subprocess import getstatusoutput
>>> status,output=getstatusoutput('ls')
>>> status
0
>>> status,output=getstatusoutput('ls nonexistent-file')
>>> status
2
```

It may happen that this returns a different exit code than from the command line (indicating there's some internal implementation in Python). All you need to be concerned with is that the exit code was non-zero (there was an error).

# Setting Exit Codes

So now that we can get those exit codes, how can we set them? Let's explore this in `shell` and in `python`.

## Shell

Create a file called `bash_exit.sh` with the following content:

```bash
#!/usr/bin/env bash

if [ $1 == "hello" ]
then
  exit 0
else
  exit 59
fi
```

and then make it executable `chmod +x bash_exit.sh`. Now, try running it with `./bash_exit.sh hello` and `./bash_exit.sh goodbye` and see what those exit codes are.

## Python

Create a file called `python_exit.py` with the following content:

```python
#!/usr/bin/env python

import sys


if sys.argv[1] == "hello":
  sys.exit(0)
else:
  sys.exit(59)
```

and then make it executable `chmod +x python_exit.py`. Now, try running it with `./python_exit.py hello` and `./python_exit.py goodbye` and see what those exit codes are. Déjà vu?

# Ignoring Exit Codes

To finish up this section, you will sometimes encounter a code that does not respect exit codes. This can be very annoying when you start development with the assumption that exit status codes are meaningful (such as with CI). In these cases, you'll need to ignore the exit code. An easy way to do this is to execute a second command that always gives `exit 0` if the first command doesn't, like so:

```bash
> ls nonexistent-file || echo ignore failure
```

The `command_1 || command_2` operator means to execute `command_2` only if `command_1` has failed (non-zero exit code). Similarly, the `command_1 && command_2` operator means to execute `command_2` only if `command_1` has succeeded. Try this out using one of scripts you made in the previous session:

```bash
> ./python_exit.py goodbye || echo ignore
```

What does that give you?


::::::::::::::::::::::::::::::::::::: callout 

## Overriding Exit Codes

It's not really recommended to 'hack' the exit codes like this, but this example is provided so that you are aware of how to do it, if you ever run into this situation. Assume that scripts respect exit codes, until you run into one that does not.

::::::::::::::::::::::::::::::::::::::::::::::::



::::::::::::::::::::::::::::::::::::: keypoints 

  - Exit codes are used to identify if a command or script executed with errors or not
  - Not everyone respects exit codes

::::::::::::::::::::::::::::::::::::::::::::::::