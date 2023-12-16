### NFS

#### Instalation

sudo apt install nfs-kernel-server -y

#### Status

systemctl status nfs-kernel-server

#### Settings

/etc/exports

This file specifies which directories should be shared and the access rights for users and systems. It is also possible to configure settings such as the transfer speed and the use of encryption. NFS access rights determine which users and systems can access the shared directories and what actions they can perform. Here are some important access rights that can be configured in NFS:

| **Permissions** | **Description**                                                                                                                                      |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`                | Gives users and systems read and write permissions to the shared directory.                                                                                |
| `ro`                | Gives users and systems read-only access to the shared directory.                                                                                          |
| `no_root_squash`    | Prevents the root user on the client from being restricted to the rights of a normal user.                                                                 |
| `root_squash`       | Restricts the rights of the root user on the client to the rights of a normal user.                                                                        |
| `sync`              | Synchronizes the transfer of data to ensure that changes are only transferred after they have been saved on the file system.                               |
| `async`             | Transfers data asynchronously, which makes the transfer faster, but may cause inconsistencies in the file system if changes have not been fully committed. |

#### Details

root_squash - downgrades users who try to access the share as root (UIT/GID 0) to the nfsnobody user

all_squash - downgrades everyone to nfsnobody

#### Show shares

showmount --exports `<IP>`

#### Mount share

sudo mount -t nfs `<ip>`:`<share> <local_folder>`

-t specifies the file system type. Best to mount in /mnt

#### NFS interesting files

##### Settings

/etc/exports

#### NFS Imitation

##### Create user

sudo useradd `<username>`

This user has no password

##### Change UID

sudo usermod -u `<uid> <username>`

##### Change user GID

sudo groupmod -d `<gid> <username>`

##### Verify

cat /etc/passwd | grep `<username>`

### X

X is a portable, network-transparent window system for managing a windowed GUI. Essentially, when paired with a display manager, it serves as a full-fledged GUI which you can use to run programs that might not run headlessly.

The presence of .Xauthority and .xsession files in the home directory indicate that a display might be configured. This theory is further supported by the fact that
the display manager LightDM is found in the /etc/passwd file.
The .Xauthority file is used to store credentials in the form of cookies used by xauth when authenticating X sessions. When a session is started, the cookie is then used to authenticate the subsequent connections to that specific display.

#### Get cookie

cat <.Xauthority_file> | base64

Encode for easier transport.

#### Set cookie

export XAUTHORITY=`<cookie file>`

#### Get screenshot

xwd can be used to get a screenshot of the display in its current state

xwd -root -screen -silent -display :0 > /tmp/screen.xwd

-root: select root window
-screen: send GetImage request to root window
-silent: operate silently
-display: specify server to connect to (use w and get value in column FROM)

convert to png convert screen.xwd screen.png

### SSH

Install

sudo apt install openssh-server -y

Server status

systemctl status ssh

#### Settings

/etc/ssh/sshd_config


### Apache web server

#### Instalation

```shell-session
sudo apt install apache2 -y
```

#### Settings

##### Global settings

/etc/apache2/apache2.conf

##### Directory settings

.htaccess

This file allows us to configure certain directory-level settings, such as access controls, without having to customize the Apache configuration file. We can also add modules to get features like `mod_rewrite`, `mod_security`, and `mod_ssl` that help us improve the security of our web application.


### Python web server

#### Instalation

sudo apt install python3 -y

#### Starting the server

python3 -m http.server (will run on TCP/8000)

```shell-session
python3 -m http.server 443
```

```shell-session
python3 -m http.server --directory /home/cry0l1t3/target_files
```
