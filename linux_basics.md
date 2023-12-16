#### File system

| **Path** | **Description**                                                                                                                                                                                                                                                                                                             |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/`          | The top-level directory is the root filesystem and contains all of the files required to boot the operating system before other filesystems are mounted as well as the files required to boot the other filesystems. After boot, all of the other filesystems are mounted at standard mount points as subdirectories of the root. |
| `/bin`       | Contains essential command binaries.                                                                                                                                                                                                                                                                                              |
| `/boot`      | Consists of the static bootloader, kernel executable, and files required to boot the Linux OS.                                                                                                                                                                                                                                    |
| `/dev`       | Contains device files to facilitate access to every hardware device attached to the system.                                                                                                                                                                                                                                       |
| `/etc`       | Local system configuration files. Configuration files for installed applications may be saved here as well.                                                                                                                                                                                                                       |
| `/home`      | Each user on the system has a subdirectory here for storage.                                                                                                                                                                                                                                                                      |
| `/lib`       | Shared library files that are required for system boot.                                                                                                                                                                                                                                                                           |
| `/media`     | External removable media devices such as USB drives are mounted here.                                                                                                                                                                                                                                                             |
| `/mnt`       | Temporary mount point for regular filesystems.                                                                                                                                                                                                                                                                                    |
| `/opt`       | Optional files such as third-party tools can be saved here.                                                                                                                                                                                                                                                                       |
| `/root`      | The home directory for the root user.                                                                                                                                                                                                                                                                                             |
| `/sbin`      | This directory contains executables used for system administration (binary system files).                                                                                                                                                                                                                                         |
| `/tmp`       | The operating system and many programs use this directory to store temporary files. This directory is generally cleared upon system boot and may be deleted at other times without any warning.                                                                                                                                   |
| `/usr`       | Contains executables, libraries, man files, etc.                                                                                                                                                                                                                                                                                  |
| `/var`       | This directory contains variable data files such as log files, email in-boxes, web application related files, cron files, and more.                                                                                                                                                                                               |

#### Change Bash prompt

https://bash-prompt-generator.org/

#### Getting help

Go see a shrink.

man `<tool>`

`<tool>` --help

`<tool> -h`

Another tool that can be useful in the beginning is `apropos`. Each manual page has a short description available within it. This tool searches the descriptions for instances of a given keyword.

apropos `<keyword>`

Another useful resource to get help if we have issues to understand a long command is: [https://explainshell.com/](https://explainshell.com/)

#### System information

| **Command** | **Description**                                                                                                              |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `whoami`        | Displays current username.                                                                                                         |
| `id`            | Returns users identity                                                                                                             |
| `hostname`      | Sets or prints the name of current host system.                                                                                    |
| `uname`         | Prints basic information about the operating system name and system hardware.                                                      |
| `pwd`           | Returns working directory name.                                                                                                    |
| `ifconfig`      | The ifconfig utility is used to assign or to view an address to a network interface and/or configure network interface parameters. |
| `ip`            | Ip is a utility to show or manipulate routing, network devices, interfaces and tunnels.                                            |
| `netstat`       | Shows network status.                                                                                                              |
| `ss`            | Another utility to investigate sockets.                                                                                            |
| `ps`            | Shows process status.                                                                                                              |
| `who`           | Displays who is logged in.                                                                                                         |
| `env`           | Prints environment or sets and executes command.                                                                                   |
| `lsblk`         | Lists block devices.                                                                                                               |
| `lsusb`         | Lists USB devices                                                                                                                  |
| `lsof`          | Lists opened files.                                                                                                                |
| `lspci`         | Lists PCI devices.                                                                                                                 |

#### SSH login

ssh `<username>@<ip address>`

#### Create files

touch <file_name>

```shell-session
touch ./Storage/local/user/userinfo.txt
```

#### Create directories

mkdir <directory_name>

mkdir -p Storage/local/user/documents

#### See directory as tree

tree `<directory_path>`

#### Move files and directories

```shell-session
mv <file/directory> <renamed file/directory>
```

```shell-session
mv info.txt information.txt
```

```shell-session
 mv information.txt readme.txt Storage/
