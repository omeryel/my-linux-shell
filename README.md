# My Linux Shell
Writing a command line shell for linux in C

## What a shell does?
After a command is entered, the following things are done:

- A command is entered and if its length is non-null, keep it in history.
- Parsing : Parsing is the breaking up of commands into individual words and strings
- Checking for special characters like pipes, etc is done
- Checking if built-in commands are asked for.
- If pipes are present, handling pipes.
- Executing system commands and libraries by forking a child and calling execvp.
- Printing current directory name and asking for next input.
For keeping history of commands, recovering history using arrow keys and handling autocomplete using the tab key, we will be using the readline library provided by GNU.

Click [here](https://www.tutorialspoint.com/unix/unix-what-is-shell.htm) to know more about the working of shells.

## Implementation
To install the readline library, open the terminal window and write
`sudo apt-get install libreadline-dev`.

Enter the sudo password and hit 'y' in the next step.
- Printing the directory can be done using getcwd.
- Getting user name can be done by getenv(“USER”)
- Parsing can be done by using strsep(“”). It will separate words based on spaces. Always skip words with zero length to avoid storing of extra spaces.
- After parsing, check the list of built-in commands, and if present, execute it. If not, execute it as a system command. To check for built-in commands, store the commands in an array of character pointers, and compare all with strcmp().
- Note: “cd” does not work natively using execvp, so it is a built-in command, executed with chdir().
- For executing a system command, a new child will be created and then by using the execvp, execute the command, and wait until it is finished.
- Detecting pipes can also be done by using strsep(“|”).To handle pipes, first separate the first part of the command from the second part. Then after parsing each part, call both parts in two separate new children, using execvp. Piping means passing the output of first command as the input of second command.
    1. Declare an integer array of size 2 for storing file descriptors. File descriptor 0 is for reading and 1 is for writing.
    2. Open a pipe using the pipe() function.
    3. Create two children.
    4. In child 1->
    ```
    Here the output has to be taken into the pipe.
    Copy file descriptor 1 to stdout.
    Close  file descriptor 0.
    Execute the first command using execvp()
    ```
    5. In child 2->
    ```
    Here the input has to be taken from the pipe.
    Copy file descriptor 0 to stdin.
    Close file descriptor 1.
    Execute the second command using execvp()
    ```
    6. Wait for the two children to finish in the parent.
