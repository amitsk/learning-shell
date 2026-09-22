# learning-shell

A collection of practical shell exercises, scripts, and cheatsheets for learning Linux command-line tools. The goal is to type the commands until they are yours, and to read a command — yours or a coding agent's — before you press Enter.

[Start Here: Getting Started with Shell Scripting →](scripts/getting_started.md)

### If you are new

Two hours, then close the tab. Each stop says when you are done.

1. **30 minutes.** [What a shell is](scripts/getting_started.md#1-what-is-a-shell) and [hello.sh](scripts/getting_started.md#3-writing-your-first-script). Done when it prints `Hello, world!`. SSH in that file is for a later day.
2. **20 minutes.** [nano](scripts/text_editors.md#2-nano-and-pico-the-friendly-ones) and [how to leave Vim](scripts/text_editors.md#3-vim-basics-and-the-joke-about-never-leaving). Stop at [you can close this tab](scripts/text_editors.md#you-can-close-this-tab).
3. **45 minutes.** [Bash basics, sections 1–4](scripts/basic_shell.md#1-five-minutes-in-scratch): scratch directory, where you are, files, looking at a file. Stop at [the light path ends here](scripts/basic_shell.md#the-light-path-ends-here).

Same afternoon, if you still feel like it:

- **20 minutes.** [Aliases](scripts/shell_customization.md#aliases) and [reload](scripts/shell_customization.md#6-reloading-your-config). `gs` is Ghostscript; the nickname in this tutorial is `gstatus`.
- **15 minutes.** [Modern tools](scripts/modern_tools.md): one install line, the table, the traps, the alias block.

### When a course names the tool

Open that chapter and ignore the rest. Each one tells you how long it is.

| You were handed… | Open | About |
| --- | --- | --- |
| "Learn the shell" and you finished the list above | You are done | — |
| A script to write | [Bash scripting](scripts/tools_bash.md) | 45 min |
| Permissions, find, or pipes | [the rest of Bash basics](scripts/basic_shell.md#5-when-you-forget) | an evening |
| A CSV | [sed, sort, uniq](scripts/tools_sed.md) | 45 min |
| A log file | [awk](scripts/tools_awk.md) | 30 min |
| A URL | [curl and wget](scripts/tools_http.md) | 30 min |
| Users on a Linux box | [users and groups](scripts/users_groups.md) | 30 min |
| `make` | [Make and Ninja](scripts/build_systems.md) | an evening |
| A prompt or sudo | [the rest of shell customization](scripts/shell_customization.md#1-understanding-sudo-superuser-do) | optional |
| An editor you want to live in | [the rest of text editors](scripts/text_editors.md#4-neovim-vims-hyperextensible-cousin) | optional |

## Table of Contents

- [If you are new](#if-you-are-new)
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
  - [Modern Replacements for Unix Utilities](#modern-replacements-for-unix-utilities)
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
- Modern CLI replacements for classic Unix utilities (`bat`, `eza`, `ripgrep`, `fd`, `sd`, `zoxide`, `delta`) and the aliases that keep them out of scripts

A sentence here and there says what to check when a model typed the command. The commands themselves are the lesson.

---

## Why the shell still matters (especially with AI)

The [two-hour path](#if-you-are-new) does not depend on this section. Read it when you want the reason, then go type `hello.sh`.

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

### Modern Replacements for Unix Utilities

- A 15-minute page in [scripts/modern_tools.md](scripts/modern_tools.md): one install line, a table, the traps (`batcat`, `sd -p`, delta's git config), and the [alias block](scripts/modern_tools.md#3-aliases-for-these-tools)

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
- [bat: A cat clone with syntax highlighting and Git integration](https://github.com/sharkdp/bat)
- [eza: A modern, maintained replacement for ls](https://eza.rocks/)
- [ripgrep: A line-oriented search tool that respects gitignore](https://github.com/BurntSushi/ripgrep)
- [fd: A simple, fast and user-friendly alternative to find](https://github.com/sharkdp/fd)
- [zoxide: A smarter cd command](https://github.com/ajeetdsouza/zoxide)
- [fzf: General-purpose command-line fuzzy finder](https://github.com/junegunn/fzf)
- [sd: Intuitive find & replace](https://github.com/chmln/sd)
- [delta: A syntax-highlighting pager for git and diff](https://github.com/dandavison/delta)

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
