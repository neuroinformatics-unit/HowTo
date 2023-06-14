# Linux and command line

## Ubuntu

### Distro update error
This is an error that appeared when updating to a new Ubuntu distribution.
Error msg: Software Updater - Not all updates can be installed.   
Solution: `sudo apt-get dist-upgrade`

### Terminal is not opening after installing a new Python version
If you installed a new Python version without the use of `conda`, there might be a mismatch in the python naming in a bin file.   
If it's possible open terminal through vscode or open a virtual terminal (VT) with CTRL + ALT + F3 and run `gnome-ternimal`.
Does it throw a Python error? If yes run `sudo nano /usr/bin/gnome-terminal` and change `#!/usr/bin/python3` to `#!/usr/bin/python3.10` if the version you're currently using is 3.10.  
Exit the VT via CTRL + ALT + F2.

### How to mount ceph storage on Ubuntu
In this example, we will **permanently** mount the `neuroinformatics` partition of `ceph`. The same procedure can be used to mount other partitions, such as those belonging to particular labs or projects.

The following instructions have been tested on **Ubuntu 20.04 LTS**.

#### Prerequisites
- Administrator rights (`sudo`) on the local Ubuntu machine
- `cifs-utils` installed via `sudo apt-get install cifs-utils`

#### Store your SWC credentials
First, create a file to store your SWC login credentials and save it in your home directory.
You can do that on the terminal via `nano ~/.smb_swc`. 

The ``.smb_swc`` file contents should look like this:
```bash
user=<SWC-USERNAME>
password=<SWC-PASSWORD>
domain=AD.SWC.UCL.AC.UK
```
Save the file and exit.

#### Create a mount point
Create a directory to mount the storage to. Sensible places to do this on Ubuntu would be in `/mnt` or `/media`. In the example below, we will use `/media/ceph-neuroinformatics`.

```bash 
sudo mkdir /media/ceph-neuroinformatics
```

#### Edit the fstab file
This will allow you to mount the storage automatically on boot. Before editing the file, make a backup of it, just in case.
```bash
sudo cp /etc/fstab /etc/fstab.bak
```
Now, open the file with your favourite editor (e.g. `sudo nano /etc/fstab`) and add new lines at the end of the file. 

For example:
```bash
# Mount ceph/neuroinformatics
//ceph-gw02.hpc.swc.ucl.ac.uk/neuroinformatics /media/ceph-neuroinformatics cifs uid=1002,gid=1002,credentials=/home/<LOCAL-USERNAME>/.smb_swc 0 0
```
Make sure to replace the following:
- `//ceph-gw02.hpc.swc.ucl.ac.uk/neuroinformatics` with the path to your desired partition, e.g. `//ceph-gw02.hpc.swc.ucl.ac.uk/<LAB-NAME>`
- `/media/ceph-neuroinformatics` with the path to the local mount point you created in the previous step.
- `uid=1002,gid=1002` with your local user ID and group ID. You can find these out by running `id` on the terminal.
- `<LOCAL-USERNAME>` with your username on the local Ubuntu machine on which you are mounting the storage

Save the file and exit.

#### Mount the storage
You can now mount the storage by running `sudo mount -a`. If you get an error, check the `fstab` file for typos. If you get no error, you should be able to see the mounted storage by running `df -h`.

The next time you reboot your machine, the storage should be mounted automatically.

#### Unmount the storage
To unmount the storage, run `sudo umount /media/ceph-neuroinformatics` (or whatever your mount point is).

To permanently unmount the storage, remove the lines you added to the `fstab` file and run `sudo mount -a` again.
