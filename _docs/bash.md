---

layout: default
title: "Bash Scripting Snippets"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

--- 

# Bash Conditionals Examples

You can find a link to the GNU manual below. 

> Reference: ![GNU Bash Conditionals](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

## 1. If-Else Conditional

### Syntax:
```bash
if [ condition ]; then
  # Code to execute if condition is true
elif [ another_condition ]; then
  # Code to execute if another_condition is true
else
  # Code to execute if none of the above conditions are true
fi
```

### Example:
```bash
#!/bin/bash
num=10

if [ $num -gt 5 ]; then
  echo "$num is greater than 5"
elif [ $num -eq 5 ]; then
  echo "$num is equal to 5"
else
  echo "$num is less than 5"
fi
```

---

## 2. Comparison Operators

### Numeric Comparisons:
| Operator | Description                   | Example                |
|----------|-------------------------------|------------------------|
| `-eq`    | Equal to                      | `[ $a -eq $b ]`       |
| `-ne`    | Not equal to                  | `[ $a -ne $b ]`       |
| `-gt`    | Greater than                  | `[ $a -gt $b ]`       |
| `-ge`    | Greater than or equal to      | `[ $a -ge $b ]`       |
| `-lt`    | Less than                     | `[ $a -lt $b ]`       |
| `-le`    | Less than or equal to         | `[ $a -le $b ]`       |

### String Comparisons:
| Operator | Description                   | Example                |
|----------|-------------------------------|------------------------|
| `=`      | Strings are equal            | `[ "$a" = "$b" ]`      |
| `!=`     | Strings are not equal        | `[ "$a" != "$b" ]`     |
| `<`      | String is less than (lexicographically)| `[ "$a" \< "$b" ]` |
| `>`      | String is greater than       | `[ "$a" \> "$b" ]`     |
| `-z`     | String is empty              | `[ -z "$a" ]`          |
| `-n`     | String is not empty          | `[ -n "$a" ]`          |

---

## 3. Logical Operators

| Operator | Description       | Example                          |
|----------|-------------------|----------------------------------|
| `!`      | Logical NOT       | `[ ! condition ]`               |
| `&&`     | Logical AND       | `[ condition1 ] && [ condition2 ]` |
| `||`     | Logical OR        | `[ condition1 ] || [ condition2 ]` |

### Example:
```bash
is_admin=true
is_logged_in=true

if [ "$is_admin" = "true" ] && [ "$is_logged_in" = "true" ]; then
  echo "Access granted"
else
  echo "Access denied"
fi
```

---

## 4. Using `[[` for Advanced Conditions

`[[` is more flexible than `[`. It supports pattern matching and avoids certain pitfalls.

### Example:
```bash
str="Hello"
if [[ $str == H* ]]; then
  echo "String starts with H"
fi
```

---

## 5. Case Statement

### Syntax:
```bash
case $variable in
  pattern1)
    # Code to execute if pattern1 matches
    ;;
  pattern2)
    # Code to execute if pattern2 matches
    ;;
  *)
    # Default case
    ;;
esac
```

### Example:
```bash
#!/bin/bash
fruit="apple"

case $fruit in
  apple)
    echo "It's an apple!"
    ;;
  banana)
    echo "It's a banana!"
    ;;
  *)
    echo "Unknown fruit."
    ;;
esac
```

---

## 6. File Tests

| Operator | Description                       | Example                 |
|----------|-----------------------------------|-------------------------|
| `-e`     | File exists                      | `[ -e file ]`           |
| `-f`     | File exists and is a regular file| `[ -f file ]`           |
| `-d`     | Directory exists                 | `[ -d directory ]`      |
| `-r`     | File is readable                 | `[ -r file ]`           |
| `-w`     | File is writable                 | `[ -w file ]`           |
| `-x`     | File is executable               | `[ -x file ]`           |
| `-s`     | File is not empty                | `[ -s file ]`           |

### Example:
```bash
#!/bin/bash
file="example.txt"

if [ -f "$file" ]; then
  echo "$file exists and is a regular file."
else
  echo "$file does not exist."
fi
```

---

## Summary
- Use **`if`**, **`case`**, and logical operators for branching.
- Use **`[ ]`** or **`[[ ]]`** for conditions.
- Combine numeric, string, and file tests as needed.

These tools are the foundation of Bash scripting logic.

--- 

# Bash Error Handling

## 1. Using `$?` (Exit Status)
Every command in Bash returns an exit status:
- **`0`**: Indicates success.
- **Non-zero**: Indicates failure.

### Example:
```bash
#!/bin/bash

cp file.txt /nonexistent-directory
if [ $? -ne 0 ]; then
  echo "Error: Failed to copy file."
fi
```

---

## 2. `set` Built-In Command

### **`set -e`**: Exit on Error
Automatically exits the script if a command returns a non-zero exit status.
```bash
#!/bin/bash
set -e

echo "This runs."
cp file.txt /nonexistent-directory  # Causes the script to exit
echo "This will not run."
```

### **`set -u`**: Treat Undefined Variables as Errors
Triggers an error if an undefined variable is used.
```bash
#!/bin/bash
set -u

echo "This is defined: $DEFINED_VAR"
```

### Common Combination:
```bash
set -euo pipefail
```
- **`-e`**: Exit on error.
- **`-u`**: Treat undefined variables as errors.
- **`pipefail`**: Ensures the script fails if any command in a pipeline fails.

---

## 3. `trap` Command
The `trap` command lets you handle signals and cleanup tasks.

### Example: Cleanup on Exit
```bash
#!/bin/bash
trap "echo 'An error occurred. Exiting...'; exit 1" ERR

cp file.txt /nonexistent-directory  # Error triggers trap
echo "This will not run."
```

### Example: Cleanup Temporary Files
```bash
#!/bin/bash
trap "rm -f /tmp/tempfile; echo 'Cleaned up.'" EXIT

touch /tmp/tempfile
echo "Doing work..."
```

---

## 4. Using `||` (OR) for Fallback
Run an alternate command if the first one fails.

### Example:
```bash
#!/bin/bash

mkdir /example-directory || echo "Failed to create directory."
```

---

## 5. Using `if` Statements
Manually check the success of commands.

### Example:
```bash
#!/bin/bash

if cp file.txt /nonexistent-directory; then
  echo "File copied successfully."
else
  echo "Failed to copy file."
fi
```

---

## 6. Using Functions for Error Handling
Encapsulate commands and error handling logic into functions.

### Example:
```bash
#!/bin/bash

error_exit() {
  echo "Error: $1"
  exit 1
}

cp file.txt /nonexistent-directory || error_exit "Failed to copy file."
```

---

## 7. Combining `pipefail` with Pipelines
By default, only the last command in a pipeline affects the exit status. Use `set -o pipefail` to fail the pipeline if any command fails.

### Example:
```bash
#!/bin/bash
set -o pipefail

cat file.txt | grep "text" | sort || echo "Pipeline failed."
```

---

## 8. Redirecting Errors to a Log File
Redirect error messages to a file for later inspection.

### Example:
```bash
#!/bin/bash

command 2> error.log
```
- **`2>`**: Redirects `stderr` (standard error).

---

## 9. Retry Logic
Retry a command if it fails.

### Example:
```bash
#!/bin/bash

for i in {1..3}; do
  command && break || echo "Attempt $i failed."
  sleep 1
done
```

---

## 10. Error Messages with `>&2`
Send error messages to `stderr`.

### Example:
```bash
#!/bin/bash

echo "This is an error message." >&2
```

---

## Summary of Error Handling Techniques:
- Use `$?` to check the exit status of commands.
- Use `set -euo pipefail` for more robust scripts.
- Use `trap` to handle unexpected errors and perform cleanup.
- Redirect `stderr` to log files for debugging.

These techniques can help make your Bash scripts more robust and capable of handling unexpected conditions.

---

## 11. Parameter expansion in bash 

Parameter expansion allows you to modify and manipulate the values of variables.


---

# Bash Shortcuts Cheat Sheet

## Navigating the Command Line
- `Ctrl+A`: Move to the beginning of the line.
- `Ctrl+E`: Move to the end of the line.
- `Alt+F`: Move forward one word.
- `Alt+B`: Move backward one word.
- `Ctrl+L`: Clear the screen (same as `clear` command).

## Editing Commands
- `Ctrl+U`: Cut (delete) from the cursor to the beginning of the line.
- `Ctrl+K`: Cut (delete) from the cursor to the end of the line.
- `Ctrl+W`: Cut (delete) the word before the cursor.
- `Alt+D`: Cut (delete) the word after the cursor.
- `Ctrl+Y`: Paste the last cut text (yank).
- `Ctrl+T`: Swap the last two characters before the cursor.
- `Alt+T`: Swap the last two words before the cursor.
- `Ctrl+_`: Undo the last editing action.

## Searching and Reusing Commands
- `Ctrl+R`: Reverse search through command history.
- `Ctrl+S`: Forward search through command history (if enabled).
- `Ctrl+G`: Cancel search in history.
- `Up Arrow`: Scroll backward through command history.
- `Down Arrow`: Scroll forward through command history.
- `!!`: Repeat the last command.
- `!<n>`: Execute command number `<n>` from history.
- `!<prefix>`: Execute the most recent command starting with `<prefix>`.

## Controlling Processes
- `Ctrl+C`: Kill the current process.
- `Ctrl+Z`: Suspend the current process.
- `jobs`: List suspended or background processes.
- `fg`: Resume the most recent suspended process in the foreground.
- `bg`: Resume the most recent suspended process in the background.

## Moving and Manipulating Text
- `Ctrl+Left Arrow`: Move to the beginning of the previous word (if enabled).
- `Ctrl+Right Arrow`: Move to the beginning of the next word (if enabled).
- `Alt+.`: Insert the last argument of the previous command.

## Shell Features
- `Ctrl+D`: Log out of the current shell session.
- `Ctrl+P`: Scroll backward through history (same as up arrow).
- `Ctrl+N`: Scroll forward through history (same as down arrow).
- `Ctrl+X Ctrl+E`: Open the current command in your default editor.
- `Tab`: Autocomplete files, directories, or commands.

## Terminal Control
- `Ctrl+S`: Pause terminal output.
- `Ctrl+Q`: Resume terminal output.
- `Ctrl+Z`: Suspend the current foreground process.
- `Ctrl+D`: Send EOF (end of file) to the terminal, often used to log out or close the terminal.

## Special Characters
- `Ctrl+V`: Insert the next character literally (e.g., type `Ctrl+V Tab` to insert a literal Tab character).
- `Ctrl+O`: Execute the command and bring the next one from history.

## Debugging
- `Ctrl+X Ctrl+V`: Print the version of Bash.

## Miscellaneous
- `Ctrl+G`: Exit the current editing mode or cancel a command.


