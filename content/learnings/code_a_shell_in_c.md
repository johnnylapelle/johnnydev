# Let's code a Shell in GO
I'm following the tutorial: [Write a Shell in C by Stephen Brennan](https://brennan.io/2015/01/16/write-a-shell-in-c/)

## Notes
What a shell do :
- Initialize: Read and execute its configuration file.
- Interpret: Reads commands from stdin and executes them.
- Terminate: After executing commands, it executes shutdown commands, frees up memory and terminates.

Three steps during the main loop:
- Parse
- Read
- Execute

We read the line, parsed it and execute it. Then we frees up the line and the args parsed.

In the loop function, we have a status variable, which is an int that will tell us when to the pipeline `read>parse>execute` is done. To do so we use a do-while loop.

### Read
To read the line, we can't know in advance the size of the command, we need to start with a block and if the command exceed it, you have to re-allocate it with more spaces.

So we get the first char with the `getchar()` function. Check if its the EOF or `\n`.
If it's the case, add the NULL char and return the buffer.
If not, add the char to the buffer and increment the position of the buffer by one to get the next char.
If the position of the buffer is greater than its capacity, we reallocate it.

### Parse
The parsing is similar to the reading, we allocated an array of tokens that hold every tokens. We used the function `strtok` from the `string.h` librarie.
When the buffer is too small for all the tokens we reallocate it with `realloc`

### How shell start processes
We want to create a process, that will execute our commands.
So we need 2 functions:
- `fork`: fork will duplicate the running process into two separate one.
- `exec`: execute replace the current running program with an entirely new one.

First we fork the current process and then with exec we execute our commands.

When fork is used it creates a duplication of the current process, the original is called the `parent` and the copy the `child`.
So the `parent` can manage the `child` by killing and cleaning his process.

### Builtin Commands
Some commands have to be builtin, like `cd`.
Cause if we use the `chdir` function inside the newly created process it will only change the current directory inside that process, but we want the change to be persistent to the parent.

So we code 3 `builtin functions`:
- `cd`
- `help`
- `exit`

We added those 3 functions to a list. So if we want to add commands, we just have to code them and add them to the list. Its scalable pretty easily.

### Execute
The execure part is pretty simple:

We get the args returned by the parser, check if the first args is in the builtin functions with `strcmp` and if its the case, call it the args.

And if the command not builtin, we then called the `lsh_launch`function that will launch a new process and execute the command in that process with the `execvp` function.
