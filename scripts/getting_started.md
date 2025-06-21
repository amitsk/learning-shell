# Getting Started with Shell Scripting

This guide will help you get started with shell scripting on Linux, macOS, and Windows. It covers basic setup, recommended tools, and useful tips for all platforms.

[Next: Bash Shell & Scripting Basics →](basic_shell.md)

---

## 1. What is Shell Scripting?

Shell scripting lets you automate tasks by writing a series of shell commands in a file (a script). These scripts can:

- Automate repetitive tasks
- Process files and data
- Manage system operations

---

## 2. Setting Up Your Environment

### Linux

- Most distributions come with Bash pre-installed.
- Open your terminal (Ctrl+Alt+T or search for "Terminal").

### macOS

- The default shell is Zsh (as of macOS Catalina), but Bash is also available.
- Open Terminal (Cmd+Space, type "Terminal").

### Windows

- **Recommended:** Use [Windows Terminal](https://aka.ms/terminal) for a modern terminal experience.
- **WSL2 (Windows Subsystem for Linux):**
  - Install [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install) to run a real Linux environment on Windows.
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
- [nano](https://www.nano-editor.org/), [vim](https://www.vim.org/), or [Visual Studio Code](https://code.visualstudio.com/) for editing scripts.

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

- **First time connecting:** You may be asked to confirm the server's fingerprint. Type `yes` to continue.
- **Copying files to/from a remote server:**

  ```sh
  # Copy a file to the remote server
  scp localfile.txt username@remote_host:/path/to/destination/

  # Copy a file from the remote server
  scp username@remote_host:/path/to/file.txt ./
  ```

- **More info:** [SSH Tutorial for Beginners](https://www.ssh.com/academy/ssh/command)
- **SSH Keys Explained:**[SSH Keys](https://www.sectigo.com/resource-library/what-is-an-ssh-key)

---

## 7. References & Further Reading

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [starship.rs Documentation](https://starship.rs/)
- [Windows Terminal Documentation](https://learn.microsoft.com/en-us/windows/terminal/)
- [WSL2 Documentation](https://learn.microsoft.com/en-us/windows/wsl/)
- [SSH Tutorial for Beginners](https://www.ssh.com/academy/ssh/command)

---
Happy scripting!

[Next: Bash Shell & Scripting Basics →](basic_shell.md)

