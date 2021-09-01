---
title: Bash
tags:
- cheatsheet
- snippet
- linux
- bash
---
???+ warning
    This sheet is a supplement for my reference, so might not be useful to others

## File Downloads

### wget

Wget to location
```bash
wget -O savefile.txt http://web/file.txt
```

## Comparing files

- `comm` - column display of file diferences

- `diff` - You know, the most common diff format

### Diff

| Flag | Description           |
| ---- | --------------------- |
| -y   | Side by side          |
| -r   | Recursive (directory) |

## Scripting

### Special Variables

| Var         | Description                     |
| ----------- | ------------------------------- |
| `$0`        | Name of bash script             |
| `$1` - `$9` | First 9 arguments of script     |
| `$#`        | Number of arguments             |
| `$?`        | Status code of last run process |
| `$$`        | Process ID of current script    |
| `$USER`     | Current user                    |
| `$HOSTNAME` | Hostname of machine             |
| `$RANDOM`   | Random integer                  |
| `$LINENO`   | Current line number in script   |

### Reading Input

```bash
echo "What is your name?"
read name
echo "Hello, $name"
```

| Flag        | Description                              |
| ----------- | ---------------------------------------- |
| -p "Prompt" | Specify prompt                           |
| -s          | Don't echo user input, eg. for passwords |

### Conditionals

If Statement
```bash
if [ <some test> ]; then
  do something
fi  
```

Else Statement
```bash
if [ <some test> ]; then
  do something
else
  do somethingelse
fi  
```

???+ warning
    Conditionals are very white space sensitive

| Operator             | Description (True if)          |
| -------------------- | ------------------------------ |
| `!EXPRESSION`        | The EXPRESSION is false        |
| `-n STRING`          | STRING length > 0              |
| `-z STRING`          | STRING length = 0              |
| `STRING1 != STRING2` | Strings not equal              |
| `STRING1 = STRING2`  | Strings equal                  |
| `INT1 -eq INT2`      | Int equality                   |
| `INT1 -ne INT2`      | Int inequality                 |
| `INT1 -gt INT2`      | Int1 > INT2                    |
| `INT1 -lt INT2`      | Int1 < INT2                    |
| `INT1 -ge INT2`      | Int1 >= INT2                   |
| `INT1 -le INT2`      | Int1 <= INT2                   |
| `-d PATH`            | Path exists and is a directory |
| `-e PATH`            | Path exists                    |
| `-r PATH`            | Path exists and is readable    |
| `-w PATH`            | Path exists and is writable    |
| `-x PATH`            | Path exists and is executable  |

`[` is actually a program, which returns `1` for a false, or `0` for true.

As such you can do
```bash
if some command; then
  echo "Command succeeded"
else
  echo "Command failed"
fi
```
### Boolean Operators

```bash
command1 && command2
```
Will run command2 if and only if command1 succeeds (returns zero code)

```bash
command1 || command2
```
Will run command2 if and only if command1 fails (returns non-zero code)

Example
```bash
grep $user2 /etc/passwd && echo "$user2 found!" || echo "$user2 not found!"
```

### Loops

#### For Loops

Syntax
```bash
for var in <list>
do
  echo $var
done
```

Example
```bash
for ip in $(seq 1 10); do echo 10.11.1.$ip; done
```
or
```bash
for ip in {1..10}; do echo 10.11.1.$ip; done
```

Example 2
```bash
for textfile in *.txt; do echo "Found $textfile"; done
```

#### While loops

Syntax
```bash
while [ <some test> ]
do
  something
done
```
Previous for loop example
```bash
counter=1
while [ $counter -lt 10 ]
do
  echo "10.11.1.$counter"
  ((counter++))
done
```

### Functions

Syntax
```bash
function my_func {
  
}
```

### Variables

Two types of variables, shell variables and environment variables.

Shell variables are not passed to child processes
```bash
shell_var="stuff"
echo "shell_var=${shell_var}"
```

Environment variables are inherited by children and can be seen in `/proc/<pid>/environ`

Can set an environment variable with `export`
```bash
export SOME_ENV="stuff"
env | grep "SOME_ENV" # returns 'SOME_ENV=stuff'
ANOTHER_ENV="something else"
env | grep "ANOTHER_ENV" # Doesn't work
export ANOTHER_ENV
env | grep "ANOTHER_ENV" # returns 'ANOTHER_ENV=something else'
```


## Useful Commands

### xdg-open

Open the file or folder in whatever program is the defaul, eg. open a media player for a video, or your browser for a
website or a file browser for a directory.
