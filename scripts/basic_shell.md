# Bash Shell & Scripting Basics

This guide introduces the basics of using the Bash shell and writing shell scripts on Linux.

---

## What is the Shell?

The shell is a command-line interface for interacting with your operating system. Bash (Bourne Again SHell) is the most common shell on Linux.

---

## Basic Shell Commands

- `pwd` — Print current working directory
- `ls` — List files and directories
- `cd` — Change directory
- `mkdir` — Make a new directory
- `rm` — Remove files or directories
- `cp` — Copy files or directories
- `mv` — Move or rename files or directories
- `cat` — Concatenate and display file contents
- `echo` — Print text to the terminal
- `man` — Show manual pages for commands

---

## File Permissions and Listing Files

### File Permissions: Read, Write, Execute

- Every file and directory has permissions for the owner, group, and others:
  - `r` = read
  - `w` = write
  - `x` = execute
- Permissions are shown as a string (e.g., `-rwxr-xr--`) or as a number (e.g., `755`, `777`).
- Example: `chmod 755 myfile.sh` sets permissions to `rwxr-xr-x`.
- `777` means read, write, and execute for everyone (not recommended for most files).

### Viewing Permissions and Hidden Files with `ls`

- `ls` — List files in the current directory
- `ls -l` — Long listing format (shows permissions, owner, group, size, date, name)
- `ls -a` — Show all files, including hidden files (those starting with `.`)
- `ls -la` or `ls -al` — Long listing including hidden files
- `ls -lh` — Long listing with human-readable file sizes
- `ls -lrt` — Long listing, sorted by modification time (oldest first)

**Example output:**

```sh
ls -la
```

```text
drwxr-xr-x  2 amit users 4096 Jun 21 10:00 .
drwxr-xr-x 10 root root  4096 Jun 21 09:00 ..
-rw-r--r--  1 amit users  220 Jun 21 10:00 .bashrc
-rwxr-xr-x  1 amit users  123 Jun 21 10:00 myscript.sh
```

- The first column shows the permissions.
- The first character: `-` for file, `d` for directory.
- Next 9 characters: owner/group/other permissions (e.g., `rwxr-xr--`).

---

## Command Line Tips

- Use `Tab` for autocompletion
- Use `Ctrl + C` to stop a running command
- Use `Ctrl + L` to clear the terminal
- Use `history` to see previous commands

---

## Redirection and Pipes

- `>` : Redirect output to a file (overwrite)
- `>>` : Redirect output to a file (append)
- `<` : Read input from a file
- `|` : Pipe output of one command to another

**Examples:**

```sh
echo "Hello" > hello.txt      # Write to file
cat hello.txt | grep H         # Pipe to grep
```

---

## Dotfiles in Unix

Dotfiles are configuration files in your home directory that start with a dot (`.`). They are hidden by default and store settings for your shell and other programs.

**Common dotfiles:**

- `~/.bashrc` — Bash shell configuration for interactive shells
- `~/.bash_profile` or `~/.profile` — Bash login shell configuration
- `~/.zshrc` — Zsh shell configuration
- `~/.vimrc` — Vim editor configuration
- `~/.gitconfig` — Git configuration

**Tips:**

- Use `ls -a` to see hidden files (dotfiles) in your home directory.
- You can version-control your dotfiles by keeping them in a Git repository.
- Customizing dotfiles lets you personalize your shell, editor, and tool behavior.

---

## Variables

```sh
name="MyName"
echo $name
```

---

## Writing a Simple Script

1. Create a file, e.g., `myscript.sh`
2. Add the shebang line at the top:

   ```sh
   #!/bin/bash
   ```

3. Write your commands below it:

   ```sh
   #!/bin/bash
   echo "Hello, world!"
   ```

4. Make it executable:

   ```sh
   chmod +x myscript.sh
   ./myscript.sh
   ```

---

## Conditionals

```sh
if [ "$name" = "MyName" ]; then
  echo "Hello, MyName!"
else
  echo "Hello, stranger!"
fi
```

---

## Loops

```sh
for file in *.txt; do
  echo $file
done
```

---

## Reading User Input

```sh
echo "Enter your name:"
read username
echo "Hello, $username!"
```

---

## Functions

```sh
say_hello() {
  echo "Hello, $1!"
}

say_hello "Amit"
```

---

## Resources

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html)

[Next: Shell Customization Guide →](shell_customization.md)