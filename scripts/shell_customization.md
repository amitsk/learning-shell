# Shell Customization Guide

This guide covers how to customize your shell environment, including configuration files and prompt customization.


---

## 0. Your Home Directory & Shell Basics

### What is the Home Directory?

- Your home directory is your personal space for files and configuration on Unix-like systems.
- The path is usually `/home/username` (Linux) or `/Users/username` (macOS).
- Shortcut: `~` always refers to your home directory.
- To go to your home directory:

  ```sh
  cd ~
  # or simply
  cd
  ```

### Who Am I? (Finding Your Username)

- Use the `whoami` command to print your current username:

  ```sh
  whoami
  ```

### Identifying Your Current Shell

- To see which shell you are currently using:

  ```sh
  echo $SHELL
  # or
  ps -p $$
  ```

---

## 1. Shell Configuration Files

### Bash

- `~/.bashrc`: Runs for interactive non-login shells. Place aliases, functions, and prompt customizations here.
- `~/.bash_profile` or `~/.profile`: Runs for login shells. Use for environment variables and commands that should run once per session.
- `~/.bash_logout`: Commands to run on shell exit.

### Zsh

- `~/.zshrc`: Main config for interactive shells. Place aliases, functions, and prompt customizations here.
- `~/.zprofile`: Runs for login shells.
- `~/.zlogout`: Commands to run on shell exit.

---

## 2. Common Customizations

### Aliases

Add shortcuts for common commands in your `~/.bashrc` or `~/.zshrc`:

```sh
alias ll='ls -lFh'
alias gs='git status'
alias mv='mv -i'
alias rm='rm -i'
alias cp='cp -i'
```

- The `-i` flag makes `mv`, `rm`, and `cp` interactive, prompting before overwriting or deleting files. This helps prevent accidental data loss, which is a common risk in Unix systems when using these commands without confirmation.

### Functions

Define your own shell functions:

```sh
mkcd() {
  mkdir -p "$1" && cd "$1"
}
```

### Environment Variables

Environment variables are key-value pairs that affect how programs run. Set variables for your session:

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
- Use `export` to make variables available to child processes
- View all environment variables with `env` or `printenv`
- View a specific variable with `echo $VARIABLE_NAME`

### Personal Bin Directory

Create a personal `bin` directory for your custom scripts:

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

Enable vi-style command line editing in your shell:

```sh
set -o vi
```

- This allows you to use vi keybindings for editing commands
- Press `Esc` to enter command mode, then use vi commands like `h`, `j`, `k`, `l` for navigation
- Use `i` to return to insert mode
- Add this to your `~/.bashrc` or `~/.zshrc` to make it permanent

---

## 3. Prompt Customization

### Bash Prompt (PS1)

Customize your prompt by setting the `PS1` variable in `~/.bashrc`:

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

Zsh uses the `PROMPT` variable:

```sh
export PROMPT="%n@%m:%~$ "
```

---

## 4. Advanced Prompt: starship.rs

[starship.rs](https://starship.rs/) is a fast, customizable, and minimal prompt for any shell.

- Install with:

  ```sh
  curl -sS https://starship.rs/install.sh | sh
  ```

- Add to your `~/.bashrc` or `~/.zshrc`:

  ```sh
  eval "$(starship init bash)"
  # or for zsh:
  eval "$(starship init zsh)"
  ```

- Configure with `~/.config/starship.toml`

---

## 5. Reloading Your Config

After editing your rc/profile files, reload them with:

```sh
source ~/.bashrc
# or
source ~/.zshrc
```

---

## References

- [Bash Prompt HOWTO](https://tldp.org/HOWTO/Bash-Prompt-HOWTO/)
- [starship.rs Documentation](https://starship.rs/)
- [Zsh Guide](https://zsh.sourceforge.io/Guide/zshguide.html)

[Next: sed, sort, uniq, and More →](tools_sed.md)