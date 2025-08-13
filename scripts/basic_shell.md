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
- `grep` — Search for patterns in text

### pwd: Print Working Directory

Shows your current location in the file system.

```sh
# Show current directory
pwd
# Output: /home/username/projects

# Useful in scripts to know where you are
echo "Currently in: $(pwd)"
```

### ls: List Files and Directories

Display contents of directories with various formatting options.

```sh
# Basic listing
ls

# List with details (permissions, owner, size, date)
ls -l

# List all files including hidden ones (starting with .)
ls -a

# List with human-readable file sizes
ls -lh

# List sorted by modification time (newest first)
ls -lt

# List sorted by modification time (oldest first)
ls -ltr

# List only directories
ls -d */

# List specific file types
ls *.txt        # All .txt files
ls *.py         # All Python files

# List contents of specific directory
ls /etc
ls ~/Documents

# Recursive listing (show subdirectories)
ls -R

# List with full timestamps
ls -l --time-style=full-iso
```

### cd: Change Directory

Navigate through the file system.

```sh
# Go to home directory
cd
cd ~

# Go to specific directory
cd /usr/local/bin
cd ~/Documents

# Go to parent directory
cd ..

# Go back to previous directory
cd -

# Go to root directory
cd /

# Navigate using relative paths
cd ./subfolder
cd ../other-folder

# Navigate multiple levels
cd ../../parent-parent-directory

# Use tab completion for paths
cd Doc<TAB>     # Completes to Documents if it exists
```

### mkdir: Make Directory

Create new directories.

```sh
# Create a single directory
mkdir new-folder

# Create multiple directories
mkdir folder1 folder2 folder3

# Create nested directories (parent directories created automatically)
mkdir -p projects/web-app/src/components

# Create directory with specific permissions
mkdir -m 755 public-folder

# Create directory and show what was created
mkdir -v new-project
# Output: mkdir: created directory 'new-project'

# Create directory structure for a project
mkdir -p myapp/{src,tests,docs,config}
```

### rm: Remove Files and Directories

Delete files and directories (be careful - this is usually permanent!).

```sh
# Remove a single file
rm filename.txt

# Remove multiple files
rm file1.txt file2.txt file3.txt

# Remove files with pattern matching
rm *.tmp        # Remove all .tmp files
rm test_*       # Remove all files starting with "test_"

# Interactive removal (ask before each deletion)
rm -i important-file.txt

# Remove directory and all contents (recursive)
rm -r old-project/

# Force removal (no prompts, ignore non-existent files)
rm -f stubborn-file.txt

# Combine flags: force recursive removal
rm -rf temporary-folder/

# Verbose output (show what's being removed)
rm -v *.log
# Output: removed 'access.log'
# Output: removed 'error.log'

# Safety tip: Always double-check before using rm -rf
ls -la before-deleting/  # Check contents first
rm -rf before-deleting/  # Then delete
```

### cp: Copy Files and Directories

Copy files and directories to new locations.

```sh
# Copy a file
cp source.txt destination.txt

# Copy file to different directory
cp document.txt ~/backup/

# Copy multiple files to a directory
cp file1.txt file2.txt file3.txt ~/backup/

# Copy with pattern matching
cp *.jpg ~/pictures/

# Copy directory and all contents (recursive)
cp -r project-folder/ project-backup/

# Interactive copy (ask before overwriting)
cp -i important.txt important-backup.txt

# Preserve file attributes (permissions, timestamps)
cp -p original.txt copy-with-attributes.txt

# Verbose output
cp -v source.txt destination.txt
# Output: 'source.txt' -> 'destination.txt'

# Copy and create destination directories if needed
cp --parents src/utils/helper.py backup/
# Creates backup/src/utils/ if it doesn't exist

# Update copy (only copy if source is newer)
cp -u newer-file.txt existing-file.txt
```

### mv: Move and Rename Files

Move files to new locations or rename them.

