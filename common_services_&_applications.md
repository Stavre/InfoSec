### NFS

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
