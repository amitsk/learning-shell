# Modern Replacements for Unix Utilities

[← Back: Build Systems: Make and Ninja](build_systems.md)

Classic Unix utilities (`cat`, `ls`, `grep`, `find`, `df`, `du`, `top`) were designed in an era when terminals had 80 columns, RAM was measured in kilobytes, and a screen that could display 16 colors felt like witchcraft. They are universal, battle-tested, and will still be running on government satellites when we are all dust. You must know them.

On a personal workstation, the 1979 defaults are a bit like commuting on a penny-farthing: historically venerable, and hard on the knees.

A newer set of interactive tools — many written in Rust or Go — add syntax highlighting, git awareness, sensible ignore rules, and parallelism at the prompt. They are nicer to look at. They do not replace `ls` on a server you SSH into for five minutes.

> **In the age of AI:** Coding agents and CI runners speak standard POSIX. If an LLM writes a shell script or a Makefile with `cat`, `grep`, `find`, or `sed`, leave them alone. Do not alias `cat` to `bat` in an automated pipeline or an Alpine Docker container — `bat` will happily send ANSI escape sequences and launch an interactive pager inside a headless CI job, where it will hang in the dark waiting for a human finger to press `q`. `sd` is worse in that setting: given a filename, it rewrites the file in place.
>
> **The Golden Rule:** Use the **classic utilities** (`cat`, `grep`, `find`, `sed`) in scripts, Makefiles, Dockerfiles, and agent instructions. Use the **modern replacements** (`bat`, `ripgrep`, `fd`, `sd`, `zoxide`, `delta`) interactively when your actual eyeballs are looking at the glass.

---

## 0. If you are new, install three tools

The matrix below is a menu. Coursework does not require every row.

Install and actually use these three first:

1. **`fzf`** — fuzzy-pick history, files, and git branches (`Ctrl+R`).
2. **`ripgrep` (`rg`)** — search file *contents*.
3. **`bat`** — read a file with line numbers and syntax highlighting.

