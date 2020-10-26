# Operating System Structure

## Service

- An operating system provides an environment for the execution of programs.
- It provides certain services to programs and to the users of those programs.
- OS provides functions that are helpful to the user:
  - User interface
  - Program execution
  - I/O operation
  - File-system manipulation
  - Process commnunication
  - Error detection
  
## Interrupt

- The occurrence of an event is usually signaled by an interrupt from either the hardware or the software. Hardware may trigger an interrupt at any time by sending a signal to the CPU, usually by way of the system bus. Software may trigger an interrupt by executing a special operation called a system call
- When the CPU is interrupted, it stops what it is doing and immediately transfers execution to a fixed location. The fixed location usually contains the starting address where the service routine for the interrupt is located. The interrupt service routine executes; on completion, the CPU resumes the interrupted computation.
- Interrupts cause the operating system to change a CPU from its current task and to run a kernel routine. 

## System Calls

- Provide an interface to the services made available by an operating system
- Example for system call: writing a simple program to read data from one file and copy them to another file

> The first input that the program will need is the names of the two files: the input file
and the output file. These names can be specified in many ways, depending on the operating-system design. One approach is for the program to ask the user for the names. In an interactive system, this approach will require a sequence of system calls, first to write a prompting message on the screen and then to read from the keyboard the characters that define the two files. On mouse-based and icon-based systems, a menu of file names is usually displayed in a window. The user can then use the mouse to select the source name, and a window can be opened for the destination name to be specified. This sequence requires many I/O system calls.  
  Once the two file names have been obtained, the program must open the input file and create the output file. Each of these operations requires another system call. Possible error conditions for each operation can require additional system calls. When the program tries to open the input file, for example, it may find that there is no file of that name or that the file is protected against access. In these cases, the program should print a message on the console (another sequence of system calls) and then terminate abnormally (another system call). If the input file exists, then we must create a new output file. We may find that there is already an output file with the same name. This situation may cause the program to abort (a system call), or we may delete the existing file (another system call) and create a new one (yet another system call). Another option, in an interactive system, is to ask the user (via a sequence of system calls to output the prompting message and to read the response from the terminal) whether to replace the existing file or to abort the program.  
  When both files are set up, we enter a loop that reads from the input file (a system call) and writes to the output file (another system call). Each read and write must return status information regarding various possible error conditions. On input, the program may find that the end of the file has been reached or that there was a hardware failure in the read (such as a parity error). The write operation may encounter various errors, depending on the output device (for example, no more disk space).  
  Finally, after the entire file is copied, the program may close both files (another system call), write a message to the console or window (more system calls), and finally terminate normally (the final system call)

- Types of system calls:
  - Process control: `fork`, `exit`, `wait`
  - File manipulation: `open`, `read`, `write`, `close`
  - Device manipulation: `ioctl`, `read`, `write`
  - Information maintenance: `getpid`, `alarm`, `sleep`
  - Communication: `pipe`, `shm_open`, `mmap`
  - Protection: `chmod`, `unmask`, `chown`
  
