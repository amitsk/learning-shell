# Shell Customization Guide

[← The light path](basic_shell.md#the-light-path-ends-here) | [Next: aliases →](#aliases)

Your prompt is a way to see `user@host:directory` before you type `rm`. This file is also rc files, `PATH`, `sudo`, and two prompt themes.

**About 20 minutes. Done when** you have added an alias, seen it with `alias`, and reloaded the shell.

The visit is [Aliases](#aliases) and [Reloading](#6-reloading-your-config). [sudo](#1-understanding-sudo-superuser-do), a hand-built prompt, Starship, and Oh My Posh are optional pages in this same file.

---

## 0. Your Home Directory & Shell Basics

### What is the Home Directory?

`~` is the shortest path you will type all day. It is your personal space for files and config: `/home/username` on Linux, `/Users/username` on macOS. `cd` with no arguments is a homing pigeon.

```sh
cd ~
# or simply
cd
```

### Who Am I? (Finding Your Username)

For when you have too many terminals and have lost the plot:

```sh
whoami
```

### Identifying Your Current Shell

`$SHELL` is your *login* shell — a polite fiction if you typed `bash` after zsh started. `ps -p $$` is who is actually answering.

```sh
echo $SHELL
# or
ps -p $$
```

---

## 1. Understanding sudo (Superuser Do)

Optional page. The 20-minute visit is [aliases](#aliases).

[`sudo`](https://www.sudo.ws/docs/man/sudo.man/) ("superuser do") runs *one* command as root (or another user via `sudo -u`) and logs who did it — safer than hanging out in a root shell you forget to exit, though cold comfort after a typoed `sudo rm`.

```sh
sudo apt update && sudo apt install htop   # install a package (then run `htop` without sudo)
sudo nano /etc/hosts                       # edit a system config
sudo systemctl restart nginx               # manage a service
sudo -u postgres psql                      # run as a specific user, not root
sudo -l                                    # what are you actually allowed to run?
sudo -k                                    # drop the ~15-minute password cache right now
```

Rules to live by:

- **Only `sudo` what needs it.** `htop` and `git status` do not need root privileges.
- **Read twice** before pressing Enter on `sudo` + `rm`, `chmod`, or any path starting with `/`.
- **Edit `/etc/sudoers` only with `sudo visudo`** — it checks syntax before letting you lock yourself out.
- **Never `sudo` an untrusted script**, including one an agent wrote and you only skimmed.

---

## 2. Shell Configuration Files

Interactive settings (aliases, prompt, `set -o vi`) go in the `*rc` file. Login-once settings (environment, `PATH`) go in the profile. The difference has confused everyone once. When in doubt, put it in `~/.bashrc` (or `~/.zshrc`) and [reload](#6-reloading-your-config).

### Bash

- `~/.bashrc`: Runs for interactive non-login shells. Place aliases, functions, and prompt customizations here.
- `~/.bash_profile` or `~/.profile`: Runs for login shells. Use for environment variables and commands that should run once per session.
- `~/.bash_logout`: Commands to run on shell exit.

### Zsh

- `~/.zshrc`: Main config for interactive shells. Place aliases, functions, and prompt customizations here.
- `~/.zprofile`: Runs for login shells.
- `~/.zlogout`: Commands to run on shell exit.

---

## 3. Common Customizations

### Aliases

An alias is a nickname. You type a short word; the interactive shell swaps in a longer command before it runs. Put aliases in `~/.bashrc` or `~/.zshrc`. They are for fingers on a keyboard. A script with `#!/bin/bash` does not load that file, so it will not see them — unless something runs the script as a login shell (`bash -lc`), in which case it will. Write scripts as if the nicknames do not exist.

```sh
alias ll='ls -lFh'
alias gstatus='git status'
alias mv='mv -i'
alias rm='rm -i'
alias cp='cp -i'
```

The `-i` flag is a seatbelt on `mv` / `rm` / `cp`: it asks before clobbering. Keep those seatbelts in `~/.bashrc` — a non-interactive script that inherits `rm -i` will hang forever waiting for someone to type `y`.

`gstatus` is a shell nickname for `git status` (git has its own alias list via `git config --global alias.st status`, so `git gstatus` won't work). Avoid aliasing `gs` — on many Linux boxes that hides Ghostscript. [Modern tools](modern_tools.md#3-aliases-for-these-tools) has the nicknames for `eza`, `bat`, and `fd`.

#### See them, drop one, bypass one

```sh
alias                 # every alias in this shell
alias ll              # just that one
unalias ll            # gone until you source the rc file again
\ls                   # skip the alias, run the real ls
command ls            # same idea, and it works when the name is a function too
type ll               # alias, function, or real program?
```

`\ls` is the one to remember the day `ls` has become `eza` and a homework checker wanted GNU `ls`.

#### Quoting

Single quotes on the right-hand side mean "store this text, do not expand it now."

```sh
alias today='date +%F'          # %F is for date, later
alias home="echo $HOME"         # $HOME is expanded once, when the alias is defined
```

Prefer single quotes unless you have a reason to bake in today's value.

#### Alias or function

An alias does not take arguments of its own. Everything you type after the nickname is stuck on the end. With the `ll` above, `ll /tmp` becomes `ls -lFh /tmp`, which is what you want. The moment the extra words need to go in the *middle* of the command, or you need an `if`, write a function. `mkcd` below is a function because `mkdir` and `cd` are two commands and `$1` has to land in both.

Reload after editing: [section 6](#6-reloading-your-config).

#### What not to alias

Leave `sed`, `grep`, `find`, and `diff` pointing at the classic tools. `sd` rewrites files in place, `rg` skips gitignored paths, `fd` does not speak `find`'s flag language, and `delta` is a pager git should call from `~/.gitconfig`. The reasoned list, with a copy-paste block, is [Modern tools: Aliases](modern_tools.md#3-aliases-for-these-tools).

### Functions

A tiny program you keep in the rc file. `mkcd` is the one everyone writes eventually: make the directory, then go there.

```sh
mkcd() {
  mkdir -p "$1" && cd "$1"
}
```

### Environment Variables

Key-value pairs that child processes inherit once you `export` them (`env` lists all of them; `echo "$VAR"` prints one). `PATH` is a scavenger hunt where first match wins — put `~/bin` at the front if you want *your* `hello` to beat `/usr/bin/hello`.

```sh
export EDITOR=nano
export PATH="$HOME/bin:$PATH"
export HISTSIZE=10000
export HISTFILESIZE=20000
```

### Personal Bin Directory

Your scripts, your `PATH`, no `sudo` required — the grown-up upgrade from keeping `run this.sh` on your Desktop:

```sh
mkdir -p ~/bin
printf '%s\n' '#!/bin/bash' 'echo "Hello from my script!"' > ~/bin/myscript
chmod +x ~/bin/myscript
myscript   # runs from anywhere once ~/bin is on PATH
```

### Vi Mode

`Esc` then `hjkl` right on the command line (`set -o vi` in `~/.bashrc`). Full tour in [Text editors](text_editors.md#7-vim-and-emacs-modes-in-the-shell-and-in-coding-agents).

---

## 4. Prompt Customization

Optional page. Aliases and a reload were the visit. A custom prompt is a hobby you can start when the default one feels rude.

Those backslash codes are not regex. They are prompt hieroglyphs. The `\[ \]` around colors is how Bash knows those characters are zero-width for line wrapping. Skip them and wrapping gets drunk.

### Bash Prompt (PS1)

```sh
export PS1="\u@\h:\w$ "
```

- `\u` = username
- `\h` = hostname
- `\w` = current directory

**Example with colors:**

```sh
export PS1="\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]$ "
```

### Zsh Prompt

Zsh uses `PROMPT` instead of `PS1`. Different letters, same idea.

```sh
export PROMPT="%n@%m:%~$ "
```

When the hieroglyphs stop being fun, skip to starship or Oh My Posh below.

---

## 5. Advanced Prompt: starship.rs and Oh My Posh

`PS1` is fine until you want Git branch, language versions, and command duration without writing a novel of escape codes. **[starship.rs](https://starship.rs/)** (minimal, Rust) and **[Oh My Posh](https://ohmyposh.dev/)** (colorful Powerline themes, Go) both work across Bash, Zsh, Fish, and PowerShell. **Pick one** — enabling both makes them fight over every keypress.

Both want a [Nerd Font](https://github.com/amitsk/learning-linux/blob/main/docs/05-dev-workstation.md#coding-fonts-nerd-fonts-banishing-the-tofu) in your terminal so icons don't render as empty boxes. Prefer a package manager or read any remote install script before running it.

### starship.rs

- **Install:** Follow the [Starship install guide](https://starship.rs/guide/#%F0%9F%9A%80-installation) (Homebrew, `dnf`/`cargo`, or download-and-review `https://starship.rs/install.sh`).
- **Activate** (end of `~/.bashrc` or `~/.zshrc`, then `source` it):

  ```sh
  eval "$(starship init bash)"   # or: eval "$(starship init zsh)"
  ```

- **Customize:** Edit `~/.config/starship.toml` or apply a [preset](https://starship.rs/presets/) (e.g., `starship preset nerd-font-symbols -o ~/.config/starship.toml`).

### Oh My Posh

- **Install:** Follow the [Oh My Posh install docs](https://ohmyposh.dev/docs/installation/linux) (Homebrew, `winget`, or download-and-review `https://ohmyposh.dev/install.sh` — on WSL, install the Linux binary and ensure `~/.local/bin` or `~/bin` is on `PATH`).
- **Activate** (last line of `~/.bashrc` or `~/.zshrc`):

  ```sh
  eval "$(oh-my-posh init bash --config ~/.cache/oh-my-posh/themes/jandedobbeleer.omp.json)"
  # zsh: swap `bash` for `zsh`; PowerShell: oh-my-posh init pwsh | Invoke-Expression
  ```

- **Themes:** Browse screenshots in the [theme gallery](https://ohmyposh.dev/docs/themes), preview your active config with `oh-my-posh print preview`, or export a copy to tweak (`oh-my-posh config export --output ~/.mytheme.omp.json`). Passing a local file is faster than fetching a URL on shell startup.

---

## 6. Reloading Your Config

Editing the rc file does nothing to the shell that is already open. `source` is "read this file again." Or open a new terminal, which is the same idea with more ceremony.

```sh
source ~/.bashrc
# or
source ~/.zshrc
```

---

## References

- [Bash Prompt HOWTO](https://tldp.org/HOWTO/Bash-Prompt-HOWTO/)
- [starship.rs Documentation](https://starship.rs/)
- [Oh My Posh Documentation](https://ohmyposh.dev/)
- [Zsh Guide](https://zsh.sourceforge.io/Guide/zshguide.html)

[← Two-hour path](../README.md#if-you-are-new) | [Modern tools table →](modern_tools.md)
