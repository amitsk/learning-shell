# Shell Customization Guide

[← Back: Bash Scripting Basics](tools_bash.md) | [Next: sed, sort, uniq, and More →](tools_sed.md)

Your prompt is not a personality. It is a way to see `user@host:directory` before you type `rm`. This chapter is rc files, aliases, `PATH`, `sudo`, and a prompt that tells the truth.

> **In the age of AI:** Agents inherit your aliases and your `PATH`. An `alias rm='rm -i'` that saves *you* will hang a non-interactive script that expected GNU `rm` to just delete. Keep surprise aliases for your interactive shell; keep generated scripts boring. And never `curl | sh` an installer you have not read — including the one that customizes this prompt.

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

`sudo` is not a spicy prefix. It is "run this as someone who can break the machine." Prefer it over logging in as root; still read the command.

### What is sudo?

- `sudo` stands for "superuser do" or "substitute user do"
- It lets permitted users run a command as root (or another user) without staying root
- Safer than a root login, and it leaves an audit trail — which is cold comfort after `sudo rm`, but better than no trail

### Basic Usage

```sh
# Run a command as root
sudo command

# Install packages (on Ubuntu/Debian)
sudo apt update
sudo apt install package-name

# Edit system files
sudo nano /etc/hosts

# View root's files
sudo ls -la /root

# Run a command as a specific user
sudo -u username command
```

`sudo -u` is "do this as them," not "do this as god." Useful, less exciting.

### Common sudo Tasks

**File operations requiring elevated privileges:**
```sh
# Copy files to system directories
sudo cp file.conf /etc/

# Change ownership of files
sudo chown user:group filename

# Modify file permissions
sudo chmod 644 /etc/somefile

# Create directories in system locations
sudo mkdir /opt/myapp
```

**System management:**
```sh
# Restart services
sudo systemctl restart nginx
sudo service apache2 restart

# View system logs
sudo tail -f /var/log/syslog

# Mount filesystems
sudo mount /dev/sdb1 /mnt/usb
```

`tail -f` is a live tail. `Ctrl+C` when you have seen enough drama.

### Best Practices

- **Use sudo instead of su**: `su` is a root shell you then have to remember to leave. `sudo` is one command and done.
- **Be specific**: Only `sudo` the command that needs it. `htop` does not.
- **Read it twice**: especially anything with `rm`, `chmod`, or a path starting at `/`
- **Use visudo**: Edit `/etc/sudoers` only with `sudo visudo`. It checks syntax before it lets you lock yourself out.

### Security Tips

- Your password is cached for about 15 minutes. That is convenience, not magic.
- `sudo -k` forgets the cache now.
- `sudo -l` lists what you are actually allowed to run.
- Never run an untrusted script with sudo, including one a model wrote and you only skimmed.

### Example: Installing Software

```sh
# Update package lists (requires sudo)
sudo apt update

# Install a package (requires sudo)
sudo apt install htop

# Run the installed program (no sudo needed)
htop
```

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
alias gs='git status'
alias mv='mv -i'
alias rm='rm -i'
alias cp='cp -i'
```

The `-i` flag is a seatbelt on `mv` / `rm` / `cp`: they ask before clobbering. That saves interactive you. It will hang a script waiting for a prompt nobody will type.

`gs` is a shell nickname for `git status`. The shell only expands it when `gs` is the command itself. `git gs` asks git for a *git* alias named `gs`, which is a different list (`git config --global alias.st status`, then you type `git st`). [Modern tools](modern_tools.md#3-aliases-for-these-tools) has the nicknames for `eza`, `bat`, and `fd`, and it repeats this trap so it stays a trap you have met twice.

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

Key-value pairs that child processes can see — once you `export` them. `PATH` is a scavenger hunt: first match wins. Put `~/bin` at the front if you want *your* `hello` to beat `/usr/bin/hello`.

```sh
export EDITOR=vim
export PATH="$HOME/bin:$PATH"
export HISTSIZE=10000
export HISTFILESIZE=20000
```

**Common environment variables:**
- `EDITOR`: Default text editor for command-line programs
- `PATH`: Directories where the shell looks for executable files
- `HISTSIZE`: Number of commands to remember in current session
- `HISTFILESIZE`: Number of commands to save in history file
- `LANG`: System language and locale settings

**Tips:**
- `export` makes the variable available to child processes
- `env` or `printenv` lists them all
- `echo $VARIABLE_NAME` prints one

### Personal Bin Directory

Your scripts, your `PATH`, no `sudo` required. This is the grown-up version of "I keep a file on the Desktop named `run this.sh`."

```sh
# Create the directory
mkdir -p ~/bin

