# Bash Scripting Basics

This guide covers the fundamental concepts of Bash scripting, including variables, conditionals, loops, and functions.

---

## 1. Shebang

Every shell script should begin with a "shebang" line, which tells the system what interpreter to use. For Bash scripts, this is:

```sh
#!/bin/bash
```

---

## 2. Variables

Variables store data. In Bash, you define them without spaces around the `=` sign.

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

### Environment vs. Local Variables

- **Local variables** are only available within the current script or shell session.
- **Environment variables** are available to the script and any child processes it starts. You create them using `export`.

```sh
# Local variable
local_var="I am local"

# Environment variable
export ENV_VAR="I am available everywhere"
```

---

## 3. Conditionals

### `if` Statements

`if` statements let you execute code based on a condition.

```sh
# Basic if-else
if [ "$name" = "Alice" ]; then
  echo "Hello, Alice!"
else
  echo "You are not Alice."
fi

# if-elif-else
if [ $age -gt 18 ]; then
  echo "You are an adult."
elif [ $age -eq 18 ]; then
  echo "You are exactly 18."
else
  echo "You are a minor."
fi
```

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

`case` statements are useful for matching a variable against multiple patterns.

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

---

## 4. Loops

### `for` Loops

`for` loops iterate over a list of items.

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

### `while` Loops

`while` loops continue as long as a condition is true.

```sh
counter=1
while [ $counter -le 5 ]; do
  echo "Counter: $counter"
  ((counter++))
done
```

---

## 5. Functions

Functions help you organize your code into reusable blocks.

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

Bash supports one-dimensional arrays.

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

---

## 7. Reading User Input

Use the `read` command to get input from the user.

```sh
echo "Please enter your name:"
read user_name
echo "Hello, $user_name!"
```

---
[← Back: Getting Started](getting_started.md) | [Next: Shell Customization →](shell_customization.md)
