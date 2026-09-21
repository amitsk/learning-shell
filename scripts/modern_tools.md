# Modern Replacements for Unix Utilities

[← Back: Build Systems: Make and Ninja](build_systems.md)

Classic Unix utilities (`cat`, `ls`, `grep`, `find`, `df`, `du`, `top`) were designed in an era when terminals had 80 columns, RAM was measured in kilobytes, and a screen that could display 16 colors felt like witchcraft. They are universal, battle-tested, and will still be running on government satellites when we are all dust. You must know them.

On a personal workstation, the 1979 defaults are a bit like commuting on a penny-farthing: historically venerable, and hard on the knees.

A newer set of interactive tools — many written in Rust or Go — add syntax highlighting, git awareness, sensible ignore rules, and parallelism at the prompt. They are nicer to look at. They do not replace `ls` on a server you SSH into for five minutes.

> **In the age of AI:** Coding agents and CI runners speak standard POSIX. If an LLM writes a shell script or a Makefile with `cat`, `grep`, and `find`, leave them alone. Do not alias `cat` to `bat` in an automated pipeline or an Alpine Docker container — `bat` will happily send ANSI escape sequences and launch an interactive pager inside a headless CI job, where it will hang in the dark waiting for a human finger to press `q`.
>
> **The Golden Rule:** Use the **classic utilities** (`cat`, `grep`, `find`) in scripts, Makefiles, Dockerfiles, and agent instructions. Use the **modern replacements** (`bat`, `ripgrep`, `fd`, `zoxide`) interactively when your actual eyeballs are looking at the glass.

---

## 1. The Upgrade Matrix

These are interactive upgrades, not drop-in replacements. Keep the classic tool for scripts and remote boxes.

| Job | Classic | Interactive upgrade | Language | Why bother | Link |
| --- | --- | --- | --- | --- | --- |
| View a file | `cat` / `less` | `bat` | Rust | Syntax highlighting, line numbers, git gutters, automatic paging | [sharkdp/bat](https://github.com/sharkdp/bat) |
| List a directory | `ls` | `eza` | Rust | Colors by file type, git status column, icons, built-in tree view | [eza-community/eza](https://github.com/eza-community/eza) · [eza.rocks](https://eza.rocks/) |
| Search file contents | `grep` | `ripgrep` (`rg`) | Rust | Fast, respects `.gitignore`, skips binaries without asking | [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| Search filenames | `find` | `fd` | Rust | `fd pattern` instead of `-name`, ignores gitignored junk, parallel `-x` | [sharkdp/fd](https://github.com/sharkdp/fd) |
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

[`delta`](https://github.com/dandavison/delta) is a syntax-highlighting pager for git, `diff`, and `grep` output.

Standard `git diff` is a wall of green and red. `delta` highlights the word that changed, applies language highlighting, and can show side-by-side views.

#### Installation & Git config
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

Add this to `~/.gitconfig`:

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true
    side-by-side = true
    line-numbers = true
    syntax-theme = Dracula
```

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

## 3. Sane Aliases (Without Sabotaging Your Scripts)

Aliasing `cat='bat'` is like adopting a lively puppy: great fun until it chews through something important.

If you put `alias cat='bat'` in a script, `bat` will page output or emit ANSI color when a pipeline expected bytes. The script will hang or break.

Keep aliases in interactive `~/.bashrc` or `~/.zshrc` (non-interactive scripts do not source those), and tell tools to behave when piped:

```sh
# ~/.bashrc or ~/.zshrc — safe interactive upgrades

# Ubuntu/Debian name collisions (before anything uses `bat` / `fd`)
if command -v batcat >/dev/null && ! command -v bat >/dev/null; then
    alias bat='batcat'
fi
if command -v fdfind >/dev/null && ! command -v fd >/dev/null; then
    alias fd='fdfind'
fi

# bat: disable paging when used as cat, keep full bat for preview
if command -v bat >/dev/null; then
    alias cat='bat --paging=never'
    alias preview='bat'
fi

# eza: muscle memory. --icons needs a Nerd Font; leave it off until you have one.
if command -v eza >/dev/null; then
    alias ls='eza'
    alias ll='eza -l --git'
    alias la='eza -la --git'
    alias lt='eza --tree --level=2'
    # after a Nerd Font is installed:
    # alias ll='eza -l --icons --git'
fi

# zoxide — use the init for the file you are editing
if command -v zoxide >/dev/null; then
    eval "$(zoxide init bash)"    # ~/.bashrc
    # eval "$(zoxide init zsh)"   # ~/.zshrc
fi
```

---

## 4. Next Steps & Where to Go

| Topic | Link |
| --- | --- |
| Set up a complete developer workstation (includes an `apt install` of these tools) | [linux-setup: 5a. Development Workstation](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#modern-replacements-for-unix-utilities) |
| Automate tasks with Make and Ninja | [learning-shell: Build Systems](build_systems.md) |
| Work with logs and CSVs | [AWK Tutorial](tools_awk.md) · [sed, sort, uniq](tools_sed.md) |
| Practice bash scripting | [Bash Scripting Basics](tools_bash.md) |
