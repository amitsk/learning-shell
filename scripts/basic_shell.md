# Bash Shell & Scripting Basics

This is the "walk around the filesystem without setting it on fire" chapter. Sections 1–4 are the visit. The rest is the shelf: man pages, find, permissions, pipes, debugging.

**About 45 minutes for sections 1–4. Done when** you have made a file in `scratch/` and read it with `cat` or `less`.

[← How to leave Vim](text_editors.md#you-can-close-this-tab) | [Light path ends at section 4](#the-light-path-ends-here)

---

## Contents

- [1. Five minutes in `scratch/`](#1-five-minutes-in-scratch)
- [2. Where am I?](#2-where-am-i)
- [3. Making, copying, renaming, deleting](#3-making-copying-renaming-deleting)
- [4. Looking at files](#4-looking-at-files)
- [The light path ends here](#the-light-path-ends-here)
- [5. When you forget](#5-when-you-forget)
- [6. Finding files and text](#6-finding-files-and-text)
- [7. Did it work?](#7-did-it-work)
- [8. Permissions](#8-permissions)
- [9. Pipes and redirection](#9-pipes-and-redirection)
- [10. Dotfiles, the keyboard, and "is it running?"](#10-dotfiles-the-keyboard-and-is-it-running)
- [11. Debugging](#11-debugging)

---

## 1. Five minutes in `scratch/`

Reading about `mkdir` is how you stay bad at `mkdir`. Type this from the **repository root** (the directory that contains `scripts/` and `scratch/`). `scratch/` is gitignored; that is the point.

```sh
cd scratch                         # already inside scripts/? use: cd ../scratch
mkdir -p playground/{notes,data}
cd playground
printf '%s\n' "hello from scratch" > notes/hello.txt
cp notes/hello.txt notes/hello.bak
ls -l notes
cat notes/hello.txt
```

You just created a tree, wrote a file, copied it, listed it, and read it. Sections 2–4 are those five moves with better manners. If a command in front of you contains `-delete`, read it before you run it.

Sample data for later lives next to this file: `banklist.csv` and `nginx.log`. From `scratch/playground` they are at `../../scripts/banklist.csv`; from the repo root they are `scripts/banklist.csv`. Either works.

---

## 2. Where am I?

The filesystem is a tree. You are always standing in one directory. `pwd` prints it. `ls` lists it. `cd` moves. That is the whole navigation story; the flags are seasoning.

```sh
pwd                          # /home/you/projects/learning-shell/scratch/playground
ls                           # names only
ls -l                        # permissions, owner, size, date — the useful one
ls -la                       # include hidden files (names starting with .)
ls -lh                       # human sizes: 4.0K, not 4096
ls -lt                       # newest first
ls -ltr                      # oldest first (logs, so the latest is at the bottom)
```

`ls` is the command you will type more than your own name. `ls -ltr` is the one you will type on a server at 2 a.m.

```sh
cd                           # home, same as cd ~
cd /usr/local/bin
cd ~/Documents
cd ..                        # parent
cd -                         # previous directory (a tiny time machine)
cd ../../scripts             # from playground: up, up, into scripts/
```

Tab-complete paths. `cd Doc<Tab>` is not laziness. It is how you avoid typoing `Docments` and then wondering why the computer "doesn't work."

### Pocket `ls`

| Flag | What you wanted |
| --- | --- |
| `-l` | details |
| `-a` | hidden files |
| `-h` | with `-l`, readable sizes |
| `-t` / `-tr` | sort by time |
| `-R` | recursive (noisy; `find` or `eza --tree` is usually nicer) |
| `-d */` | directories only, in the current folder |

Globs (`*.txt`) are expanded by the *shell*, not by `ls`. That is why this lists text files even without `ls`:

```sh
printf '%s\n' *.txt
```

If nothing matches, Bash (by default) hands you the literal `*.txt`. That surprise has a long career.

### Optional upgrade: `eza`

Want colors, git status, and a built-in tree view (`eza -l --git`, `eza --tree --level=2`)? Install and alias [`eza`](https://eza.rocks/) via [Modern tools](modern_tools.md#3-aliases-for-these-tools) (add `--icons` once you have a [Nerd Font](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#coding-fonts-nerd-fonts-banishing-the-tofu)).

---

## 3. Making, copying, renaming, deleting

Four verbs. Three of them are mostly reversible. One of them is `rm`.

```sh
mkdir notes                      # one directory
mkdir -p data/raw data/clean     # -p: create parents, do not complain if they exist
mkdir -p myapp/{src,tests,docs}  # brace expansion: three folders, one thought
```

`mkdir -p` is the grown-up form. You will type it in scripts so a missing parent is not a plot twist.

```sh
cp notes/hello.txt notes/hello.copy
cp -r myapp myapp.bak            # directories need -r
cp -i notes/hello.txt notes/hello.copy   # ask before overwriting
mv notes/hello.copy notes/hello.old      # rename, or move — same command
mv notes/hello.old data/
```

`mv` is rename when the destination is a new name in the same directory, and move when it is a different directory. Unix did not feel this needed two programs. Unix felt a lot of things.

```sh
rm notes/hello.old               # one file
rm -i notes/hello.bak            # ask first
rm -r old-project/               # directory and everything under it
rm -rf temporary-folder/         # force, recursive. Look first.
```

Unix will not move the file to Trash and then send you a recap email. `rm` is usually permanent. `rm -rf` is how directories vanish. Look first, then delete.

**Look, then delete:**

```sh
ls -la before-deleting/
rm -rf before-deleting/
```

If an agent wrote the `rm` line, read the path twice. Models have no emotional attachment to your home directory.

### Pocket `cp` / `mv` / `rm`

| Flag | Meaning |
| --- | --- |
| `-r` / `-R` | recursive (directories) |
| `-i` | interactive: ask before clobbering |
| `-v` | verbose: print what happened |
| `-p` (`cp`) | keep permissions and timestamps |
| `-f` (`rm`) | do not ask, ignore missing files |

---

## 4. Looking at files

`cat` concatenates. Displaying one file is the degenerate case, and the one everyone uses.

```sh
cat notes/hello.txt
cat notes/hello.txt notes/hello.bak          # glued together
cat -n notes/hello.txt                       # line numbers
head -5 ../../scripts/banklist.csv           # first five lines (from playground)
tail -5 ../../scripts/banklist.csv           # last five
less ../../scripts/nginx.log                 # scroll: `q` quits, `/` searches
```

`less` is how you read a file that is longer than your ambition. `cat` on a 4 GB log is how you learn about scrollback.

`head` and `tail` beat `cat file | head`. The extra `cat` is not adding value; it is walking to the fridge to hand someone a fridge.

Write and append with redirection (more in [section 9](#9-pipes-and-redirection)):

```sh
printf '%s\n' "hello" > notes/hello.txt      # overwrite
printf '%s\n' "another line" >> notes/hello.txt
```

`echo` prints a line. Prefer quotes. `echo -e` understands `\n` and `\t` in Bash; `printf` is the portable grown-up.

```sh
echo "Hello, $USER"
echo "Today is $(date)"
echo "Currently in: $(pwd)"
```

Create a file without opening an editor (type, then `Ctrl+D`):

```sh
cat > notes/quick.txt
```

### Optional upgrade: `bat`

[`bat`](https://github.com/sharkdp/bat) is `cat` with syntax highlighting, git markers, and an automatic pager. See [Modern tools](modern_tools.md#2-traps) for install commands and the Debian/Ubuntu `batcat` name quirk.

---

## The light path ends here

You made a directory, put a file in it, and read it back. Close the tab. The [two-hour path](../README.md#if-you-are-new) is complete.

Optional, if the afternoon is still young: [aliases](shell_customization.md#aliases) (20 minutes) or the [modern tools table](modern_tools.md) (15 minutes).

## Keep going

Sections 5–11 are here when you need them: the manual, finding files, permissions, pipes, debugging. [Scripting](tools_bash.md) is the chapter for variables and loops, the week a course asks for a script.

---

## 5. When you forget

`man` is the original chatbot: worse personality, fewer hallucinations, ships with the machine.

```sh
man ls
man grep
man chmod
```

Inside a man page: `/` search forward, `n` next hit, `q` quit. Same keys as `less`, because it *is* `less`.

```sh
ls --help                 # short; GNU tools usually have this
help cd                   # for shell builtins (cd, echo, read) — no man page needed
whatis ls                 # one-line description
man -k copy               # search descriptions by keyword
```

Man sections you will actually meet: **1** (user commands), **5** (file formats, like `man 5 passwd`), **8** (admin commands). `man 1 printf` is the command; `man 3 printf` is the C function. That distinction has confused everyone once.

You do not memorize flags. You memorize that `man` exists, and that `--help` is faster when you just need a reminder.

---

## 6. Finding files and text

Two different jobs, endlessly mixed up:

- **Where is the file?** → `find` (or `fzf`)
- **Where is the line?** → `grep` (or `rg`)

### `find`: walk the tree

```sh
find . -type f -name '*.log' -print
find . -type d -name 'src'
find . -type f -mtime -7                     # regular files, last 7 days
```

`-print` first. Always. Then decide.

```sh
find . -type f -name '*.log' -print
find . -type f -name '*.log' -ok rm -- {} \;
```

`{}` is "the path we just found." `\;` ends `-exec` / `-ok`. `-ok` asks before each `rm`. `-delete` does not ask. Prefer `-ok` until you are sure, and `rm --` so a file named `-rf` is a file, not a flag.

```sh
# Intentional and privileged. Do not paste this into a directory you like.
find . -user "$USER" -exec chown root {} \;
```

### `grep`: find the line

`grep` finds the line you meant, or a different line that happens to contain the same letters. The pattern you think you want is rarely the pattern you typed on the first try. That is not a character flaw. That is text.

From the repo root (`cd ../..` if you are still in `scratch/playground`), on real files in this tutorial:

```sh
grep ',AZ,' scripts/banklist.csv
grep -n -i chicago scripts/banklist.csv
grep -c ',AZ,' scripts/banklist.csv          # 16 matching lines, not "occurrences"
grep -w GET scripts/nginx.log                # GET, not GETTING
grep -C 2 ' 404 ' scripts/nginx.log          # 2 lines of context around 404s
grep -r TODO .                               # recursive (noisy in a git repo)
```

Eleven of those Arizona lines look normal. Five start with a quote because the bank name contains a comma, and that is the thread the [sed chapter](tools_sed.md#quoted-fields-and-why-cut-lies) pulls until the CSV confesses.

Common flags:

| Flag | Meaning |
| --- | --- |
| `-i` | ignore case |
| `-n` | line numbers |
| `-v` | invert |
| `-c` | count matching lines |
| `-l` | only the filenames |
| `-r` | recursive |
| `-w` | whole word |
| `-C N` | context (`-A` after, `-B` before) |

A little regex goes a long way. You do not need to parse email addresses.

```sh
grep '^217' scripts/nginx.log            # starts with
grep 'product_2' scripts/nginx.log
grep -E ' 404 | 500 ' scripts/nginx.log  # extended regex; -E lets you write |
```

Pipes make `grep` a filter, not just a file search:

```sh
ps aux | grep '[n]ginx'            # see section 10 for the [n] trick
ls -l | grep '\.py$'
```

### Optional upgrades: `ripgrep` (`rg`) and `fzf`

At your own keyboard, [`ripgrep`](https://github.com/BurntSushi/ripgrep) (`rg`) is `grep -r` with sane defaults (respects `.gitignore`, skips binaries, prints line numbers), and [`fzf`](https://github.com/junegunn/fzf) fuzzy-filters filenames or `Ctrl+R` history so you don't paste a 400-file tree into a chat window. Keep `grep` in scripts (servers always have it); grab install commands and `fzf` keybindings in [Modern tools](modern_tools.md).

---

## 7. Did it work?

Every command returns a number when it finishes. **0 means success.** Anything else (1–255) means "no," in a flavor you can look up later. `$?` is the last command's status — and it changes after *every* command, including `echo $?`.

```sh
ls /home
echo $?                            # 0

ls /nope
echo $?                            # non-zero, often 2
```

Useful numbers, for when a script fails in a way that is not "it printed something red":

| Status | Rough meaning |
| --- | --- |
| `0` | ok |
| `1` | general error |
| `2` | bad arguments |
| `126` | found it, cannot execute (permissions) |
| `127` | command not found (typo, or not on `PATH`) |
| `130` | you hit Ctrl+C |

Prefer testing the command itself over `$?`. `$?` is a firefly. Look at it, and it has already moved.

```sh
if cp important.txt important.txt.bak; then
  echo "Backup ok"
else
  echo "Backup failed, leaving the original" >&2
fi

if systemctl is-active nginx >/dev/null 2>&1; then
  echo "Nginx is running"
fi
```

`&&` runs the next command only on success. `||` runs it only on failure.

```sh
cp file.txt file.bak && echo "Backup created"
mkdir /tmp/mydir || echo "Could not create directory" >&2
test -f config.txt || touch config.txt
vim notes/hello.txt || nano notes/hello.txt
```

Chain two or three. After that, write an `if`. Left-to-right `&&` / `||` mixes are legal and a popular way to confuse your future self:

```sh
# Legal. Also a puzzle. Prefer if/else for anything you will reread.
cp important.txt backup/ && echo "Backup done" || { echo "Backup failed" >&2; exit 1; }

if make; then
  echo "Build ok"
else
  echo "Build failed" >&2
  exit 1
fi
```

---

## 8. Permissions

Every file has three audiences: **owner**, **group**, **others**. Each audience gets some mix of **r**ead, **w**rite, **x**ecute.

```text
-rwxr-xr--
││││││││││
│││││││││└ others: no execute
││││││││└─ others: no write
│││││││└── others: read
││││││└─── group: no write
│││││└──── group: read + execute
││││└───── owner: execute
│││└────── owner: write
││└─────── owner: read
│└──────── file type: - file, d directory, l symlink
```

Numbers are the same idea in octal. r=4, w=2, x=1. Add them per audience.

| Digit | Meaning |
| --- | --- |
| `0` | `---` |
| `5` | `r-x` (4+1) |
| `6` | `rw-` (4+2) |
| `7` | `rwx` (4+2+1) |

| Mode | Looks like | Typical use |
| --- | --- | --- |
| `644` | `rw-r--r--` | ordinary file |
| `755` | `rwxr-xr-x` | script or directory you are willing to share |
| `700` | `rwx------` | private |
| `600` | `rw-------` | SSH private key |
| `777` | `rwxrwxrwx` | keys in the door, note that says "just in case." Agents love this number. Decline. |

```sh
chmod 644 document.txt
chmod 755 myscript.sh
chmod 700 private.txt
chmod 600 ~/.ssh/id_ed25519
```

### Why `./hello.sh` says Permission denied

A new file is not executable. That is not the computer being rude. That is the computer refusing to run your grocery list.

```sh
printf '%s\n' '#!/bin/bash' 'echo "Hello World"' > hello.sh
./hello.sh
# bash: ./hello.sh: Permission denied

chmod +x hello.sh
./hello.sh
# Hello World
```

`chmod +x` flips the execute bit. `chmod 755` sets the full mode. Both work. You can also skip the bit and name the interpreter:

```sh
bash hello.sh                # works without +x
```

`./hello.sh` needs execute permission *and* a shebang (`#!/bin/bash`) so the kernel knows who should read the grocery list.

```sh
ls -la
```

```text
drwxr-xr-x  2 amit users 4096 Jun 21 10:00 .
-rw-r--r--  1 amit users  220 Jun 21 10:00 .bashrc
-rwxr-xr-x  1 amit users  123 Jun 21 10:00 myscript.sh
```

First character: `-` file, `d` directory. Next nine: owner / group / others.

Directories need execute permission too — it means "you may enter." A directory that is `r--` is a window you can look through and a door you cannot open.

---

## 9. Pipes and redirection

This is the actual superpower. One tool does one thing; the pipe is how they become a workflow. Agents will happily write a 40-line Python script that `sort | uniq -c` already was.

| Symbol | Meaning |
| --- | --- |
| `>` | write stdout to a file (overwrite) |
| `>>` | append stdout |
| `<` | stdin from a file |
| `2>` | write stderr |
| `\|` | stdout of the left becomes stdin of the right |

```sh
printf '%s\n' "Hello" > hello.txt
grep H hello.txt
head -5 < ../../scripts/banklist.csv
```

A real pipeline, on this repo's bank list. Start from the repo root so the paths stay short:

```sh
cd ../..    # if you are still in scratch/playground
cut -d, -f3 scripts/banklist.csv | sort | uniq | head
```

That is "column 3, sorted, unique, first few." It prints values, and some of them are cities — this CSV has quoted fields, so a comma inside a bank name moves the columns. `cut` is not a CSV parser; it is a comma counter. The [sed chapter](tools_sed.md#quoted-fields-and-why-cut-lies) makes that the lesson.

`uniq` only collapses *adjacent* duplicates, which is why `sort` sits in front — skip `sort` and `uniq` will quietly undercount with total confidence (and watch out for the CSV header row sneaking into `cut | sort | uniq`). Models forget both too.

```sh
grep -c ',AZ,' scripts/banklist.csv    # 16
```

Compare that number with whatever a chat window just claimed. When they disagree, `grep -c` is not the one hallucinating.

`echo` into a file is how a lot of scripts start. Quotes still matter:

```sh
echo "This is a log entry" > logfile.txt
echo "Another entry" >> logfile.txt
```

---

## 10. Dotfiles, the keyboard, and "is it running?"

### Dotfiles

Names that start with `.` are hidden from `ls` and visible to `ls -a`. They are ordinary files with a dress code. Common ones:

| File | Job |
| --- | --- |
| `~/.bashrc` | interactive Bash |
| `~/.bash_profile` / `~/.profile` | login Bash |
| `~/.zshrc` | interactive Zsh |
| `~/.vimrc` | Vim |
| `~/.gitconfig` | Git |

People keep these in a git repo so a new laptop feels like the old one. [Shell customization](shell_customization.md) is the next place to put an alias you will actually use.

### Keyboard

| Keys | What they do |
| --- | --- |
| `Tab` | complete a path or command |
| `Ctrl+C` | stop the running command (here it is not "copy") |
| `Ctrl+L` | clear the screen |
| `Ctrl+R` | search history |
| `Ctrl+A` / `Ctrl+E` | start / end of line (Emacs-style; Bash's default) |
| `↑` | previous command |

`history` prints the list. `Ctrl+R` is how you recover the one-liner you swore you would save.

### Is it actually running?

`ps` answers a different question from "the agent said it started."

```sh
ps                         # your processes, this terminal
ps aux                     # most everything (BSD-style columns)
ps aux | grep '[n]ginx'    # the [n] trick: grep does not match its own argv
ps aux --sort=-%mem | head
```

The `[n]ginx` pattern is a tiny regex joke that is also useful: the character class matches `n`, so the process list matches `nginx`, but the `grep` command line itself is `[n]ginx` and does not match. You can also `grep nginx | grep -v grep`. The first one is more fun at parties, which tells you something about the parties.

```text
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
amit      1234  0.1  0.5  21616  5432 pts/0    Ss   10:30   0:01 -bash
```

PID is the handle. STAT: `R` running, `S` sleeping, `Z` zombie (the parent has not `wait`ed — usually a bug, occasionally a ghost story).

```sh
top                        # live view; q to quit
htop                       # nicer, if installed
kill 1234                  # SIGTERM, the polite ask
kill -9 1234               # SIGKILL, the not polite ask
killall process_name
pkill pattern
```

Prefer `kill` before `kill -9`. Processes like a chance to flush files. `-9` is for when they decline.

Job control, for things you started in *this* terminal:

```sh
some-long-command &        # background
jobs
fg %1
bg %1
nohup some-long-command &  # survive hangup (closing the SSH session)
```

In a script, `$!` is the PID of the last background job, and `wait` waits:

```sh
my_command &
pid=$!
wait "$pid"
```

---

## 11. Debugging

Generated scripts fail in the same ways handwritten ones do, just with more confidence. `set -x` shows what actually ran. `shellcheck` is the review you run *before* you ask a model why `echo $file` exploded on a filename with a space.

```sh
#!/usr/bin/env bash
set -x
name="Amit"
echo "Hello, $name"
set +x
echo "Tracing is off."
```

```text
+ name=Amit
+ echo 'Hello, Amit'
Hello, Amit
+ set +x
Tracing is off.
```

`set -e` (`errexit`) exits in many — not all — failure cases. Failures used as the test in `if`, `while`, `&&`, or `||` are exceptions. It is a seatbelt, not a substitute for checking the command you cannot afford to ignore.

```sh
#!/usr/bin/env bash
set -e
ls /nonexistent-directory
echo "This will not be printed."
```

A stricter baseline some people start from: `set -Eeuo pipefail`. `-u` errors on unset variables; `pipefail` makes a pipeline fail if any command in it fails, not just the last. Test those options on *your* script before copying them blindly into every header.

### `shellcheck`

[`shellcheck`](https://www.shellcheck.net/) catches unquoted variables (`echo Hello, $name`), missing shebangs, and unchecked `cd` commands before they ruin your afternoon — whether you or an agent wrote the script. Install commands and examples live in [Bash scripting: `shellcheck`](tools_bash.md#8-before-you-run-it-shellcheck).

---

## Resources

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [Bash tutorial by Free Code Camp](https://www.freecodecamp.org/news/bash-scripting-tutorial-linux-shell-script-and-command-line-for-beginners/)
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html)
- [ShellCheck](https://www.shellcheck.net/)
- [explainshell](https://explainshell.com/) — paste a command, see the flags (still read `man` when it matters)

When you can move around and pipe `cut | sort | uniq` at a CSV, you are ready for programs that do it twice: [Bash scripting basics](tools_bash.md).

[← Two-hour path](../README.md#if-you-are-new) | [Next, when you need it: scripting →](tools_bash.md)