```

// moves multiple files to Storage/

#### Copy

```shell-session
cp Storage/readme.txt Storage/local/
```

#### Editing files

Most used: Vim, nano, Vi and Emacs.

##### Vim modes

| **Mode** | **Description**                                                                                                                                                                                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Normal`     | In normal mode, all inputs are considered as editor commands. So there is no insertion of the entered characters into the editor buffer, as is the case with most other editors. After starting the editor, we are usually in the normal mode.                  |
| `Insert`     | With a few exceptions, all entered characters are inserted into the buffer.                                                                                                                                                                                     |
| `Visual`     | The visual mode is used to mark a contiguous part of the text, which will be visually highlighted. By positioning the cursor, we change the selected area. The highlighted area can then be edited in various ways, such as deleting, copying, or replacing it. |
| `Command`    | It allows us to enter single-line commands at the bottom of the editor. This can be used for sorting, replacing text sections, or deleting them, for example.                                                                                                   |
| `Replace`    | In replace mode, the newly entered text will overwrite existing text characters unless there are no more old characters at the current cursor position. Then the newly entered text will be added.                                                              |

#### Find files and directories

##### Which

One of the common tools is `which`. This tool returns the path to the file or link that should be executed. This allows us to determine if specific programs, like **cURL**, **netcat**, **wget**, **python**, **gcc**, are available on the operating system. Let us use it to search for Python in our interactive instance.

```shell-session
user@htb[/htb]$ which python

/usr/bin/python
```

##### Find

```shell-session
StavreAcad@htb[/htb]$ find <location> <options>
```

Let us look at an example of what such a command with multiple options would look like.

  **Syntax - find**

```shell-session
StavreAcad@htb[/htb]$ find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 
```

| **Option**        | **Description**                                                                                                                                                                                                                                                          |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-type f`             | Hereby, we define the type of the searched object. In this case, '`f`' stands for '`file`'.                                                                                                                                                                                |
| `-name *.conf`        | With '`-name`', we indicate the name of the file we are looking for. The asterisk (`*`) stands for 'all' files with the '`.conf`' extension.                                                                                                                             |
| `-user root`          | This option filters all files whose owner is the root user.                                                                                                                                                                                                                    |
| `-size +20k`          | We can then filter all the located files and specify that we only want to see the files that are larger than 20 KiB.                                                                                                                                                           |
| `-newermt 2020-03-03` | With this option, we set the date. Only files newer than the specified date will be presented.                                                                                                                                                                                 |
| `-exec ls -al {} \;`  | This option executes the specified command, using the curly brackets as placeholders for each result. The backslash escapes the next character from being interpreted by the shell because otherwise, the semicolon would terminate the command and not reach the redirection. |
| `2>/dev/null`         | This is a `STDERR` redirection to the '`null device`', which we will come back to in the next section. This redirection ensures that no errors are displayed in the terminal. This redirection must `not` be an option of the 'find' command.                            |

##### Locate

It will take much time to search through the whole system for our files and directories to perform many different searches. The command `locate` offers us a quicker way to search through the system. In contrast to the `find` command, `locate` works with a local database that contains all information about existing files and folders. We can update this database with the following command.

  **Syntax - find**

```shell-session
StavreAcad@htb[/htb]$ sudo updatedb
```

If we now search for all files with the "`.conf`" extension, you will find that this search produces results much faster than using `find`.

  **Syntax - find**

```shell-session
StavreAcad@htb[/htb]$ locate *.conf

