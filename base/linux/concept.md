# Concepts

## File

- Everything in Linux is a file (including directory)
- Extension is meaningless. A PNG file may not end with `.png`
- Unix-like operating systems, like Linux, maintain a single filesystem tree with devices attached at various points. This contrasts with other operating systems such as MS-DOS and Windows that maintain separate trees for each device (for example C:\, D:\, etc.)

## Directories

| Directory      | Comments                                                                                                                                                                                                                                                                                  |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| /              | The root directory, where everything begins                                                                                                                                                                                                                                               |
| /bin           | Contains binaries (programs) that must be present for the system to boot and run                                                                                                                                                                                                          |
| /boot          | Contains the Linux kernel, initial RAM disk image (for drivers needed at boot time), and the boot loader                                                                                                                                                                                  |
| /dev           | This is a special directory that contains device nodes                                                                                                                                                                                                                                    |
| /etc           | The /etc directory contains all of the system-wide configuration files. It also contains a collection of shell scripts that start each of the system services at boot time. Everything in this directory should be readable text.                                                         |
| /home          | In normal configurations, each user is given a directory in /home. Ordinary users can write files only in their home directories. This limitation protects the system from errant user activity                                                                                           |
| /lib           | Contains shared library files used by the core system programs. These are similar to DLLs in Windows                                                                                                                                                                                      |
| /media         | On modern Linux systems the /media directory will contain the mount points for removable media such as USB drives, CD-ROMs, etc. that are mounted automatically at insertion.                                                                                                             |
| /mnt           | On older Linux systems, the /mnt directory contains mount points for removable devices that have been mounted manually                                                                                                                                                                    |
| /opt           | The /opt directory is used to install “optional” software. This is mainly used to hold commercial software products that may be installed on your system                                                                                                                                  |
| /root          | This is the home directory for the root account                                                                                                                                                                                                                                           |
| /sbin          | This directory contains “system” binaries. These are programs that perform vital system tasks that are generally reserved for the superuser                                                                                                                                               |
| /tmp           | The /tmp directory is intended for storage of temporary, transient files created by various programs                                                                                                                                                                                      |
| /usr           | It contains all the programs and support files used by regular users                                                                                                                                                                                                                      |
| /usr/bin       | It contains the executable programs installed by your Linux distribution                                                                                                                                                                                                                  |
| /usr/lib       | The shared libraries for the programs in /usr/bin                                                                                                                                                                                                                                         |
| /usr/local     | The /usr/local tree is where programs that are not included with your distribution but are intended for system-wide use are installed. Programs compiled from source code are normally installed in /usr/local/bin                                                                        |
| /usr/bin       | Contains more system administration programs                                                                                                                                                                                                                                              |
| /usr/share     | /usr/share contains all the shared data used by programs in /usr/bin. This includes things like default configuration files, icons, screen backgrounds, sound files, etc                                                                                                                  |
| /usr/share/doc | Most packages installed on the system will include some kind of documentation. In /usr/share/doc, we will find documentation files organized by package                                                                                                                                   |
| /var           | With the exception of /tmp and /home, the directories we have looked at so far remain relatively static; that is, their contents don’t change. The /var directory tree is where data that is likely to change is stored. Various databases, spool files, user mail, etc. are located here |
| /var/log       | /var/log contains log files, records of various system activity. These are very important and should be monitored from time to time. The most useful one is /var/ log/messages. Note that for security reasons on some systems, you must be the superuser to view log files               |

## Standard Input, Output, and Error

- Keeping with the Unix theme of “everything is a file,” programs such as ls actually send their results to a special file called standard output (often expressed as stdout) and their status messages to another file called standard error (stderr). By default, both standard output and standard error are linked to the screen and not saved into a disk file.
- In addition, many programs take input from a facility called standard
input (stdin), which is, by default, attached to the keyboard.
- I/O redirection allows us to change where output goes and where input
comes from. Normally, output goes to the screen and input comes from the
keyboard, but with I/O redirection we can change that

## Process

- When a system starts up, the kernel initiates a few of its own activities as processes and launches a program called init. init, in turn, runs a series of shell scripts (located in /etc) called init scripts, which start all the system services.
- Many of these services are implemented as daemon programs, programs that just sit in the background and do their thing without having any user interface. So even if we are not logged in, the system is at least a little busy performing routine stuff.