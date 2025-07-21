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

## Exit Status and $?

Every command in Unix returns an exit status when it completes. Understanding exit status is crucial for writing reliable scripts and chaining commands.

### What is Exit Status?

- Exit status is a number between 0 and 255 that indicates whether a command succeeded or failed
- `0` means success
- Any non-zero value (1-255) indicates an error or failure
- The special variable `$?` contains the exit status of the last command executed

### Using $?

```sh
# Run a command and check its exit status
ls /home
echo $?    # Will print 0 if ls succeeded

# Try a command that will fail
ls /nonexistent-directory
echo $?    # Will print a non-zero number (usually 2)

# Check if a file exists
test -f myfile.txt
if [ $? -eq 0 ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

### Common Exit Codes

- `0` — Success
- `1` — General error
- `2` — Misuse of shell command
- `126` — Command cannot execute (permission denied)
- `127` — Command not found
- `130` — Script terminated by Ctrl+C

### Practical Examples

```sh
# Backup a file and check if it worked
cp important.txt important.txt.backup
if [ $? -eq 0 ]; then
    echo "Backup successful"
    rm important.txt
else
    echo "Backup failed, keeping original file"
fi

# Check if a service is running
systemctl is-active nginx > /dev/null 2>&1
if [ $? -eq 0 ]; then
    echo "Nginx is running"
else
    echo "Nginx is not running"
fi
```

---

## File Permissions and Listing Files

### File Permissions: Read, Write, Execute

- Every file and directory has permissions for the owner, group, and others:
  - `r` = read (value: 4)
  - `w` = write (value: 2)
  - `x` = execute (value: 1)
- Permissions are shown as a string (e.g., `-rwxr-xr--`) or as a number (e.g., `755`, `777`).

### Understanding Numerical Permissions

Permissions use a 3-digit octal system where each digit represents owner, group, and others:

**Single digit values:**
- `0` = no permissions (---)
- `1` = execute only (--x)
- `2` = write only (-w-)
- `3` = write + execute (-wx) [2+1]
- `4` = read only (r--)
- `5` = read + execute (r-x) [4+1]
- `6` = read + write (rw-) [4+2]
- `7` = read + write + execute (rwx) [4+2+1]

**Common permission combinations:**
- `644` = rw-r--r-- (owner: read/write, group: read, others: read) - typical for files
- `755` = rwxr-xr-x (owner: full, group: read/execute, others: read/execute) - typical for scripts/executables
- `700` = rwx------ (owner: full, group: none, others: none) - private files
- `777` = rwxrwxrwx (everyone: full permissions) - **not recommended for security**

**Examples:**
```sh
chmod 644 document.txt    # Regular file: owner can edit, others can read
chmod 755 myscript.sh     # Script: owner can edit/run, others can run
chmod 700 private.txt     # Private: only owner can access
chmod 600 ~/.ssh/id_rsa   # SSH private key: only owner can read/write
```

### Why Scripts Need Execute Permissions

When you create a shell script, it starts as a regular text file without execute permissions. Here's why you need to add them:

**What happens without execute permission:**
```sh
# Create a script
echo '#!/bin/bash\necho "Hello World"' > hello.sh

# Try to run it - this will fail
./hello.sh
# Output: bash: ./hello.sh: Permission denied
```

**The script file exists and is readable, but the system won't execute it because:**
1. The operating system checks execute permission before running any file
2. This is a security feature to prevent accidental execution of data files
3. Only files explicitly marked as executable can be run

**Adding execute permission:**
```sh
# Make it executable
chmod +x hello.sh
# or
chmod 755 hello.sh

# Now you can run it
./hello.sh
# Output: Hello World
```

**Alternative ways to run scripts without execute permission:**
```sh
# You can still run the script by passing it to the interpreter directly
bash hello.sh    # Works even without +x permission
sh hello.sh      # Also works
```

**Best practices for script permissions:**
- `755` - Good for scripts others might need to run
- `750` - Script executable by owner and group only
- `700` - Private script only you can run
- Always use `chmod +x` when you create a new script you want to execute directly

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

## Command Chaining with && and ||

You can chain commands together based on whether the previous command succeeded or failed using `&&` (AND) and `||` (OR) operators.

### The && Operator (AND)

- `command1 && command2` — Run command2 only if command1 succeeds (exit status 0)
- Useful for conditional execution

```sh
# Only create backup if the copy succeeds
cp file.txt file.backup && echo "Backup created successfully"

# Chain multiple commands - each runs only if the previous succeeds
mkdir project && cd project && touch README.md && echo "Project setup complete"

# Install package and then start service (only if install succeeds)
sudo apt install nginx && sudo systemctl start nginx
```

### The || Operator (OR)

- `command1 || command2` — Run command2 only if command1 fails (non-zero exit status)
- Useful for error handling and fallbacks

```sh
# Try to use vim, fall back to nano if vim is not available
vim myfile.txt || nano myfile.txt

