# Custom User Shell System

This project is a custom Unix-like shell implemented in C++ as part of the CS 2400 Shell Lab.  
It focuses on **process control**, **signals**, and **job control**, giving you hands-on experience with how a real shell manages foreground and background jobs.

---

## Features

- Custom shell prompt: `tsh> `
- Run external programs (e.g. `/bin/ls`, `/bin/cat`, etc.)
- Built-in commands:
  - `quit` – exit the shell
  - `jobs` – list background jobs
  - `bg <job>` – resume a stopped job in the background
  - `fg <job>` – bring a background/stopped job to the foreground
- Foreground and background execution using `&`
- Signal handling:
  - `Ctrl+C` → sends `SIGINT` to the foreground job
  - `Ctrl+Z` → sends `SIGTSTP` to the foreground job
- Proper reaping of terminated child processes (no zombies)

---

## Repository Structure

The important files in this project are:

- `tsh.cc` – main implementation of the tiny shell (`tsh`)
- `helper_routines.cc` / `helper_routines.h` – helper functions for job and signal management
- `Makefile` – build rules for compiling the shell and tests
- Trace / test files – used to validate correctness of your shell implementation

> Note: The original starter code and lab description are based on the CS:APP Shell Lab.

---

## How to Build

You’ll need a Linux-like environment (Linux, WSL2, or macOS) with `g++` and `make` installed.

```bash
# From the repo root
cd CustomUserShellSystem
make
```

## General Overview of Unix Shells

A *shell* is an interactive command-line interpreter that runs programs
on behalf of the user. A shell repeatedly prints a prompt, waits for a
*command line* on stdin, and then carries out some action, as directed
by the contents of the command line.

The command line is a sequence of ASCII text words delimited by
whitespace. The first word in the command line is either the name of a
built-in command or the pathname of an executable file. The remaining
words are command-line arguments. If the first word is a built-in
command, the shell immediately executes the command in the current
process. Otherwise, the word is assumed to be the pathname of an
executable program. In this case, the shell forks a child process, then
loads and runs the program in the context of the child. The child
processes created as a result of interpreting a single command line are
known collectively as a *job*. In general, a job can consist of multiple
child processes connected by Unix pipes.

If the command line ends with an ampersand "&", then the job runs in the
*background*, which means that the shell does not wait for the job to
terminate before printing the prompt and awaiting the next command line.
Otherwise, the job runs in the *foreground*, which means that the shell
waits for the job to terminate before awaiting the next command line.
Thus, at any point in time, at most one job can be running in the
foreground. However, an arbitrary number of jobs can run in the
background.

For example, typing the command line
```
tsh> jobs
```
causes the shell to execute the built-in jobs command.

### Command Line Arugments
Typing the
command line
```
tsh>/bin/ls -l -d
```
runs the `/bin/ls` program in the foreground. By convention, the shell ensures
that when the program begins executing a programs `main` routine declared as such:
```
int main(int argc, char *argv[])
```
is called so that the `argc` and `argv` arguments have the following values for this example:

-   argc == 3,

-   argv\[0\] == "/bin/ls",

-   argv\[1\]== "-l",

-   argv\[2\]== "-d".

Alternatively, typing the command line
```
tsh>/bin/ls -l -d &
```
runs the ls program in the background. Note that the ampersand (&) is not passed to the program in the arugments.

### Job control
Unix shells support the notion of *job control*, which allows users to
move jobs back and forth between background and foreground, and to
change the process state (running, stopped, or terminated) of the
processes in a job. Typing ctrl-c causes a SIGINT signal to be delivered
to each process in the foreground job. The default action for SIGINT is
to terminate the process. Similarly, typing ctrl-z causes a SIGTSTP
signal to be delivered to each process in the foreground job. The
default action for SIGTSTP is to place a process in the stopped state,
where it remains until it is awakened by the receipt of a SIGCONT
signal. Unix shells also provide various built-in commands that support
job control. For example:

-   jobs: List the running and stopped background jobs.

-   bg \<job\>: Change a stopped background job to a running background
    job.

-   fg \<job\>: Change a stopped or running background job to a running
    in the foreground.

-   kill \<job\>: Terminate a job.

## The tsh Specification

Your tsh shell should have the following features:

-   The prompt should be the string "tsh\> ".

-   The command line typed by the user should consist of a name and zero
    or more arguments, all separated by one or more spaces. If name is a
    built-in command, then tsh should handle it immediately and wait for
    the next command line. Otherwise, tsh should assume that name is the
    path of an executable file, which it loads and runs in the context
    of an initial child process (In this context, the term *job* refers
    to this initial child process).

-   tsh need not support pipes (\|) or I/O redirection (\< and \>).

-   Typing ctrl-c (ctrl-z) should cause a SIGINT (SIGTSTP) signal to be
    sent to the current foreground job, as well as any descendents of
    that job (e.g., any child processes that it forked). If there is no
    foreground job, then the signal should have no effect.

-   If the command line ends with an ampersand &, then tsh should run
    the job in the background. Otherwise, it should run the job in the
    foreground.

-   Each job can be identified by either a process ID (PID) or a job ID
    (JID), which is a positive integer assigned by tsh. Job ID's are
    used because some scripts need to manipulate certain jobs, and the
    process ID's change across runs. JIDs should be denoted on the
    command line by the prefix '`%`'. For example, "%5" denotes JID 5,
    and "5" denotes PID 5. (We have provided you with all of the
    routines you need for manipulating the job list.)

-   tsh should support the following built-in commands:

    -   The quit command terminates the shell.

    -   The jobs command lists all background jobs.

    -   The bg \<job\> command restarts \<job\> by sending it a SIGCONT
        signal, and then runs it in the background. The \<job\> argument
        can be either a PID or a JID.

    -   The fg \<job\> command restarts \<job\> by sending it a SIGCONT
        signal, and then runs it in the foreground. The \<job\> argument
        can be either a PID or a JID.

-   tsh should reap all of its zombie children. If any job terminates
    because it receives a signal that it didn't catch, then tsh should
    recognize this event and print a message with the job's PID and a
    description of the offending signal.

**Shell driver.** The sdriver.pl program executes a shell as a child
process, sends it commands and signals as directed by a *trace file*,
and captures and displays the output from the shell.

Use the -h argument to find out the usage of sdriver.pl:
```
unix> ./sdriver.pl 
Missing required -t argument
Usage: ./sdriver.pl [-hv] -t <trace> -s <shellprog> -a <args>
Options:
  -h            Print this message
  -v            Be more verbose
  -t <trace>    Trace file
  -s <shell>    Shell program to test
  -a <args>     Shell arguments
  -g            Generate output for autograder
```

