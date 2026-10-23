# Bash Scripting Basics

[← Two-hour path](../README.md#if-you-are-new) | [Everyday commands](basic_shell.md#the-light-path-ends-here)

Variables, `if`, loops, functions, arrays. The bits that turn a list of commands into a program you can run twice.

**About 45 minutes. Open this when a course asks for a script.** Done when you can write a file with a variable, a quote, and an `if`, and run it from `scratch/`.

Bash will not yell about the spaces around `=` until it has already done the wrong thing. Quote `"$file"`. Unquoted expansions are how `My Documents` becomes two arguments.

The everyday commands (`ls`, `rm`, pipes, permissions) are in [Bash basics](basic_shell.md). Try these snippets in `scratch/`.

---

## 1. Shebang

The first line is not a comment. It is a pointer: "run me with this interpreter." Skip it and you are hoping today's default shell is in a generous mood.

```sh
#!/usr/bin/env bash
```

`env` finds `bash` on `PATH`, so you are not married to `/bin/bash`. On macOS that matters: Apple's `/bin/bash` is still 3.2. Make the file executable (`chmod +x`) or call it with `bash script.sh`.

Run `shellcheck` on anything an agent just wrote, before you run the file. Quotes are the usual miss.

---

## 2. Variables

No spaces around `=`. `name = "Alice"` is Bash trying to run a command called `name`. The error message will not say "you typed a space." It will say `command not found`.

```sh
# Assigning a string
name="Alice"

# Assigning a number
age=30

# Accessing a variable
echo "User: $name, Age: $age"

# Command substitution
current_date=$(date +%Y-%m-%d)
echo "Today is $current_date"
```

`$(...)` runs a command and stuffs the output into the variable. Quote it when the output might contain spaces. Quote it even when you think it cannot.

### Environment vs. Local Variables

A variable is a secret the current shell keeps to itself until you `export` it. Child processes only hear the exported ones.

```sh
# A shell variable: this shell only
greeting="I am local"

# An environment variable: this shell and the programs it starts
export ENV_VAR="I am available everywhere"
```

`local` is a different word, and it only means something inside a function. `local name="Alice"` makes the variable disappear when the function returns. At the top level of a script, it is a syntax error. A variable called `local_var` is just a variable with an optimistic name.

```sh
greet() {
  local name="Alice"      # scoped to this function
  echo "Hello, $name"
}
```

---

## 3. Conditionals

### `if` Statements

`[` is a command, not decoration. The spaces inside the brackets are load-bearing. `fi` is `if` backwards; that is not a joke, it is the syntax.

```sh
# Basic if-else
if [ "$name" = "Alice" ]; then
  echo "Hello, Alice!"
else
  echo "You are not Alice."
fi

# if-elif-else
if [ "$age" -gt 18 ]; then
  echo "You are an adult."
elif [ "$age" -eq 18 ]; then
  echo "You are exactly 18."
else
  echo "You are a minor."
fi
```

Quote `"$name"`. Unquoted, a name with a space becomes extra arguments and the test falls over. Numbers use `-eq` / `-gt`; strings use `=` / `!=`. Mixing them is a classic "it worked on 5" bug.

**Common Comparison Operators:**

- `-eq`: equal
- `-ne`: not equal
- `-gt`: greater than
- `-lt`: less than
- `-ge`: greater than or equal to
- `-le`: less than or equal to
- `=`: string is equal to
- `!=`: string is not equal to
- `-z`: string is empty
- `-n`: string is not empty
- `-f`: file exists and is a regular file
- `-d`: directory exists

### `case` Statements

`case` is `if`/`elif` when you have a list of patterns instead of a chain of tests. The `;;` looks like a typo. It is not. Forget it and Bash will sulk.

```sh
case "$name" in
  "Alice")
    echo "Welcome, Alice."
    ;;
  "Bob")
    echo "Hey, Bob."
    ;;
  *)
    echo "Unknown user."
    ;;
esac
```

`*)` is the default branch: "none of the above." `esac` is `case` backwards. Bash is consistent about that bit.

---

## 4. Loops

### `for` Loops

`for` walks a list. Brace expansion `{1..5}` happens *before* the loop starts, which is why it is handy and also why it is not a substitute for reading a file line by line.

```sh
# Looping through a list of strings
for fruit in apple banana cherry; do
  echo "I like $fruit"
done

# Looping through numbers
for i in {1..5}; do
  echo "Number: $i"
done

# C-style for loop
for (( c=1; c<=5; c++ )); do
  echo "Count: $c"
done
```

The C-style form is there if you miss C. Most of the time the list form is the one you wanted.

### `while` Loops

`while` keeps going until the test fails. It is great for "until the file appears" and also how you write an infinite loop by accident. `Ctrl+C` is the eject button.

```sh
counter=1
while [ "$counter" -le 5 ]; do
  echo "Counter: $counter"
  ((counter++))
done
```

### Reading a file line by line

`for line in $(cat file)` looks reasonable and is wrong: it splits on spaces and expands globs. The loop that survives a line with a space in it is `while IFS= read -r`:

```sh
printf 'first line\nsecond line\n' > notes.txt
while IFS= read -r line; do
  printf '%s\n' "$line"
done < notes.txt
```

`IFS=` keeps leading whitespace. `-r` keeps backslashes. The redirect at the end feeds the loop without a pipe, so variables set inside it survive after the loop. That matters the moment you count something.

```sh
lines=0
while IFS= read -r line; do
  lines=$((lines + 1))
done < notes.txt
echo "$lines"
```

### The `set -e` arithmetic trap

`set -e` exits when a command fails. `((count++))` is a command, and it returns status 1 when the *result* is zero. So the first increment of a counter kills the script, silently, before the interesting part:

```sh
set -e
count=0
((count++))        # exits here: the result was 0, which counts as failure
echo "unreachable"
```

Use `count=$((count + 1))`, or `((count++)) || true` when you know why you are doing it. The `|| true` is a bandage; the arithmetic expansion is the fix.

---

## 5. Functions

A function is a named snippet you can call twice. `$1` is the first argument, not a movie sequel.

```sh
# Define a function
greet() {
  echo "Hello, $1!"
}

# Call the function with an argument
greet "Amit"
greet "World"
```

**Arguments in Functions:**

- `$1`, `$2`, ...: Positional arguments (first, second, etc.)
- `$#`: Number of arguments
- `$*`: All arguments as a single string
- `$@`: All arguments as separate strings

Use `"$@"` when you loop. `$*` mashes everything into one string, which is how `"Green Apple"` becomes two fruits.

```sh
print_args() {
  echo "Number of arguments: $#"
  for arg in "$@"; do
    echo "Argument: $arg"
  done
}

print_args "one" "two" "three"
```

---

## 6. Arrays

Bash arrays are zero-indexed, because of course they are. `"${fruits[@]}"` is the quoted form that keeps `Green Apple` as one item.

```sh
# Define an array
fruits=("Apple" "Banana" "Cherry")

# Access an element
echo "First fruit: ${fruits[0]}"

# Access all elements
echo "All fruits: ${fruits[@]}"

# Add an element
fruits+=("Orange")

# Loop through an array
for fruit in "${fruits[@]}"; do
  echo "$fruit"
done
```

The curly braces are not optional here. `$fruits[0]` is a different, worse program.

---

## 7. Reading User Input

`read` waits for a line. `IFS= read -r` is the grown-up form: keep leading spaces, keep backslashes. Without `-r`, a `\` in the name is a disappearing act.

```sh
echo "Please enter your name:"
IFS= read -r -p "Name: " user_name
echo "Hello, $user_name!"
```

`-p` prints the prompt on the same line. Practice in the terminal; a script that `read`s with no prompt looks hung.

---

## 8. Before you run it: `shellcheck`

[`shellcheck`](https://www.shellcheck.net/) reads your script the way a compiler reads C. It catches unquoted variables, `[` versus `[[`, useless `cat`, and the `rm` that expands to nothing when a variable is empty. That last one is worth the install by itself.

```sh
# Debian/Ubuntu/Mint
sudo apt install shellcheck

# Fedora
sudo dnf install shellcheck

# macOS
brew install shellcheck

# then
shellcheck linecount
```

A clean run prints nothing. A dirty run prints a line number, a severity, and a link to the explanation. Fix the error, read the warning, and ignore `SC1090` only when you know what it is.

Run it on anything an agent wrote before you run the thing itself. The linter does not care who typed it.

---
[← Two-hour path](../README.md#if-you-are-new) | [Aliases, if you want them →](shell_customization.md#aliases)