# Create directory or warn if it fails
mkdir /tmp/mydir || echo "Failed to create directory"

# Check if file exists, create it if it doesn't
test -f config.txt || touch config.txt
```

### Combining && and ||

You can combine both operators for more complex logic:

```sh
# Try to compile, and either celebrate success or handle failure
make && echo "Build successful!" || echo "Build failed!"

# Backup file, and either confirm success or exit with error
cp important.txt backup/ && echo "Backup done" || { echo "Backup failed!"; exit 1; }

# Check if service is running, start it if not, or report error
systemctl is-active nginx || systemctl start nginx || echo "Failed to start nginx"
```

### Command Chaining Examples

```sh
# Safe file operations
[ -d backup ] || mkdir backup && cp *.txt backup/

# Update system packages safely
sudo apt update && sudo apt upgrade || echo "Update failed"

# Git workflow
git add . && git commit -m "Update" && git push || echo "Git operation failed"

# Download and extract archive
wget https://example.com/file.tar.gz && tar -xzf file.tar.gz || echo "Download or extraction failed"
```

### Best Practices

- Use parentheses or braces `{}` to group commands when needed
- Be careful with complex chains - sometimes explicit `if` statements are clearer
- Test your command chains thoroughly, especially in scripts
- Remember that `&&` and `||` have left-to-right associativity

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

## Process Management with ps

The `ps` command shows information about running processes on your system. Understanding processes is essential for system monitoring and troubleshooting.

### Basic ps Usage

```sh
# Show processes for current user
ps

# Show all processes with detailed information
ps aux

# Show processes in a tree format (shows parent-child relationships)
ps -ef --forest
# or
pstree
```

### Understanding ps Output

**Basic `ps` output:**
```text
  PID TTY          TIME CMD
 1234 pts/0    00:00:01 bash
 5678 pts/0    00:00:00 ps
```

**Detailed `ps aux` output:**
```text
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
amit      1234  0.1  0.5  21616  5432 pts/0    Ss   10:30   0:01 -bash
amit      5678  0.0  0.1   8892   912 pts/0    R+   10:45   0:00 ps aux
```

**Column meanings:**
- **PID**: Process ID (unique identifier)
- **USER**: Username that owns the process
- **%CPU**: Percentage of CPU usage
- **%MEM**: Percentage of memory usage
- **VSZ**: Virtual memory size (KB)
- **RSS**: Resident Set Size - physical memory currently used (KB)
- **TTY**: Terminal associated with the process
- **STAT**: Process state (R=running, S=sleeping, Z=zombie, etc.)
- **START**: When the process started
- **TIME**: Total CPU time used
- **COMMAND**: The command that started the process

### Useful ps Options

```sh
# Show processes for a specific user
ps -u username

# Show processes by name (grep pattern)
ps aux | grep firefox

# Show process hierarchy
ps -ejH

# Show processes with custom columns
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu

# Monitor processes continuously (like top, but with ps)
watch -n 1 'ps aux --sort=-%cpu | head -20'
```

### Process States

Common process states you'll see in the STAT column:

- **R**: Running or runnable
- **S**: Sleeping (waiting for an event)
- **D**: Uninterruptible sleep (usually I/O)
- **T**: Stopped (by job control signal)
- **Z**: Zombie (terminated but not reaped by parent)
- **<**: High priority process
- **+**: Foreground process group

### Practical Examples

```sh
# Find memory-hungry processes
ps aux --sort=-%mem | head -10

# Find CPU-intensive processes
ps aux --sort=-%cpu | head -10

# Check if a specific service is running
ps aux | grep nginx

# Find processes by name and kill them
ps aux | grep "python script.py"
# Then use kill command with the PID

# Show process tree to understand relationships
ps -ef --forest | grep -A 5 -B 5 nginx

# Monitor a specific user's processes
watch -n 2 'ps -u amit -o pid,cmd,%cpu,%mem --sort=-%cpu'
```

### Related Commands

```sh
# Real-time process viewer
top
htop    # Enhanced version (may need to install)

# Kill processes
kill PID              # Terminate process by PID
killall process_name  # Kill all processes by name
pkill pattern         # Kill processes matching pattern

# Job control
jobs        # Show background jobs
fg %1       # Bring job 1 to foreground
bg %1       # Send job 1 to background
nohup cmd & # Run command immune to hangups
```

### Process Management in Scripts

```sh
#!/bin/bash
# Check if a process is running
if ps aux | grep -q "[n]ginx"; then
    echo "Nginx is running"
else
    echo "Nginx is not running"
fi

# Start a background process and save its PID
my_command &
PID=$!
echo "Started process with PID: $PID"

# Wait for a specific process to finish
wait $PID
echo "Process $PID has finished"
```

---

## Resources

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [Bash tutorial by Free Code Camp](https://www.freecodecamp.org/news/bash-scripting-tutorial-linux-shell-script-and-command-line-for-beginners/)
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html)

[Next: Shell Customization Guide →](shell_customization.md)