```sh
# Rename a file
mv oldname.txt newname.txt

# Move file to different directory
mv document.txt ~/archive/

# Move multiple files
mv file1.txt file2.txt file3.txt ~/destination/

# Move with pattern matching
mv *.log ~/logs/

# Move directory
mv old-folder/ new-location/

# Interactive move (ask before overwriting)
mv -i source.txt destination.txt

# Verbose output
mv -v document.txt ~/backup/
# Output: 'document.txt' -> '/home/user/backup/document.txt'

# Move and rename simultaneously
mv ~/downloads/file.txt ~/documents/renamed-file.txt

# Update move (only move if source is newer or destination doesn't exist)
mv -u source.txt destination.txt
```

### cat: Display File Contents

Read and display file contents, or concatenate multiple files.

```sh
# Display file contents
cat readme.txt

# Display multiple files
cat file1.txt file2.txt

# Display with line numbers
cat -n script.py

# Display non-printing characters
cat -A config.txt

# Display only non-blank lines with numbers
cat -b document.txt

# Concatenate files into new file
cat file1.txt file2.txt > combined.txt

# Append to existing file
cat additional.txt >> existing.txt

# Display file with page breaks
cat -v document.txt

# Read from stdin (useful in scripts)
cat > new-file.txt
# Type content, then Ctrl+D to finish

# Display first few lines (alternative to head)
cat readme.txt | head -10
```

### echo: Print Text

Output text to the terminal or files.

```sh
# Simple text output
echo "Hello, World!"

# Output without newline
echo -n "Enter your name: "

# Output with escape sequences
echo -e "Line 1\nLine 2\nLine 3"

# Output variables
name="Alice"
echo "Hello, $name!"

# Output to file
echo "This is a log entry" > logfile.txt

# Append to file
echo "Another entry" >> logfile.txt

# Output current date
echo "Today is $(date)"

# Output with special characters
echo -e "Tab\there\nNewline here"

# Output multiple arguments
echo The current directory is $(pwd)

# Output environment variables
echo "Your home directory is: $HOME"
echo "Your username is: $USER"

# Create simple files
echo "#!/bin/bash" > script.sh
echo "echo 'Hello from script'" >> script.sh
```

### man: Manual Pages

Access documentation for commands and programs.

```sh
# View manual for a command
man ls
man grep
man chmod

# Search for commands by keyword
man -k network
man -k "copy files"

# View specific section of manual
man 1 printf    # Section 1: User commands
man 3 printf    # Section 3: Library functions

# Display short description
man -f ls
# or
whatis ls

# View manual in browser-like interface
info ls

# Search within a manual page
# Use / to search forward
# Use ? to search backward
# Use n for next match
# Use N for previous match

# Manual sections:
# 1: User commands
# 2: System calls
# 3: Library functions
# 4: Device files
# 5: File formats
# 6: Games
# 7: Miscellaneous
# 8: System administration

# Quick help for built-in commands
help cd
help echo
```

### grep: Search for Patterns in Text

`grep` is one of the most powerful and commonly used commands for searching text patterns in files or command output.

**Basic syntax:**
```sh
grep [options] pattern [file...]
```

### Basic grep Usage

```sh
# Search for a word in a file
grep "error" logfile.txt

# Search for a pattern in multiple files
grep "TODO" *.py

# Search in all files in current directory
grep "function" *

# Case-insensitive search
grep -i "error" logfile.txt

# Show line numbers
grep -n "import" script.py
```

### Useful grep Options

