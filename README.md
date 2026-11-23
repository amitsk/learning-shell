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
2. Open `scripts/` — exercises, sample data (`banklist.csv`, `nginx.log`), and cheatsheets live there.
3. Type the commands. Reading them is how you stay polite to the terminal; running them is how you learn.
4. When a chapter asks for a number, check [solutions/](solutions/README.md). Comparing your output with the expected one is the exercise.

---

## Chapters

| Chapter | What is inside |
| --- | --- |
| [Getting Started](scripts/getting_started.md) | What a shell is, Bash vs. zsh/fish, WSL2/macOS setup, `hello.sh`, and SSH keys (`ssh-keygen`, `ssh-copy-id`) |
| [Text Editors](scripts/text_editors.md) | `nano`, Vim (including how to escape), Neovim, Emacs, Helix, Edit, and Vim/Emacs keys in Bash, Codex, and Grok Build |
| [Bash Basics](scripts/basic_shell.md) | `scratch/` playground, navigation, `cp`/`mv`/`rm`, `man`, `find`/`grep`, exit codes, permissions, pipes, processes, debugging |
| [Bash Scripting](scripts/tools_bash.md) | Shebang, variables, `if`/`case`, loops, functions, arrays, `read`, and `shellcheck` |
| [Shell Customization](scripts/shell_customization.md) | `sudo`, rc files, aliases, `PATH`, `PS1`, Starship, and Oh My Posh |
| [sed, sort & uniq](scripts/tools_sed.md) | Workout on `banklist.csv`, why quoted commas make `cut` lie, CRLF traps, and worked [solutions](solutions/README.md) |
| [AWK Tutorial](scripts/tools_awk.md) | Analyzing `nginx.log` — fields, histograms, 404 hunts, and built-ins (`$9` is the status, until it isn't) |
| [HTTP & File Transfer](scripts/tools_http.md) | `wget`, `curl`, `jq` pointers, `scp`, and `sftp` |
| [Users & Groups](scripts/users_groups.md) | `/etc/passwd`, `id`, `useradd -m`, and the `-a` in `usermod -aG` that saves your `sudo` |
| [Build Systems](scripts/build_systems.md) | Hand-written `Makefile`s (tabs, `.PHONY`, `-MMD -MP`) and generated `ninja` builds via CMake |
| [Modern CLI Tools](scripts/modern_tools.md) | 15-minute table for `bat`, `eza`, `rg`, `fd`, `sd`, `zoxide`, `fzf`, `delta`, traps, and safe aliases |

### Useful Resources

Tool-specific links live inside [Modern CLI Tools](scripts/modern_tools.md) and [Text Editors](scripts/text_editors.md). General Linux & shell references:

- [Linux Journey](https://linuxjourney.com/)
- [MIT Missing Semester](https://missing.csail.mit.edu/)
- [The Linux Documentation Project](https://tldp.org/guides.html)
- [DigitalOcean Linux Basics](https://www.digitalocean.com/community/tags/linux-basics)
- [Devhints: Bash Cheatsheet](https://devhints.io/bash)
- [Interactive Vim Tutorial (OpenVim)](https://openvim.com/)
- [ShellCheck](https://www.shellcheck.net/) · [explainshell](https://explainshell.com/)

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
