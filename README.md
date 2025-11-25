# Custom User Shell System

This project is a custom Unix-like shell implemented in C++.  
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