# Add it to your PATH (add this to ~/.bashrc or ~/.zshrc)
export PATH="$HOME/bin:$PATH"
```

**Benefits:**

- Keep your custom scripts organized in one place
- Make scripts executable from anywhere without typing full paths
- Separate your scripts from system binaries
- Easy to backup and sync across machines

**Example usage:**
```sh
# Create a script
echo '#!/bin/bash\necho "Hello from my script!"' > ~/bin/myscript
chmod +x ~/bin/myscript

# Run it from anywhere
myscript
```

### Vi Mode

Same joke as Vim, smaller box. `Esc` then `hjkl` on the command line. Full tour is in [Text editors](text_editors.md#7-vim-and-emacs-modes-in-the-shell-and-in-coding-agents).

```sh
set -o vi
```

- Press `Esc` for command mode, then `h` `j` `k` `l` to move
- `i` returns to insert mode
- Add this to `~/.bashrc` or `~/.zshrc` to keep it

---

## 4. Prompt Customization

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

`PS1` is enough until you want git branch, language version, and command duration without writing a novel of escape codes. [starship.rs](https://starship.rs/) and [Oh My Posh](https://ohmyposh.dev/) are both fast, cross-shell prompt engines. Pick **one**. Putting both `eval` lines in the same rc file means they fight over the prompt.

Both look best with a [Nerd Font](https://www.nerdfonts.com/) set in the terminal. Both ship installers that people `curl | sh` without reading. Don't.

### starship.rs

[starship.rs](https://starship.rs/) is a fast, customizable, and minimal prompt for any shell. One TOML file configures every shell you init.

- Prefer your operating system's package manager when available. If you use the installer below, download and review it first rather than piping a remote script directly to a shell:

  ```sh
  curl -fsSLo /tmp/starship-install.sh https://starship.rs/install.sh
  less /tmp/starship-install.sh
  sh /tmp/starship-install.sh
  ```

- Add to your `~/.bashrc` or `~/.zshrc`:

  ```sh
  eval "$(starship init bash)"
  # or for zsh:
  eval "$(starship init zsh)"
  ```

- Configure with `~/.config/starship.toml`

### Oh My Posh

[Oh My Posh](https://ohmyposh.dev/) is a prompt theme engine for any shell (bash, zsh, PowerShell, fish, and more). It ships a large set of themes and configures in JSON, YAML, or TOML. On WSL, install the Linux binary, not the Windows one.

- Prefer a package manager when you have one:

  ```sh
  # macOS / Linux (Homebrew)
  brew install jandedobbeleer/oh-my-posh/oh-my-posh

  # Windows
  winget install JanDeDobbeleer.OhMyPosh --source winget
  ```

- On Linux without Homebrew, download and review the installer first. It needs `curl`, `unzip`, `realpath`, and `dirname`. The script installs to `~/bin` or `~/.local/bin` (see [Personal Bin Directory](#personal-bin-directory) — that directory must be on `PATH`):

  ```sh
  curl -fsSLo /tmp/oh-my-posh-install.sh https://ohmyposh.dev/install.sh
  less /tmp/oh-my-posh-install.sh
  bash /tmp/oh-my-posh-install.sh
  ```

- Add as the last line of your `~/.bashrc` or `~/.zshrc`:

  ```sh
  eval "$(oh-my-posh init bash)"
  # or for zsh:
  eval "$(oh-my-posh init zsh)"
  ```

  PowerShell (`$PROFILE`):

  ```powershell
  oh-my-posh init pwsh | Invoke-Expression
  ```

  Unsure which shell you are in: `oh-my-posh get shell`.

- Themes: pass `--config` with a theme name, a local file, or a URL. A local file is faster than a name or URL (those download on first use):

  ```sh
  eval "$(oh-my-posh init bash --config ~/.mytheme.omp.json)"
  ```

  Export the current theme to tweak it:

  ```sh
  oh-my-posh config export --output ~/.mytheme.omp.json
  ```

  Preview without restarting the shell:

  ```sh
  oh-my-posh print preview
  ```

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

[← Back: Bash Scripting Basics](tools_bash.md) | [Next: sed, sort, uniq, and More →](tools_sed.md)