Add **`delta`** when `git diff` turns into a wall of red and green. Add **`sd`** when you are about to change the same string in a pile of files — preview with `-p` before you write. Paste aliases only into `~/.bashrc` or `~/.zshrc`, from [section 3](#3-aliases-for-these-tools). A script, a Makefile, and a Dockerfile keep the classic names.

The workstation install line (Mint / Ubuntu) lives in [linux-setup §5.4](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#modern-replacements-for-unix-utilities).

---

## 1. The Upgrade Matrix

These are interactive upgrades, not drop-in replacements. Keep the classic tool for scripts and remote boxes.

| Job | Classic | Interactive upgrade | Language | Why bother | Link |
| --- | --- | --- | --- | --- | --- |
| View a file | `cat` / `less` | `bat` | Rust | Syntax highlighting, line numbers, git gutters, automatic paging | [sharkdp/bat](https://github.com/sharkdp/bat) |
| List a directory | `ls` | `eza` | Rust | Colors by file type, git status column, icons, built-in tree view | [eza-community/eza](https://github.com/eza-community/eza) · [eza.rocks](https://eza.rocks/) |
| Search file contents | `grep` | `ripgrep` (`rg`) | Rust | Fast, respects `.gitignore`, skips binaries without asking | [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| Search filenames | `find` | `fd` | Rust | `fd pattern` instead of `-name`, ignores gitignored junk, parallel `-x` | [sharkdp/fd](https://github.com/sharkdp/fd) |
| Find and replace | `sed` | `sd` | Rust | `sd 'old' 'new' file`, JavaScript-style regex, preview with `-p` | [chmln/sd](https://github.com/chmln/sd) |
| Change directory | `cd` | `zoxide` (`z`) | Rust | Learns directories you have visited; fuzzy jump afterwards | [ajeetdsouza/zoxide](https://github.com/ajeetdsouza/zoxide) |
| Read a diff | `diff` | `delta` | Rust | Side-by-side views, language highlighting, word-level diffs | [dandavison/delta](https://github.com/dandavison/delta) |
| Watch processes | `top` / `htop` | `btop` / `bottom` | C++ / Rust | Graphs, mouse support, fast process filtering | [aristocratos/btop](https://github.com/aristocratos/btop) · [bottom](https://github.com/ClementTsang/bottom) |
| Disk free | `df` | `duf` | Go | Clean tables, color-coded usage bars, mounts grouped by type | [muesli/duf](https://github.com/muesli/duf) |
| Disk used | `du` | `dust` | Rust | ASCII tree of what ate the SSD; no `sort -hr` gymnastics | [bootandy/dust](https://github.com/bootandy/dust) |
| List processes | `ps` | `procs` | Rust | Colored columns, human memory units, keyword search | [dalance/procs](https://github.com/dalance/procs) |
| HTTP from the prompt | `curl` / `wget` | keep `curl`; optional `xh` / HTTPie | Rust / Python | Friendlier syntax when you are typing. Scripts and agents still emit `curl`. | [xh](https://github.com/ducaale/xh) · [httpie](https://httpie.io/) |
| Command examples | `man` | `tldr` (alongside `man`) | Various | Five real-world examples. `man` remains the full flag list. | [tldr-pages/tldr](https://github.com/tldr-pages/tldr) · [tldr.sh](https://tldr.sh/) |
| Fuzzy pick from a list | `Ctrl+R` | `fzf` | Go | Interactive finder for history, files, git branches | [junegunn/fzf](https://github.com/junegunn/fzf) |
| Filter JSON | (none; not `sed`/`awk`) | `jq` | C | Slice JSON streams. `sed` and `awk` still own text and CSV. | [jqlang/jq](https://jqlang.github.io/jq/) |
| Graph ping | `ping` | `gping` | Rust | Real-time line graph for network latency | [orf/gping](https://github.com/orf/gping) |

---

## 2. The Heavy Hitters: Tool by Tool

### `bat`: `cat` with syntax highlighting

[`bat`](https://github.com/sharkdp/bat) is `cat` that colors the file, numbers the lines, and pages when the output would otherwise flood your scrollback.

#### Why switch
- **Syntax highlighting:** Hundreds of languages and configs out of the box.
- **Git gutters:** `+`, `~`, and `-` on the left margin for uncommitted changes.
- **Smart paging:** Fits on screen? Prints and exits. Longer? Routes through `less`.
- **Line numbers:** On by default.
- **Binary defense:** Warns before dumping raw bytes that scramble the terminal font.

#### The Debian/Ubuntu naming trap
On Debian and Ubuntu, the package is `bat`, but the executable is `/usr/bin/batcat`. A 25-year-old amateur radio utility called `bat` got there first. Symlink it into `~/.local/bin`:

```sh
sudo apt install bat
mkdir -p ~/.local/bin
ln -sfn /usr/bin/batcat ~/.local/bin/bat
export PATH="$HOME/.local/bin:$PATH"
```

`~/.local/bin` is on `PATH` for login shells on Ubuntu/Mint once the directory exists (see `~/.profile`). A newly created directory is invisible until you export `PATH` in this session or log out and back in. `ln -sfn` lets you re-run the block without "File exists."

Fedora and macOS install the binary as `bat`:

```sh
# Fedora
sudo dnf install bat

# macOS
brew install bat
```

If you skip the symlink, the aliases at the end of this chapter fall back to `batcat`.

#### Everyday use
```sh
# View a code file with line numbers and syntax highlighting
bat src/index.ts

# Plain mode: strips line numbers and headers when you want plain cat behavior
bat -p README.md

# Colorize piped command output by specifying the language
git show HEAD:server.py | bat -l python
```

---

### `eza`: `ls` with colors that mean something

[`eza`](https://eza.rocks/) (the community continuation of `exa`) colors file types, can show git status, and has a built-in tree view.

#### Why switch
- **Meaningful coloring:** Directories, symlinks, executables, sockets, and `r`/`w`/`x` get distinct colors.
- **Git status column:** Modified, staged, or untracked, in the listing (`--git`).
- **Built-in tree view:** `eza --tree` instead of a separate `tree` package.
- **Human units:** `4.2M`, `120K` without a flag scavenger hunt.
- **Nerd Font icons:** `--icons` draws file-type glyphs **if** a [Nerd Font](https://www.nerdfonts.com/) is the terminal font. Without one you get tofu boxes (`□`). Install a font first — [linux-setup: Coding fonts](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#coding-fonts-nerd-fonts-banishing-the-tofu) — and treat `--icons` as optional.

#### Installation
Ubuntu 24.04, Mint 22, and current Debian ship `eza` in the distro repos:

```sh
# Ubuntu 24.04 / Mint 22 / Debian (universe)
sudo apt install eza

# Fedora
sudo dnf install eza

# macOS
brew install eza
```

Older Ubuntu/Debian that lack the package can add the **third-party** gierens repo. That is a trust decision (the same class of risk [linux-setup](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#where-apt-looks) warns about). Prefer `apt install eza` when it exists.

```sh
# Fallback only: third-party repo for older Debian/Ubuntu
sudo apt install -y gpg
sudo mkdir -p /etc/apt/keyrings
wget -qO- https://raw.githubusercontent.com/eza-community/eza/main/deb.asc | sudo gpg --dearmor -o /etc/apt/keyrings/gierens.gpg
echo "deb [signed-by=/etc/apt/keyrings/gierens.gpg] http://deb.gierens.de stable main" | sudo tee /etc/apt/sources.list.d/gierens.list
sudo chmod 644 /etc/apt/keyrings/gierens.gpg /etc/apt/sources.list.d/gierens.list
sudo apt update
sudo apt install -y eza
```

#### Everyday use
```sh
# Detailed listing with git status (add --icons after a Nerd Font is installed)
eza -la --git

# Two-level directory tree
eza --tree --level=2

# Sort by modification time, newest at the bottom
eza -l --sort=modified
```

---

### `ripgrep` (`rg`): `grep` that starts at the repo root

[`ripgrep`](https://github.com/BurntSushi/ripgrep) recursively searches the current directory for a regex. It is fast enough that people notice, and it skips the junk `grep -r` will happily chew through.

#### Why switch
- **Speed:** Rust regex engine, parallel search. Faster than GNU `grep`, `ag`, and `ack` on a typical repo.
- **Developer defaults:** Ignores `.git/`, skips binaries, respects `.gitignore`. You will not search 400MB of minified JavaScript unless you ask.
- **Recursive by default:** No `-r` / `-R`.
- **Great for AI prompts:** Find the five lines that matter instead of dumping entire files into a context window.

#### Installation
```sh
# Ubuntu / Debian / Mint
sudo apt install ripgrep

# Fedora
sudo dnf install ripgrep

# macOS
brew install ripgrep
```

#### Everyday use
```sh
# Search for a pattern across the whole repo
rg "database_timeout"

# Search only Python or Rust files
rg -t py "class UserService"
rg -t rust "impl Repository"

# Search gitignored files. One -u is --no-ignore and still skips dotfiles
# (.env, .env.local). Two -u also searches hidden files:
rg -uu "SECRET_KEY"

# Same idea, without hidden files: a gitignored secrets.yaml
rg -u "SECRET_KEY" --glob 'secrets.yaml'
```

---

### `fd`: `find` for filenames

[`fd`](https://github.com/sharkdp/fd) is a fast filename search. It matches **names**, not contents. Content search is still `rg`.

Nobody remembers the full syntax for `find`. You type `find . -name *test*` without quotes, Bash expands the glob before `find` ever sees it, and things go downhill from there. `fd test` finds things named test.

#### Why switch
- **Intuitive syntax:** `fd pattern` searches recursively. No `-name`, no quotes for basic terms.
- **Respects `.gitignore`:** Skips `node_modules`, `venv`, and hidden folders by default.
- **Regex by default:** Or `-F` for a literal string.
- **Parallel command execution:** `-x` / `--exec` runs a command on each match.

#### The Debian/Ubuntu naming trap (again)
Like `bat`, `fd` collided on Debian (a floppy-disk utility also called `fd`). The binary is `/usr/bin/fdfind`. Symlink it the same way:

```sh
sudo apt install fd-find
mkdir -p ~/.local/bin
ln -sfn "$(command -v fdfind)" ~/.local/bin/fd
export PATH="$HOME/.local/bin:$PATH"
```

On Fedora and macOS the binary is already `fd`:

```sh
# Fedora
sudo dnf install fd-find

# macOS
brew install fd
```

#### Everyday use
```sh
# Find markdown files whose names match setup (e.g. setup.md)
fd setup -e md

# Find markdown files whose contents mention setup (this is rg, not fd)
rg -l setup -g '*.md'

# Find directories only
fd -t d build

# Preview what -x would run (dry). Then delete with a prompt, not in parallel unconfirmed:
fd -e bak -x echo
fd -e bak -x rm -i --
```

`basic_shell.md` spent a page on `-ok`, `rm --`, and "do not paste this into a directory you like." `fd -e bak -x rm` is a parallel unconfirmed delete. Do not make it an everyday one-liner.

---

### `sd`: find and replace

[`sd`](https://github.com/chmln/sd) does one job `sed` makes you punctuate: find a pattern, write a replacement. The [sed chapter](tools_sed.md) is still the one to practice. `sed` is on every server, and it can delete lines, hold a buffer, and print a stream. `sd` is what you type at your own keyboard when the task is "change this string."

The shape is `sd FIND REPLACE [files...]`. Find and replace are separate arguments, so a path full of slashes does not force you to pick a new delimiter. Regex is the JavaScript / Python flavor. A capture is `$1`, not `\1`.

#### The part that bites

- **A filename means in-place.** `sd old new notes.txt` rewrites `notes.txt`. There is no backup suffix to forget, and no prompt.
- **No filename means a pipe.** `echo hello | sd hello hi` prints `hi` and changes nothing on disk. That is the safe way to learn the pattern.
- **`-p` / `--preview` prints the result and does not write.** Read it. Then run the command again without `-p` if it is what you wanted.
- **Patterns are regex.** `sd 'file.txt' 'file.md' notes.txt` treats the dot as "any character." Literal text is `-F` / `--fixed-strings`.
- **Do not alias `sed` to `sd`.** A script that expected a stream editor will rewrite the file and continue as if it had printed.
- **Practice on a copy.** `banklist.csv` and `nginx.log` in this repo are the exercise data. Copy them into `scratch/` before `sd` sees them.

`man sd` before the package is installed opens the SCSI disk driver (manual section 4). After install, `sd --help` is the page you want. `man 1 sd` is the CLI manual.

#### Installation

Ubuntu 24.04, Mint 22, and current Debian ship `sd` in universe (package `sd`, command `sd`). That build is 1.0.0. It has `-p` and `-F`. It still reads the whole file into one buffer. Upstream 1.1 made line-by-line the default and added `-A` / `--across` for the old whole-file behavior. If an upstream example passes `-A` and `sd` complains about the flag, delete `-A` — you already have the old behavior. For a line of CSV the difference is invisible.

```sh
# Ubuntu / Debian / Mint
sudo apt install sd

# Fedora
sudo dnf install sd

# macOS
brew install sd
```

#### Everyday use

```sh
mkdir -p scratch && cd scratch
printf '%s\n' 'name,city' 'Ada,London' > people.csv

# Preview. The file on disk stays 'London'.
sd -p 'London' 'Lisbon' people.csv

# Write. -F so the text is literal, not a regex.
sd -F 'London' 'Lisbon' people.csv
cat people.csv

# A capture. $1 is the word before the comma.
printf '%s\n' 'Ada,London' | sd '(\w+),(\w+)' '$2 ($1)'

# Literal mode when the search text has dots or parentheses.
printf '%s\n' 'config.json' | sd -F '.json' '.yaml'
```

A replacement that itself starts with `-` needs `--`, or `sd` will try to parse it as a flag: `sd foo -- '-bar' file.txt`.

Across a tree, keep the preview habit and let `fd` pick the files. This rewrites every match. Run it in a git repo so `git diff` (and [delta](#using-delta-with-git)) can show you the damage:

```sh
fd -e md -x sd -p 'old-name' 'new-name'
fd -e md -x sd -F 'old-name' 'new-name'
```

---

### `zoxide` (`z`): `cd` that remembers where you have been

[`zoxide`](https://github.com/ajeetdsouza/zoxide) is a smarter `cd`, inspired by `z` and `autojump`.

`cd` requires you to know where you are and where you are going. `zoxide` tracks directory "frecency" (frequency + recency) **after you have visited a path**. Right after install the database is empty, so `z learn` does nothing useful. `cd` into `/home/user/projects/learning-shell` once. Later, from somewhere else, `z learn` jumps back.

`zi` (interactive picker when several directories match) needs [`fzf`](#fzf-fuzzy-find-anything) installed.

#### Why switch
- **Short jumps:** A few characters instead of `cd ../../../`.
- **Fuzzy matching:** `z doc work` matches `/home/user/documents/work-stuff`.
- **Interactive selection:** `zi` opens an `fzf` picker.

#### Installation
Prefer the distro package. Ubuntu 24.04 / Mint 22 already have it:

```sh
# Ubuntu / Debian / Mint
sudo apt install zoxide

# Fedora
sudo dnf install zoxide

# macOS
brew install zoxide
```

If you use the upstream installer, download and read it first (same rule as [starship in Shell customization](shell_customization.md#starshiprs)):

```sh
curl -fsSLo /tmp/zoxide-install.sh https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh
less /tmp/zoxide-install.sh
sh /tmp/zoxide-install.sh
# binary lands in ~/.local/bin — that directory must be on PATH
export PATH="$HOME/.local/bin:$PATH"
```

Init in the rc file for **your** shell (`getting_started.md` puts macOS users on zsh):

```sh
# ~/.bashrc
eval "$(zoxide init bash)"

# ~/.zshrc
eval "$(zoxide init zsh)"
```

#### Everyday use
```sh
# After you have cd'd there at least once: jump to the highest-scoring match
z shell

# Jump to a specific subfolder
z projects learning-shell

# Interactive picker (requires fzf)
zi
```

---

### `delta`: diffs you can read

[`delta`](https://github.com/dandavison/delta) is a pager. Git (and `diff`, and `rg`) already produce the diff. `delta` paints it: language highlighting, the word that changed instead of the whole line, line numbers, and — when the window is wide enough — two columns.

The package name is `git-delta`. The command is `delta`. You do not put it on your `PATH` as a replacement for the `diff` program, and you do not `alias diff='delta'`. Git calls it because `~/.gitconfig` says so.

Manual: [dandavison.github.io/delta](https://dandavison.github.io/delta/). `delta --help` is the short list; `delta --help | less` is the long one.

#### Installation

```sh
# Ubuntu / Debian / Mint (universe; binary is `delta`)
sudo apt install git-delta

# Fedora
sudo dnf install git-delta

# macOS
brew install git-delta

# Older distros: GitHub releases
# https://github.com/dandavison/delta/releases
```

`delta --version` should print a version. If the shell says `command not found`, the package did not land on `PATH`; open a new terminal before debugging git.

#### Using delta with git

This is the upstream starter, not a theme collection. It matches a dark terminal. On a light Mint theme, use `light = true` instead of `dark = true`, or delete that line and let delta look at the terminal.

```ini
# ~/.gitconfig
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    dark = true

[merge]
    conflictStyle = zdiff3

[pager]
    blame = delta
```

The same settings from the prompt, if you would rather not open the file. `user.name` and `user.email` are separate; the workstation chapter sets those in [linux-setup §5.4](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#git-and-the-github-cli).

```sh
git config --global core.pager delta
git config --global interactive.diffFilter 'delta --color-only'
git config --global delta.navigate true
git config --global delta.dark true
git config --global merge.conflictStyle zdiff3
git config --global pager.blame delta
```

What each line does:

| Setting | What you feel |
| --- | --- |
| `core.pager = delta` | `git diff`, `git show`, `git log -p`, and `git reflog -p` open in delta. |
| `interactive.diffFilter` | `git add -p` and `git checkout -p` stay colorful. `--color-only` keeps the interactive prompt usable. |
| `delta.navigate = true` | Inside a long diff, `n` jumps to the next file and `N` jumps back. `q` quits. Arrows still scroll. |
| `delta.dark` / `light` | Syntax colors that match the terminal. Wrong one looks washed out, not broken. |
| `merge.conflictStyle = zdiff3` | A conflict shows **ours**, **theirs**, and the **base** (the common ancestor). delta can paint that. |
| `pager.blame = delta` | `git blame path` is highlighted too. Blame does not go through `core.pager` on its own. |

`side-by-side = true` is the view people install delta for, and it needs a wide window (about 120 columns, or a full-screen terminal). A half-width VS Code panel will wrap each column into porridge. Turn it on when the window is wide:

```sh
git config --global delta.side-by-side true
git config --global delta.line-numbers true
```

One command, without saving that as your life:

```sh
git diff | delta --side-by-side
```

Themes are the same set as `bat`. List them, then pin one:

```sh
delta --show-syntax-themes --dark
git config --global delta.syntax-theme Dracula
```

Raw git, when you want to see exactly what an agent or a grader will see:

```sh
git --no-pager diff
git -c core.pager=diff diff
```

#### Try it in `scratch/`

Git will refuse the commit until `user.name` and `user.email` are set. Do that once, globally, then:

```sh
mkdir -p scratch/delta-try && cd scratch/delta-try
git init
printf '%s\n' 'hello' 'world' > notes.txt
git add notes.txt
git commit -m "start"
printf '%s\n' 'hello' 'world!' > notes.txt
git diff
git add -p          # press y to stage the hunk
git commit -m "punctuate"
git log -p -1
```

`git diff` with a clean pager config should highlight `!` rather than reprint the whole line in angry red. `q` leaves the pager. If the diff is shorter than the screen, delta may print and return immediately — that is success, not a missing install.

`git add -p` is a different prompt. It asks per hunk: `y` stage it, `n` skip it, `q` stop asking. The color comes from `interactive.diffFilter`. The keys are git's, not delta's. Press `y`, then commit, so the conflict exercise below starts from a clean `git status`.

A conflict, so `zdiff3` is a thing you have seen rather than a stanza you pasted. `main` is the default branch name on current git; if `git init` printed `master`, use that name in the checkout below.

```sh
git checkout -b other
printf '%s\n' 'hello' 'theirs' > notes.txt
git commit -am "theirs"
git checkout main
printf '%s\n' 'hello' 'ours' > notes.txt
git commit -am "ours"
git merge other
# look at notes.txt, then back out:
git merge --abort
```

`delta` also pages two files with no git at all: `delta old.txt new.txt`. And it can color search hits: `rg --json -C 2 hello | delta`. You can ignore that until `rg` itself feels boring.

#### Optional: lazygit

[`lazygit`](https://github.com/jesseduffield/lazygit) is a full-screen git menu: status, stage, commit, push. It is not a diff pager. delta still paints the diffs inside it if `core.pager` is set.

Ubuntu 24.04 and Mint 22 do not ship a `lazygit` package. Install from the project's own instructions, or skip it. Omarchy already includes it ([chapter 5b](https://github.com/amitsk/learning-linux/blob/main/docs/05b-omarchy.md)). `git` and `delta` are enough for coursework.

---

### `btop` & `bottom`: process dashboards

[`btop`](https://github.com/aristocratos/btop) and [`bottom`](https://github.com/ClementTsang/bottom) (`btm`) turn system monitoring into an interactive dashboard.

`top` is what you run when the machine is sad. `htop` added color bars. `btop` and `bottom` add CPU sparklines, memory graphs, disk I/O, network throughput, and mouse support so you can click a runaway process without hunting for its PID.

#### Installation
```sh
# Ubuntu / Debian / Mint
sudo apt install btop

# Fedora
sudo dnf install btop

# macOS
brew install btop
```

[`bottom`](https://github.com/ClementTsang/bottom) is the other dashboard. The command is `btm`. On Ubuntu / Mint the package is also `btm` (`sudo apt install btm`). On Fedora and Homebrew the package name is `bottom`. Install `btop` or `btm`, not both, until you have a reason.

`procs` is a colored `ps`. Ubuntu 24.04 and Mint 22 do not ship it, so this workstation uses `btop` for "what is running." Fedora: `sudo dnf install procs`. macOS: `brew install procs`.

---

### `duf` & `dust`: what ate the disk

- **`duf`** replaces `df -h` for eyeballs: physical and virtual drives in tables with usage bars, instead of a dump of tmpfs and loop mounts.
- **`dust`** replaces `du -sh * | sort -hr | head`: an ASCII bar chart of the directory tree.

#### Installation
```sh
# duf — Ubuntu 24.04 / Mint 22 have this
sudo apt install duf       # Ubuntu/Debian/Mint
sudo dnf install duf       # Fedora
brew install duf           # macOS

# dust — package name is du-dust on Debian/Ubuntu *after* 24.04.
# Noble/Mint 22 do not ship it; use Fedora, Homebrew, or GitHub releases.
sudo apt install du-dust   # Ubuntu 25.04+ / Debian (binary is `dust`)
sudo dnf install dust      # Fedora
brew install dust          # macOS
# https://github.com/bootandy/dust/releases
```

---

### `gping`: ping, as a graph

[`gping`](https://github.com/orf/gping) draws a live line of latency. `ping` still prints the numbers; `gping` is for the moment you want to *see* the Wi-Fi fall over while a laptop lid is half closed.

```sh
# Ubuntu / Debian / Mint
sudo apt install gping

# Fedora
sudo dnf install gping

# macOS
brew install gping

gping example.com
gping 1.1.1.1 example.com
```

`Ctrl+C` stops it, same as `ping`. It needs a network. It does not replace `ping` in a script that is parsing `time=` lines.

---

### `xh` / HTTPie: friendlier `curl` when you are typing

Raw `curl` is universal. Typing a JSON POST from memory (`curl -X POST -H "Content-Type: application/json" -d '{"username":"alice"}' https://...`) is an exercise in escaping quotes.

Keep `curl` as the portable default (scripts, agents, CI). When you are at the keyboard, [`xh`](https://github.com/ducaale/xh) and [HTTPie](https://httpie.io/) use a friendlier syntax and pretty-print JSON so you do not have to pipe into `jq` just to see if the request worked.

[`curlie`](https://github.com/rs/curlie) is optional sugar: HTTPie-shaped flags that exec real `curl` underneath (proxies and all).

```sh
# Fedora / Homebrew; on Ubuntu/Mint, see HTTPie in linux-setup §5.11 or GitHub releases for xh
# brew install xh httpie
# sudo dnf install xh httpie

xh https://httpbin.org/get
xh POST https://httpbin.org/post name="Alice" role="Admin"
```

---

### `tldr`: examples first, `man` when you need every flag

The man page for `tar` is over 1,000 lines and still discusses magnetic tape. The command you wanted is `tar -xzf`. [`tldr`](https://tldr.sh/) prints a handful of practical examples. It complements `man`; it does not replace it. When a flag is weird, read the manual.

On Ubuntu 24.04 / Mint 22, `sudo apt install tldr` is a dummy package that pulls in `tldr-hs`. The binary is `/usr/bin/tldr-hs`, so `tldr` fails. Install **tealdeer** instead; it provides `/usr/bin/tldr`.

```sh
# Ubuntu / Debian / Mint (binary is tldr)
sudo apt install tealdeer

# Fedora
sudo dnf install tealdeer

# macOS (tlrc is the tldr-pages client)
brew install tlrc

tldr --update
tldr tar
tldr ffmpeg
tldr rsync
```

---

### `fzf`: fuzzy-find anything

If you install only one tool from this chapter, make it [`fzf`](https://github.com/junegunn/fzf). It turns any text list into an interactive search prompt.

- **`Ctrl+R`:** Live fuzzy window over bash/zsh history.
- **`Alt+C`:** Fuzzy-jump into subdirectories.
- **Pipes:** Pipe anything in. Branch checkout needs a clean ref list — `git branch` prefixes `*` and pads spaces, so `git checkout $(git branch | fzf)` tries to check out `* main` and fails.

```sh
git checkout "$(git branch --format='%(refname:short)' | fzf)"
```

```sh
# Ubuntu / Debian / Mint
sudo apt install fzf

# Fedora
sudo dnf install fzf

# macOS
brew install fzf
```

Later, [Atuin](https://docs.atuin.sh/) can replace that history window with a searchable database. Learn `Ctrl+R` here first. Atuin is another program your shell starts on every prompt; it can wait until history search is a habit.

Enable keybindings. **Pick the block for your install.** Ubuntu 24.04 / Mint 22 ship fzf 0.44, which does not have `fzf --bash` (that arrived in 0.48). Unguarded `source` of a missing file errors on every new shell.

```sh
# Ubuntu 24.04 / Mint 22 (apt fzf 0.44) — bash
[ -f /usr/share/doc/fzf/examples/key-bindings.bash ] && source /usr/share/doc/fzf/examples/key-bindings.bash
[ -f /usr/share/doc/fzf/examples/completion.bash ] && source /usr/share/doc/fzf/examples/completion.bash

# Fedora (older packages) — bash
[ -f /usr/share/fzf/shell/key-bindings.bash ] && source /usr/share/fzf/shell/key-bindings.bash
[ -f /usr/share/fzf/shell/completion.bash ] && source /usr/share/fzf/shell/completion.bash

# fzf 0.48+ only (Homebrew, newer distros). Do not eval this on Ubuntu 24.04 / Mint 22.
# eval "$(fzf --bash)"    # ~/.bashrc
# eval "$(fzf --zsh)"     # ~/.zshrc
```

---

## 3. Aliases for these tools

An alias is a nickname the **interactive** shell expands before it runs anything. `alias ll='eza -l --git'` means "when I type `ll` in this terminal, run that." How aliases work in general — listing them, removing one, bypassing one with `\ls` — is [Shell customization: Aliases](shell_customization.md#aliases). This section is the list for *these* tools.

Put the block in `~/.bashrc` or `~/.zshrc`, then `source` that file or open a new terminal. Non-interactive scripts do not read those files, which is why a careful alias stays out of `#!/bin/bash` scripts. An agent that runs `bash -lc` *does* read them. Keep the nicknames boring enough that a script surviving contact with your rc file still does the right thing.

`z` is not an alias. `eval "$(zoxide init bash)"` defines a shell function. Typing `alias z` will not show it. `type z` will.

`gs` below is a **shell** alias. `git gs` does not use it. Git has its own alias table (`git config --global alias.st status`, then `git st`). Pick one style so you do not spend an evening debugging the wrong one.

### The list

| You type | What runs | Use it? |
| --- | --- | --- |
| `ls` | `eza` | Yes, interactive. `\ls` or `command ls` is real `ls`. |
| `ll` | `eza -l --git` | Yes. Add `--icons` only after a [Nerd Font](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#coding-fonts-nerd-fonts-banishing-the-tofu) is the terminal font. |
| `la` | `eza -la --git` | Yes. Hidden files included. |
| `lt` | `eza --tree --level=2` | Yes. |
| `preview` | `bat` | Yes. Paging, line numbers, git gutter. |
| `cat` | `bat --paging=never` | Optional. `--paging=never` stops the "press q" hang. Color can still surprise a pipe. Leave `cat` alone if you pipe it. |
| `bat` | `batcat` | Ubuntu/Debian/Mint only, and only when `bat` is not already on `PATH`. |
| `fd` | `fdfind` | Same story. Fedora and Homebrew already install `fd`. |
| `rg` | `rg` | No alias. The package `ripgrep` installs `rg`. |
| `z` / `zi` | zoxide's init function | Not an alias. `zi` needs `fzf`. |
| `top` | `btop` | Optional. `btop` is a dashboard; scripts that parse `top -b` must call `command top`. |
| `df` | `duf` | Optional. `duf` drops a lot of tmpfs noise, which is what some scripts were counting. |
| `du` | `dust` | Optional, and not in Ubuntu 24.04 / Mint 22 repos. |
| `gs` | `git status` | Optional. Expands when `gs` is the whole command. `git gs` looks up a git alias instead. |
| `gd` | `git diff` | Optional. Runs delta if [git is configured to](#using-delta-with-git). |
| `sed`, `grep`, `find`, `diff` | the classic tool | **Leave these.** `sd` rewrites files, `rg` skips ignored paths, `fd` is not `find`'s language, `delta` is a pager. |

`delta` has no shell alias. Git learns it through `~/.gitconfig`.

### Copy-paste block

```sh
# ~/.bashrc or ~/.zshrc — interactive nicknames for modern tools
# Reload: source ~/.bashrc    (or source ~/.zshrc)

# Ubuntu/Debian name collisions (before anything uses `bat` / `fd`)
if command -v batcat >/dev/null && ! command -v bat >/dev/null; then
    alias bat='batcat'
fi
if command -v fdfind >/dev/null && ! command -v fd >/dev/null; then
    alias fd='fdfind'
fi

# bat: `preview` is the full pager. Aliasing cat is optional.
if command -v bat >/dev/null; then
    alias preview='bat'
    # alias cat='bat --paging=never'
fi

# eza. --icons needs a Nerd Font; leave it off until glyphs look like glyphs.
if command -v eza >/dev/null; then
    alias ls='eza'
    alias ll='eza -l --git'
    alias la='eza -la --git'
    alias lt='eza --tree --level=2'
    # alias ll='eza -l --icons --git'
fi

# Optional dashboards. Skip any you did not install.
if command -v btop >/dev/null; then
    alias top='btop'
fi
if command -v duf >/dev/null; then
    alias df='duf'
fi
if command -v dust >/dev/null; then
    alias du='dust'
fi

# Shell nicknames. `gs` runs git status. `git gs` looks up a git alias named gs.
alias gs='git status'
alias gd='git diff'

# zoxide — init matches the file you are editing. `z` is a function, not an alias.
if command -v zoxide >/dev/null; then
    eval "$(zoxide init bash)"    # ~/.bashrc
    # eval "$(zoxide init zsh)"   # ~/.zshrc
fi
```

Check what you actually defined:

```sh
alias | grep -E 'ls|ll|bat|fd|top|df|gs'
type z
command ls --version | head -1    # GNU ls, even if ls is aliased to eza
```

---

## 4. Next Steps & Where to Go

| Topic | Link |
| --- | --- |
| Set up a complete developer workstation (includes an `apt install` of these tools) | [linux-setup: 5a. Development Workstation](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#modern-replacements-for-unix-utilities) |
| What an alias is, and how to bypass one | [Shell customization: Aliases](shell_customization.md#aliases) |
| `sed` on a real CSV (keep using `sed` for the exercises) | [sed, sort, uniq](tools_sed.md) |
| `jq` on JSON from `curl` (the matrix row has no chapter of its own) | [linux-setup: jq and yq](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#512-jq-and-yq) |
| Automate tasks with Make and Ninja | [learning-shell: Build Systems](build_systems.md) |
| Work with logs and CSVs | [AWK Tutorial](tools_awk.md) · [sed, sort, uniq](tools_sed.md) |
| Practice bash scripting | [Bash Scripting Basics](tools_bash.md) |
