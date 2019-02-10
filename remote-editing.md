# Remote Editing

To work with files on a remote server or in a docker image, one can
use `sshfs` to mount the remote file system to mount point on your
local machine.  Therefore files can be edited on your local machine
using your local tools.

To install `sshfs` on Mac via homebrew:
```bash
brew install sshfs
```

To mount a directory on a server:
```bash
mkdir -p /mnt/servername
sshfs username@serveraddress:target_directory /mnt/servername
```