/etc/GeoIP.conf
/etc/NetworkManager/NetworkManager.conf
/etc/UPower/UPower.conf
/etc/adduser.conf
<SNIP>
```

However, this tool does not have as many filter options that we can use. So it is always worth considering whether we can use the `locate` command or instead use the `find` command. It always depends on what we are looking for.

#### File descriptors and Redirections

A file descriptor (FD) in Unix/Linux operating systems is an indicator of connection maintained by the kernel to perform Input/Output (I/O) operations. In Windows-based operating systems, it is called filehandle. It is the connection (generally to a file) from the Operating system to perform I/O operations (Input/Output of Bytes). By default, the first three file descriptors in Linux are:

1. Data Stream for Input
   * `STDIN – 0`
2. Data Stream for Output
   * `STDOUT – 1`
3. Data Stream for Output that relates to an error occurring.
   * `STDERR – 2`

##### Redirect STDERR to /dev/null

```shell-session
find /etc/ -name shadow 2>/dev/null
```

##### Redirect STDOUT to file

We're redirecting STDERR to /dev/null and STDOUT to results.txt file.

```shell-session
find /etc/ -name shadow 2>/dev/null > results.txt
```

##### Redirect STDOUT and STDERR to separate files

```shell-session
find /etc/ -name shadow 2> stderr.txt 1> stdout.txt
```

##### Redirecting STDIN

As we have already seen, in combination with the file descriptors, we can redirect errors and output with greater-than character (`>`). This also works with the lower-than sign (`<`). However, the lower-than sign serves as standard input (`FD 0 - STDIN`). These characters can be seen as "`direction`" in the form of an arrow that tells us "`from where`" and "`where to`" the data should be redirected. We use the `cat` command to use the contents of the file "`stdout.txt`" as `STDIN`.

  **Redirect STDIN**

```shell-session
StavreAcad@htb[/htb]$ cat < stdout.txt
```

![image](https://academy.hackthebox.com/storage/modules/18/find5.png)

##### Redirect STDOUT and Append to a file

When we use the greater-than sign (`>`) to redirect our `STDOUT`, a new file is automatically created if it does not already exist. If this file exists, it will be overwritten without asking for confirmation. If we want to append `STDOUT` to our existing file, we can use the double greater-than sign (`>>`).

##### **Redirect STDOUT and Append to a File**

```shell-session
StavreAcad@htb[/htb]$ find /etc/ -name passwd >> stdout.txt 2>/dev/null
```

##### Redirecting STDIN Stream to a File

We can also use the double lower-than characters (`<<`) to add our standard input through a stream. We can use the so-called `End-Of-File` (`EOF`) function of a Linux system file, which defines the input's end. In the next example, we will use the `cat` command to read our streaming input through the stream and direct it to a file called "`stream.txt`."

  **Redirect STDIN Stream to a File**

```shell-session
StavreAcad@htb[/htb]$ cat << EOF > stream.txt
```

![image](https://academy.hackthebox.com/storage/modules/18/find6.png)

##### Pipes

Another way to redirect `STDOUT` is to use pipes (`|`). These are useful when we want to use the `STDOUT` from one program to be processed by another. One of the most commonly used tools is `grep`, which we will use in the next example. Grep is used to filter `STDOUT` according to the pattern we define. In the next example, we use the `find` command to search for all files in the "`/etc/`" directory with a "`.conf`" extension. Any errors are redirected to the "`null device`" (`/dev/null`). Using `grep`, we filter out the results and specify that only the lines containing the pattern "`systemd`" should be displayed.

```shell-session
StavreAcad@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd
```

![image](https://academy.hackthebox.com/storage/modules/18/find7.png)

The redirections work, not only once. We can use the obtained results to redirect them to another program. For the next example, we will use the tool called `wc`, which should count the total number of obtained results.

```shell-session
StavreAcad@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd | wc -l
```

![image](https://academy.hackthebox.com/storage/modules/18/find8.png)

#### Filter contents

##### More

exit by pressing q

##### Less

exit by pressing q

##### Head

Prints the first 10 lines of a file

head /etc/passwd

##### Tail

Prints the last 10 lines of a file

##### Sort

```shell-session
cat /etc/passwd | sort
```

##### Grep

Searches for lines containing specified pattern

```shell-session
 cat /etc/passwd | grep "/bin/bash"