- `-i` — Case-insensitive search
- `-n` — Show line numbers
- `-v` — Invert match (show lines that DON'T match)
- `-c` — Count matching lines
- `-l` — Show only filenames that contain matches
- `-r` or `-R` — Search recursively through directories
- `-w` — Match whole words only
- `-A num` — Show num lines after each match
- `-B num` — Show num lines before each match
- `-C num` — Show num lines before and after each match

### grep Examples

```sh
# Find files containing "password" (case-insensitive)
grep -i "password" *.txt

# Search recursively in all subdirectories
grep -r "TODO" .

# Count how many times "error" appears
grep -c "error" logfile.txt

# Find lines that DON'T contain "success"
grep -v "success" logfile.txt

# Show context around matches (3 lines before and after)
grep -C 3 "exception" error.log

# Find whole word matches only
grep -w "cat" animals.txt  # Matches "cat" but not "catch"

# Show only filenames that contain the pattern
grep -l "main" *.py

# Find empty lines
grep "^$" file.txt

# Find lines starting with specific text
grep "^Error" logfile.txt

# Find lines ending with specific text
grep "failed$" logfile.txt
```

### Using grep with Pipes

`grep` is extremely powerful when combined with other commands:

```sh
# Search in command output
ps aux | grep firefox

# Find specific processes
ps aux | grep -v grep | grep python

# Search in file listings
ls -la | grep "\.py$"  # Find Python files

# Filter log entries by date
cat /var/log/syslog | grep "Jan 15"

# Count error messages
journalctl | grep -c "error"
```

### Basic Regular Expressions with grep

```sh
# Match any single character (.)
grep "c.t" file.txt  # Matches cat, cut, cot

# Match beginning of line (^)
grep "^Error" logfile.txt

# Match end of line ($)
grep "done$" logfile.txt

# Match any of the characters in brackets
grep "[Ee]rror" logfile.txt  # Matches Error or error

# Match a range of characters
grep "[0-9]" file.txt  # Matches any digit

# Match one or more of the preceding character (+)
grep -E "colou?r" file.txt  # Matches color or colour
```

### Practical grep Examples

```sh
# Find configuration files
find /etc -name "*.conf" | xargs grep -l "ssh"

# Search for IP addresses
grep -E "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" logfile.txt

# Find shell scripts
find . -name "*.sh" | xargs grep -l "#!/bin/bash"

# Filter out comments and empty lines
grep -v "^#" config.file | grep -v "^$"

# Find functions in Python files
grep -n "def " *.py

# Search for email addresses
grep -E "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b" file.txt
```

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

---

## Debugging Shell Scripts

Debugging is an essential skill for writing complex scripts. Here are a few techniques to help you find and fix errors in your Bash scripts.

### Tracing with `set -x`

- The `set -x` command enables a mode where the shell prints each command and its arguments as it executes. This is extremely useful for seeing the flow of your script and the values of variables at each step.
- You can turn it off with `set +x`.

```sh
#!/bin/bash

set -x  # Start tracing

name="Amit"
echo "Hello, $name"

set +x  # Stop tracing

echo "Tracing is now off."
```

**Output:**
```
+ name=Amit
+ echo 'Hello, Amit'
Hello, Amit
+ set +x
Tracing is now off.
```

### Exiting on Error with `set -e`

- The `set -e` command causes the script to exit immediately if any command fails (returns a non-zero exit status).
- This helps prevent unexpected behavior where a script continues to run after a critical command has failed.

```sh
#!/bin/bash

set -e

# This command will fail
ls /nonexistent-directory

# This command will never be reached
echo "This will not be printed."
```

### Using a Linter: `shellcheck`

- `shellcheck` is a static analysis tool that gives warnings and suggestions for your shell scripts. It can help you avoid common pitfalls and write more robust scripts.
- You may need to install it first (`sudo apt install shellcheck` on Debian/Ubuntu).

**Example:**
```sh
# script.sh
name="Amit"
echo "Hello, $name" # Shellcheck will warn about quoting
```

**Running shellcheck:**
```sh
shellcheck script.sh
```

**Output:**
```
In script.sh line 2:
echo "Hello, $name"
              ^-- SC2086: Double quote to prevent globbing and word splitting.

Did you mean:
echo "Hello, "$name""
```

---

## Resources

- [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- [Shell Scripting Tutorial](https://www.shellscript.sh/)
- [Bash tutorial by Free Code Camp](https://www.freecodecamp.org/news/bash-scripting-tutorial-linux-shell-script-and-command-line-for-beginners/)
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html)
- [ShellCheck Website](https://www.shellcheck.net/)

[Next: Shell Customization Guide →](shell_customization.md)
