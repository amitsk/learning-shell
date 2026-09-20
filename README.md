# learning-shell

A collection of practical shell exercises, scripts, and cheatsheets for learning Linux command-line tools. The goal is not to memorize flags. It is to read, check, and finish the command a coding agent just wrote.

[Start Here: Getting Started with Shell Scripting →](scripts/getting_started.md)

## Table of Contents

- [Overview](#overview)
- [Why the shell still matters (especially with AI)](#why-the-shell-still-matters-especially-with-ai)
- [Getting Started](#getting-started)
- [Contents](#contents)
  - [Getting Started with Shell Scripting](#getting-started-with-shell-scripting)
  - [Text Editors on the Command Line](#text-editors-on-the-command-line)
  - [Bash Shell & Scripting Basics](#bash-shell--scripting-basics)
  - [Shell Customization](#shell-customization-guide)
  - [Text Processing with sed, awk, sort, uniq](#text-processing-with-sed-awk-sort-uniq)
  - [AWK Tutorial](#awk-tutorial-practical-shell-data-processing)
  - [HTTP Tools: wget & curl](#http-tools-wget--curl-basics)
  - [User and Group Management](#user-and-group-management-in-linux)
  - [Build Systems: Make and Ninja](#build-systems-make-and-ninja)
  - [Useful Resources](#useful-resources)
- [Contributing](#contributing)

---

## Overview

Hands-on exercises, sample data (`banklist.csv`, `nginx.log`), and cheatsheets for:

- Navigating the filesystem without guessing
- Permissions, ownership, users, and groups
- Processing CSV and log files with `sed`, `awk`, `sort`, and `uniq`
- HTTP from the prompt (`curl`, `wget`) instead of a GUI
- Build files you can read (`make`, `ninja`)

Each chapter has a short **In the age of AI** note: not "skip this, the model knows," but "here is what you still have to check after the model types it."

---

## Why the shell still matters (especially with AI)

Coding agents are fluent in `bash`. They are also fluent in imaginary flags, unquoted variables, and `chmod 777` "just to make it work." The shell is how you **see** a command before it runs, **check** that it did what you meant, and **glue** small tools together on a machine the chatbot cannot click.

These pages are literacy for that loop:

1. **Read** what an agent is about to run — especially `rm`, `chmod`, `curl | sh`, and anything with `sudo`.
2. **Verify** the output against the file in front of you. If `awk` counted the CSV header as a US state, the model will not be the first to notice.
3. **Narrow the problem** with `rg`, `find`, `fzf`, and logs so you paste evidence into the agent, not the whole disk.
4. **Finish the job** on a remote box: SSH, a terminal editor, a pipeline, a Makefile. Agents draft; you press Enter.

You do not need to out-type autocomplete. You need to know when `$9` is the HTTP status, when it is not, and when `:q!` is the correct emotional response.

---

## Getting Started

1. Clone this repository (or download the files if git is still on tomorrow's list).
2. Open `scripts/` — exercises, sample data, and cheatsheets live there.
3. Type the commands. Reading them is how you stay polite to the terminal; running them is how you learn.

---

## Contents

### Getting Started with Shell Scripting

- Cross-platform setup and tips in [scripts/getting_started.md](scripts/getting_started.md)
- What a shell is, bash/zsh/ksh/csh/fish in brief, and why these pages use Bash
- WSL2, Windows Terminal, starship.rs / Oh My Posh, and scratch directory notes
- SSH to remote servers, including **key-based authentication** (`ssh-keygen`, `ssh-copy-id`, `ssh-agent`)

### Text Editors on the Command Line

- Terminal editors in [scripts/text_editors.md](scripts/text_editors.md)
- nano/pico, Vim (including how to leave), Neovim, Emacs
- Newer editors: Helix and Microsoft Edit
- Vim/Emacs keybindings in the shell, Codex CLI, and Grok Build — same joke, smaller box

### Bash Shell & Scripting Basics

- Walkthrough of the filesystem in [scripts/basic_shell.md](scripts/basic_shell.md): a `scratch/` playground, then `ls`/`rm`/pipes/permissions
- Scripting (variables, loops, functions) in [scripts/tools_bash.md](scripts/tools_bash.md)
- Notes on reading agent-generated commands (`rm`, `chmod 777`, `shellcheck`)

### Shell Customization Guide

- rc files, aliases, `PATH`, and a prompt that tells the truth in [scripts/shell_customization.md](scripts/shell_customization.md)
- `sudo` is not a spicy prefix; starship.rs or Oh My Posh — pick one, they fight if you enable both

### Text Processing with sed, awk, sort, uniq

- A workout on a real CSV of failed banks in [scripts/tools_sed.md](scripts/tools_sed.md)
- Cut columns, drop junk, `sort | uniq`, notice when the header got counted
- AWK basics for CSV and log analysis

### AWK Tutorial: Practical Shell Data Processing

- Twenty minutes with `nginx.log` in [scripts/tools_awk.md](scripts/tools_awk.md): `$9` is the status, until it isn't

### HTTP Tools: wget & curl Basics

- Download a file, POST a form, copy over SSH in [scripts/tools_http.md](scripts/tools_http.md)
- `wget` is the download button; `curl` is the Swiss Army knife; neither is `curl | sh`

### User and Group Management in Linux

- `/etc/passwd` is a phone book (the passwords moved) in [scripts/users_groups.md](scripts/users_groups.md)
- `useradd -m`, `usermod -aG`, and the flags that do not wipe `sudo`

### Build Systems: Make and Ninja

- `make` you can read, `ninja` a generator writes, in [scripts/build_systems.md](scripts/build_systems.md)
- Tabs, not spaces. `.PHONY`. `make -n` before `make`
- C and Rust examples, then when to let CMake write the Ninja file

### Useful Resources

- Curated list of Linux and shell learning resources
- [Linux Journey](https://linuxjourney.com/)
- [The Linux Documentation Project (LDP)](https://tldp.org/guides.html)
- [Interactive Vim Tutorial](https://openvim.com/)
- [Digital Ocean Linux series](https://www.digitalocean.com/community/tags/linux-basics)
- [CSAIL's Missing Semester Series](https://missing.csail.mit.edu/)
- [Linux Cheatsheets](http://www.nixtutor.com/linux/all-the-best-linux-cheat-sheets/)
- [Helix: A Modern Text Editor](https://helix-editor.com/)
- [Microsoft Edit](https://github.com/microsoft/edit)
- [NeoVim - hyperextensible Vim-based text editor](https://neovim.io/)
- [starship.rs - X Shell Prompt](https://starship.rs/)
- [Oh My Posh - Cross-shell Prompt Theme Engine](https://ohmyposh.dev/)
- [Lots of Fonts](https://www.nerdfonts.com/)

---

## Contributing

Contributions, suggestions, and improvements are welcome — including better jokes, if you can find them. Open an issue or a pull request.

### Reviewing changes in VS Code

Pull requests are the review surface for this repo. In [Visual Studio Code](https://code.visualstudio.com/) (or [VS Code for the Web](https://vscode.dev/)):

1. Install the recommended [GitHub Pull Requests](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github) extension (see `.vscode/extensions.json`).
2. Sign in to GitHub from the Accounts menu.
3. Open the **GitHub Pull Requests** view, pick the PR, and comment on the diff.

You can also open a PR in the browser editor:

```text
https://github.dev/amitsk/learning-shell/pull/<number>
```

From a local checkout: Command Palette → **GitHub Pull Requests: Review Pull Request**.