```

-v excludes lines containing that pattern

##### Cut

Removes sections from each line.

-d - sets the delimiter character

-f - sets the section you want to display (indexing starts at 1)

See man for more details. Man page really simple.

```shell-session
cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1
```

##### Tr

Translate or delete characters

tr `<what to replace> <replace with>`

```shell-session
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "
```

##### Column

Columnate lists

-o, --output-separator string
           Specify the columns delimiter for table output (default is two spaces).

-s, --separator separators
           Specify the possible input item delimiters (default is whitespace).

-t, --table
           Determine the number of columns the input contains and create a table. Columns are delimited with whitespace, by default, or with the characters supplied using the --output-separator option. Table output is useful for    pretty-printing.

See man page. Man page is really simple.

```shell-session
cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t
```

##### Awk

Pattern scanning and processing language

Best of luck with awk.

##### Sed

stream editor for filtering and transforming text

Good luck with this one too:)

##### Wc

print newline, word, and byte counts for each file

See man page.

#### Regex

Regular expressions (`RegEx`) are an art of expression language to search for patterns in text and files. They can be used to find and replace text, analyze data, validate input, perform searches, and more. In simple terms, they are a filter criterion that can be used to analyze and manipulate strings. They are available in various programming languages and programs and are used in many different ways and functions.

A regular expression is a sequence of letters and symbols that form a search pattern. In addition, regular expressions can be created with patterns called metacharacters. Meta characters are symbols that define the search pattern but have no literal meaning. We can use it in tools like `grep` or `sed` or others.

##### Grouping

Among other things, regex offers us the possibility to group the desired search patterns. Basically, regex follows three different concepts, which are distinguished by the three different brackets:

Suppose we use the `OR` operator. The regex searches for one of the given search parameters. In the next example, we search for lines containing the word `my` or `false`. To use these operators, you need to apply the extended regex using the `-E` option in grep.

###### **OR operator**

```shell-session
cry0l1t3@htb:~$ grep -E "(my|false)" /etc/passwd

lxd:x:105:65534::/var/lib/lxd/:/bin/false
pollinate:x:109:1::/var/cache/pollinate:/bin/false
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

Since one of the two search parameters always occurs in the three lines, all three lines are displayed accordingly. However, if we use the `AND` operator, we will get a different result for the same search parameters.

###### **AND operator**

```shell-session
cry0l1t3@htb:~$ grep -E "(my.*false)" /etc/passwd

mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

Basically, what we are saying with this command is that we are looking for a line where we want to see both `my` and `false`. A simplified example would also be to use `grep` twice and look like this:

```shell-session
cry0l1t3@htb:~$ grep -E "my" /etc/passwd | grep -E "false"

mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

#### Permission Management

Under Linux, permissions are assigned to users and groups. Each user can be a member of different groups, and membership in these groups gives the user specific, additional permissions. Each file and directory belongs to a specific user and a specific group. So the permissions for users and groups that defined a file are also defined for the respective owners. When we create new files or directories, they belong to the group we belong to and us.

When a user wants to access the contents of a Linux directory, they must first traverse the directory, which means navigating to that directory, requiring the user to have `execute` permissions on the directory. Without this permission, the user cannot access the directory's contents and will instead be presented with a “`Permission Denied`" error message.


It is important to note that `execute` permissions are necessary to traverse a directory, no matter the user's level of access. Also, `execute` permissions on a directory do not allow a user to execute or modify any files or contents within the directory, only to traverse and access the content of the directory.

To execute files within the directory, a user needs `execute` permissions on the corresponding file. To modify the contents of a directory (create, delete, or rename files and subdirectories), the user needs `write` permissions on the directory.

The whole permission system on Linux systems is based on the octal number system, and basically, there are three different types of permissions a file or directory can be assigned:

* (`r`) - Read
* (`w`) - Write
* (`x`) - Execute

The permissions can be set for the `owner`, `group`, and `others` like presented in the next example with their corresponding permissions.

```shell-session
cry0l1t3@htb[/htb]$ ls -l /etc/passwd

- rwx rw- r--   1 root root 1641 May  4 23:42 /etc/passwd
- --- --- ---   |  |    |    |   |__________|
|  |   |   |    |  |    |    |        |_ Date
|  |   |   |    |  |    |    |__________ File Size
|  |   |   |    |  |    |_______________ Group
|  |   |   |    |  |____________________ User
|  |   |   |    |_______________________ Number of hard links
|  |   |   |_ Permission of others (read)
|  |   |_____ Permissions of the group (read, write)
|  |_________ Permissions of the owner (read, write, execute)
|____________ File type (- = File, d = Directory, l = Link, ... )
```

---

##### Change Permissions


We can modify permissions using the `chmod` command, permission group references (`u` - owner, `g` - Group, `o` - others, `a` - All users), and either a [`+`] or a [`-`] to add remove the designated permissions. In the following example, a user creates a new shell script owned by that user, not executable, and set with read permissions for all users.

```shell-session
cry0l1t3@htb[/htb]$ ls -l shell

-rwxr-x--x   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```

We can then apply `read` permissions for all users and see the result.

```shell-session
cry0l1t3@htb[/htb]$ chmod a+r shell && ls -l shell

-rwxr-xr-x   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```
