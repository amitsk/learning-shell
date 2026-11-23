# Modern Replacements for Unix Utilities

[← Two-hour path](../README.md#if-you-are-new) | [What an alias is](shell_customization.md#aliases)

**About 15 minutes. Done when** you have run one command from the table and, if you want the nicknames, pasted the alias block into `~/.bashrc` or `~/.zshrc`.

Classic tools (`cat`, `ls`, `grep`, `find`, `sed`) are on every server and in every script. The tools below are for the terminal you are looking at. They are nicer there. A Dockerfile still wants `grep`.

The workstation install line also lives in [linux-setup §5.4](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#modern-replacements-for-unix-utilities).

## 0. If you are new, install three tools

`fzf`, `ripgrep` (`rg`), and `bat`. Add `delta` when `git diff` is a wall of color. Add `sd` when the same string needs to change in a pile of files. The rest of the table can wait.

```sh
# Ubuntu / Debian / Mint
sudo apt install bat ripgrep fd-find fzf eza zoxide git-delta sd btop duf tealdeer gping

# Fedora (also has dust, which Mint 22 does not)
# sudo dnf install bat ripgrep fd-find fzf eza zoxide git-delta sd btop duf tealdeer gping dust
```

On macOS, `brew install` the command names: `bat fd ripgrep fzf eza zoxide git-delta sd btop duf gping tlrc`.

If an agent later writes `cat`, `grep`, `find`, or `sed` in a script, leave those names alone.

---

## 1. The table

One command each. Upstream docs are a click away when a flag is weird.

| Job | Classic | Try this | Tool |
| --- | --- | --- | --- |
| View a file | `cat` | `bat README.md` | [bat](https://github.com/sharkdp/bat) |
| List a directory | `ls` | `eza -l --git` | [eza](https://eza.rocks/) |
| Search inside files | `grep` | `rg TODO` | [ripgrep](https://github.com/BurntSushi/ripgrep) |
| Search filenames | `find` | `fd notes -e md` | [fd](https://github.com/sharkdp/fd) |
| Find and replace | `sed` | `sd -p 'old' 'new' file.txt` | [sd](https://github.com/chmln/sd) |
| Jump to a directory | `cd` | `z proj` | [zoxide](https://github.com/ajeetdsouza/zoxide) |
| Read a diff | `git diff` | `git diff` after the config below | [delta](https://github.com/dandavison/delta) |
| Fuzzy-pick a line | `Ctrl+R` | `Ctrl+R` after the fzf lines below | [fzf](https://github.com/junegunn/fzf) |
| Examples, short | `man` | `tldr tar` | [tldr](https://tldr.sh/) |
| Watch processes | `top` | `btop` | [btop](https://github.com/aristocratos/btop) |
| Disk free | `df` | `duf` | [duf](https://github.com/muesli/duf) |
| What ate the disk | `du` | `dust` | [dust](https://github.com/bootandy/dust) |
| Ping as a graph | `ping` | `gping example.com` | [gping](https://github.com/orf/gping) |
| JSON from an API | — | `curl -s URL \| jq .` | [jq](https://jqlang.org/) · [linux-setup](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#512-jq-and-yq) |
| Friendlier HTTP | `curl` | `xh https://httpbin.org/get` | [xh](https://github.com/ducaale/xh) |

`z` remembers directories only after you have `cd`'d there once. `jq` is the tool for JSON; the sed chapter still owns CSV.

---

## 2. Traps

A few names lie. Everything else in the table is `apt install` and the command you just read.

**`bat` and `fd` on Debian, Ubuntu, and Mint.** The packages are `bat` and `fd-find`. The commands are `batcat` and `fdfind`, because older tools already took the short names. The alias block below fixes that. Or, once:

```sh
mkdir -p ~/.local/bin
ln -sfn /usr/bin/batcat ~/.local/bin/bat
ln -sfn "$(command -v fdfind)" ~/.local/bin/fd
export PATH="$HOME/.local/bin:$PATH"
```

**`tldr`.** `sudo apt install tldr` pulls in a different program and the `tldr` command is missing. `tealdeer` is the package that provides `tldr`. Then `tldr --update` once.

**`dust`.** Not in Ubuntu 24.04 or Mint 22. Skip it, or use a Fedora/Homebrew machine. `duf` and `btop` are in the Mint repos. [`bottom`](https://github.com/ClementTsang/bottom) is the other process view: package `btm`, command `btm`. `procs` and [`lazygit`](https://github.com/jesseduffield/lazygit) are not in those repos; the table is enough until you want them.

**`fzf` keys on Mint 22.** Ubuntu 24.04 ships fzf 0.44, which has no `fzf --bash`. This is the block that makes `Ctrl+R` fuzzy:

```sh
# ~/.bashrc  — Ubuntu 24.04 / Mint 22
[ -f /usr/share/doc/fzf/examples/key-bindings.bash ] && source /usr/share/doc/fzf/examples/key-bindings.bash
[ -f /usr/share/doc/fzf/examples/completion.bash ] && source /usr/share/doc/fzf/examples/completion.bash
```

Ubuntu 26.04+, Fedora, and Homebrew ship fzf 0.48+, where `eval "$(fzf --bash)"` (or `--zsh`) replaces both lines above. Leave that line commented on Mint 22 / Ubuntu 24.04.

### `sd`: find and replace

`sd 'old' 'new'` prints. `sd 'old' 'new' file.txt` rewrites the file. `sd -p` prints the result and leaves the file alone. Patterns are regex; `-F` is literal text, which is what you want for a filename or a dot.

```sh
printf '%s\n' 'Ada,London' > scratch/people.csv
sd -p 'London' 'Lisbon' scratch/people.csv
sd -F 'London' 'Lisbon' scratch/people.csv
```

Mint's package is `sd` 1.0.0. It has `-p` and `-F`. It does not have upstream 1.1's `-A` flag; if a README example fails on `-A`, delete that flag. `man sd` before the package is installed opens the SCSI disk driver. After install, `sd --help` is the page you want.

Practice on a copy. `banklist.csv` in this repo is exercise data.

The sed chapter is still the one to practice. `sed` deletes lines and prints a stream. `sd` changes a string. Leave the `sed` command itself unaliased.

### Using delta with git

`delta` paints `git diff`. The package is `git-delta`. The command is `delta`. It is a pager, so it lives in `~/.gitconfig`, not in an alias.

```sh
git config --global core.pager delta
git config --global interactive.diffFilter 'delta --color-only'
git config --global delta.navigate true
git config --global delta.dark true          # light terminal: delta.light true
git config --global merge.conflictStyle zdiff3
```

Then `git diff`. A changed word gets highlighted. `q` quits. In a long diff, `n` and `N` move between files. `git add -p` uses the same colors and its own keys: `y`, `n`, `q`.

Side-by-side wants a wide window. Turn it on when you have one:

```sh
git config --global delta.side-by-side true
```

Raw git, when you want the unpainted diff: `git --no-pager diff`.

Manual: [dandavison.github.io/delta](https://dandavison.github.io/delta/).

---

## 3. Aliases for these tools

Nicknames for the interactive shell. Scripts with `#!/bin/bash` do not read `~/.bashrc`. [Shell customization](shell_customization.md#aliases) is how aliases work in general: list them, remove one, bypass one with `\ls`.

`z` comes from `zoxide init`, so it is a function. `type z` shows it. `alias z` does not.

`gstatus` is the nickname for `git status`. `gs` is Ghostscript on a lot of Linux machines (`GS>` is the prompt). An alias named `gs` hides it. `git gstatus` does nothing either: git has its own alias list (`git config --global alias.st status`, then `git st`).

| You type | What runs | Use it? |
| --- | --- | --- |
| `ls` | `eza` | Yes. `\ls` is real `ls`. |
| `ll` | `eza -l --git` | Yes. `--icons` wants a [Nerd Font](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#coding-fonts-nerd-fonts-banishing-the-tofu) first. |
| `la` | `eza -la --git` | Yes. |
| `lt` | `eza --tree --level=2` | Yes. |
| `preview` | `bat` | Yes. |
| `bat` / `fd` | `batcat` / `fdfind` | Only on Debian-family machines where the short name is missing. |
| `gstatus` | `git status` | Yes. |
| `gd` | `git diff` | Yes. Uses delta once git is configured. |
| `sed`, `grep`, `find`, `diff`, `cat` | the classic tool | Leave them. `sd` rewrites files, `rg` skips ignored paths, `delta` is a pager, `bat` will page. |

```sh
# ~/.bashrc or ~/.zshrc
# Reload: source ~/.bashrc

if command -v batcat >/dev/null && ! command -v bat >/dev/null; then
    alias bat='batcat'
fi
if command -v fdfind >/dev/null && ! command -v fd >/dev/null; then
    alias fd='fdfind'
fi

if command -v bat >/dev/null; then
    alias preview='bat'
fi

if command -v eza >/dev/null; then
    alias ls='eza'
    alias ll='eza -l --git'
    alias la='eza -la --git'
    alias lt='eza --tree --level=2'
fi

alias gstatus='git status'
alias gd='git diff'

if command -v zoxide >/dev/null; then
    eval "$(zoxide init bash)"    # ~/.bashrc
    # eval "$(zoxide init zsh)"   # ~/.zshrc
fi
```

```sh
alias ll
type z
command ls --version | head -1
```

---

## 4. When you want the long version

| You have… | Open |
| --- | --- |
| A CSV | [sed, sort, uniq](tools_sed.md) |
| A log file | [awk](tools_awk.md) |
| A script to write | [Bash scripting](tools_bash.md) |
| A project to compile | [Make and Ninja](build_systems.md) |
| A new Linux desktop | [linux-setup, workstation](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#modern-replacements-for-unix-utilities) |
