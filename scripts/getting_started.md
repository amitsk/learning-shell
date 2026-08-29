# Getting Started with the Unix Shell and Scripting

This is the "open a terminal without panic" chapter. It works on Linux, macOS, and Windows (via WSL). You get setup, a first script, and SSH. The rest of the repo is practice.

Need a Linux *desktop* first — distro, install, SSH server, editors? That is a different tutorial: [amitsk/learning-linux](https://github.com/amitsk/learning-linux). This one assumes you already have a prompt you can type into.

[Next: Text Editors on the Command Line →](text_editors.md)

---

## 1. What is a shell?

The operating system kernel talks to hardware. You do not talk to the kernel. You talk to a **shell**: a program that reads what you type, runs commands, and prints the result.

The window that shows this is a **terminal** (Terminal.app, GNOME Terminal, Windows Terminal, that black rectangle in your IDE). The terminal is the stage. The shell is the actor. Mixing the two names up is a rite of passage; mixing them up in a job interview is optional.

**Shell scripting** is the same conversation, written down. A script is a file of commands the shell runs in order so you do not have to type them every Tuesday.

A shell is good at:

- Moving around the filesystem and wrangling files
- Gluing small tools together (`grep`, `sort`, pipes)
- Automating the boring stuff
- Talking to remote machines over SSH

It is a poor spreadsheet, a worse word processor, and will not forgive a typo the way a GUI "are you sure?" box would.

### Common shells

You only need one. These names show up anyway:

| Shell | In one or two lines |
| --- | --- |
| **bash** (Bourne Again SHell) | Default on most Linux distros, WSL, CI images, and Dockerfiles. The lingua franca of "paste this into a terminal." |
| **zsh** (Z shell) | Default login shell on **macOS** since Catalina. Fancy completion and plugins; most bash you type still works. |
| **ksh** (KornShell) | Older professional Unix shell. Still appears on AIX, Solaris, and in enterprise scripts that have outlived their authors. |
| **csh** / **tcsh** (C shell) | C-like syntax from BSD. Historic; do not write new scripts in it. The industry had a meeting about this and then never scheduled a second one. |
| **fish** (Friendly Interactive Shell) | Pleasant to live in, *not* POSIX-compatible. Great interactive UX; a bad place to paste this tutorial's examples. |

### macOS: zsh is the default, Bash 3.2 is a museum piece

Apple's Terminal starts **zsh**. That is fine for daily use.

macOS also ships `/bin/bash`, but it is **Bash 3.2** — frozen because of GPL licensing, not because 3.2 was the pinnacle of human achievement. This tutorial's examples assume a current Bash (5.x). Install one with [Homebrew](https://brew.sh/):

```sh
brew install bash
$(brew --prefix)/bin/bash --version   # want 5.x, not 3.2
```

Optional: make Homebrew's Bash your login shell (so new Terminal windows start there):

```sh
echo "$(brew --prefix)/bin/bash" | sudo tee -a /etc/shells
chsh -s "$(brew --prefix)/bin/bash"
```

Apple Silicon lands at `/opt/homebrew/bin/bash`; Intel at `/usr/local/bin/bash`. `brew --prefix` saves you from memorizing that.

You do **not** have to switch your login shell. Running `bash` (or the Homebrew path) when you follow this tutorial is enough.

### We will use Bash

zsh, fish, and ksh are allowed hobbies. **Every explanation and script in this tutorial is Bash**, unless a page says otherwise. Bash is what Linux servers, autograders, and "works on my machine" Dockerfiles speak. A file that starts with `#!/bin/bash` is the one your future teammate will assume.

If your prompt is zsh, start a Bash session for the exercises:

```sh
bash
echo "$BASH_VERSION"
```

Do not paste `[[` tests into fish and then blame the tutorial. fish is allowed to be different; this document is not going to chase it.

---

## 2. Setting Up Your Environment

### Linux

Bash is already there on Mint, Ubuntu, Fedora, and friends. Open a terminal (`Ctrl+Alt+T`, or search for "Terminal") and you are in it.

If you still need to *install* Linux, follow [amitsk/learning-linux](https://github.com/amitsk/learning-linux) first, then come back.

### macOS

Open Terminal (`Cmd+Space`, type "Terminal"). You will land in **zsh**. Install a modern Bash as in [section 1](#macos-zsh-is-the-default-bash-32-is-a-museum-piece), then either `chsh` or just type `bash` when you work through these pages.

### Windows

- **Recommended:** [Windows Terminal](https://aka.ms/terminal) plus **WSL** — a real Linux userland, not a costume.
- **WSL2 (Windows Subsystem for Linux):**
  - In an elevated PowerShell, run `wsl --install`, restart if prompted, and launch the installed distribution.
  - Use `wsl --list --online` to see distributions and `wsl -l -v` to check their WSL version.
  - Ubuntu from the Microsoft Store is the boring, correct choice.
  - Inside WSL you have Bash, the same as a Linux box. Git Bash is the backup plan, not the lifestyle.
- **Git Bash:** a lightweight Bash included with [Git for Windows](https://gitforwindows.org/). Fine for `git` and a few scripts; WSL is closer to the real thing.

---

## 3. Writing Your First Script

A script is a text file the shell agrees to treat as a program. The first line (`#!/bin/bash`) is a shebang: it names the interpreter. Without it, you are hoping today's default shell is in a generous mood.

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

Files under `scratch/` are gitignored. Break things there. That is the point. The rest of the repo would rather not see your `hello2-final-FINAL.sh`.

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
