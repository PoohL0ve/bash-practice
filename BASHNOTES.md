## All About Bash
Bash is a shell that allows the users to enter commands and returns the output of the commands.
```bash
# List Storage
ls
# Find out which shell
echo $SHELL
# Change to bash
which bash # tells which folder bash is in
/bin/bash 
```
In simple terms a script is a text file with commands in it.
```bash
# Let a script be executable
chmod +x myscript.sh
ls -l # Long listing
./myscript.sh # Executes the script
cat myscript.sh # reads the contents
# Current working directory
pwd
```
Scripts are only better than simply writing commands in the console when they save you time. Every bash script must start with a **Shebang**, which tells the intepreter which shell to use.
```bash
#!/bin/bash
echo "Hello, World!"
```

Variables in bash have no space after the assignment operator. To print the variable with the **echo** command use the *$* sign in front of it.
```bash
learn="I am learning"
echo $learn
# Store the output of a command
files=$(ls)
echo $USER
```
Environment variables are those already registered in the system and are in all caps, don't create variables in all caps. You can list them using the **env** command.