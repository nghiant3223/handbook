# Operating System Structure

## Operating System Service

- An operating system provides an environment for the execution of programs.
- It provides certain services to programs and to the users of those programs.
- OS provides functions that are helpful to the user:
  - User interface
  - Program execution
  - I/O operation
  - File-system manipulation
  - Process commnunication
  - Error detection
  
## System Calls

- Provide an interface to the services made available by an operating system
- Example for system call: writing a simple program to read data from one file and copy them to another file
> The first input that the program will need is the names of the two files: the input file
and the output file. These names can be specified in many ways, depending on the operating-system design. One approach is for the program to ask the user for the names. In an interactive system, this approach will require a sequence of system calls, first to write a prompting message on the screen and then to read from the keyboard the characters that define the two files. On mouse-based and icon-based systems, a menu of file names is usually displayed in a window. The user can then use the mouse to select the source name, and a window can be opened for the destination name to be specified. This sequence requires many I/O system calls
