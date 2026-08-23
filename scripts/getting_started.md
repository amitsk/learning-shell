# Getting Started with the Unix Shell and Scripting

This guide will help you get started with using the Unix shell, command-line tools, and shell scripting on Linux, macOS, and Windows. It covers basic setup, recommended tools, and useful tips for all platforms to master both interactive shell usage and automation through scripting.

[Next: Text Editors on the Command Line →](text_editors.md)

---

## 1. What is the Unix Shell and Shell Scripting?

The Unix shell is a command-line interface that allows you to interact with your operating system using text commands. Shell scripting takes this a step further by letting you automate tasks by writing a series of shell commands in a file (a script). Together, they provide powerful capabilities for:

- Interactive system management and file operations
- Automating repetitive tasks
- Processing files and data efficiently
- Managing system operations and workflows

---

## 2. Setting Up Your Environment

### Linux

- Most distributions come with Bash pre-installed.
- Open your terminal (Ctrl+Alt+T or search for "Terminal").

### macOS

- Current macOS installations use Zsh by default. Bash is also available, although the system Bash version may be older than the current upstream release.
- Open Terminal (Cmd+Space, type "Terminal").

### Windows

- **Recommended:** Use [Windows Terminal](https://aka.ms/terminal) with WSL for a Linux-compatible environment.
- **WSL2 (Windows Subsystem for Linux):**
  - In an elevated PowerShell, run `wsl --install`, restart if prompted, and launch the installed distribution.
  - Use `wsl --list --online` to see distributions and `wsl -l -v` to check their WSL version.
  - You can use Ubuntu or other distributions from the Microsoft Store.
  - Access your Linux files and run Bash scripts just like on a real Linux machine.
- **Git Bash:** A lightweight Bash environment included with [Git for Windows](https://gitforwindows.org/).

---

## 3. Writing Your First Script

1. Open your terminal.
2. Create a new file:

   ```sh
   nano hello.sh
   ```

3. Add the following lines:

   ```sh
   #!/bin/bash
   echo "Hello, world!"
   ```

4. Save and exit (Ctrl+O, Enter, Ctrl+X in nano).
5. Make it executable:

   ```sh
   chmod +x hello.sh
   ```

6. Run your script:

   ```sh
   ./hello.sh
   ```

---

## 4. Recommended Tools

- [starship.rs](https://starship.rs/): A fast, customizable, and minimal prompt for any shell. Works on Linux, macOS, and Windows (including WSL2 and PowerShell).
- [Windows Terminal](https://aka.ms/terminal): Modern, tabbed terminal for Windows. Supports PowerShell, Command Prompt, WSL, and more.
- [nano](https://www.nano-editor.org/), [vim](https://www.vim.org/), [Microsoft Edit](https://github.com/microsoft/edit), [Visual Studio Code](https://code.visualstudio.com/), or [Helix](https://helix-editor.com/) for editing scripts.

---

## 5. Notes on the `scratch` Directory

- Any files you create in the `scratch` directory are ignored by Git and will not show up as changes. Use this directory for experiments and temporary files.

---

## 6. Using SSH to Connect to a Remote Server

SSH (Secure Shell) allows you to securely connect to a remote Linux or Unix server from your terminal.

- **Basic usage:**

  ```sh
  ssh username@remote_host
  ```
  - Replace `username` with your remote username and `remote_host` with the server's IP address or hostname.

- **Example:**

  ```sh
  ssh amit@192.168.1.100
  ```

- **First time connecting:** You may be asked to confirm the server's fingerprint. Compare it with a value from the host admin (or the cloud console) before typing `yes`.

### Key-based authentication (preferred)

Password logins work, but they are easy to phish, tedious to type, and often disabled on real servers. **SSH keys** use a key pair: a private key that stays on your machine, and a public key you install on the server.

1. **Generate a key** (Ed25519 is the modern default):

   ```sh
   ssh-keygen -t ed25519 -C "you@example.com"
   ```

   Press Enter to accept the default path (`~/.ssh/id_ed25519`). Set a passphrase. A key without a passphrase is convenient; a key *with* one is safer if the laptop is stolen.

2. **Install the public key on the server.** This first hop still uses the **account password**. Pin the key you just made:

   ```sh
   ssh-copy-id -i ~/.ssh/id_ed25519.pub username@remote_host
   ```

   If `ssh-copy-id` is missing (common on macOS unless you install it), copy the **public** key by hand (you will type the server password once):

   ```sh
   cat ~/.ssh/id_ed25519.pub | ssh username@remote_host "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
   ```

3. **Log in without a password** (you may still type the *key* passphrase):

   ```sh
   ssh username@remote_host
   ```

4. **Optional: ssh-agent** so you type the passphrase once per session:

   ```sh
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

5. **Optional: `~/.ssh/config`** so you can type `ssh myserver` instead of a long command:

   ```text
   Host myserver
     HostName 192.168.1.100
     User amit
     IdentityFile ~/.ssh/id_ed25519
   ```

**Do not share or commit the private key.** Keep `~/.ssh` at `700`, the private key at `600`, and the public key at `644`. The public key (`*.pub`) is what goes on servers; the private key never does. Do not disable password login on the server until a second key-based login has worked.

### Copying files

```sh
# Copy a file to the remote server
scp localfile.txt username@remote_host:/path/to/destination/

# Copy a file from the remote server
scp username@remote_host:/path/to/file.txt ./
```

- **More info:** [SSH Tutorial for Beginners](https://www.ssh.com/academy/ssh/command)
- **SSH keys:** [OpenSSH keys (ssh.com)](https://www.ssh.com/academy/ssh/key) · [GitHub: connecting with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

---

## 7. References & Further Reading

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [starship.rs Documentation](https://starship.rs/)
- [Windows Terminal Documentation](https://learn.microsoft.com/en-us/windows/terminal/)
- [WSL2 Documentation](https://learn.microsoft.com/en-us/windows/wsl/)
- [SSH Tutorial for Beginners](https://www.ssh.com/academy/ssh/command)
- [OpenSSH keys](https://www.ssh.com/academy/ssh/key)
- [Linux Cheatsheets](http://www.nixtutor.com/linux/all-the-best-linux-cheat-sheets/)
- [Text Editors on the Command Line](text_editors.md)

---
Happy scripting!

[Next: Text Editors on the Command Line →](text_editors.md)
