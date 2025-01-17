# CSC 3510 Computer Organization

Carthage College Computer Organization Course

## Our platform - emulated Cortex-A53's

The emulated machines will be command-line only. A good development environment would be to edit on your native machine and sftp code down to the emulated machine where you run and debug. VS Code has a nice SFTP plug-in that can do this for you every time you save a file. Additionally, VSCode supports editing via ssh by downloading the *Remote Development-SSH* extention (which can be found [here](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)). The official getting started docs can be found [here](https://code.visualstudio.com/docs/remote/ssh-tutorial). This has the advantage of giving you direct access to the VM (including terminal), not just syncing files. 

## Installing QEMU on MacOS

This guide is derived from [here](https://translatedcode.wordpress.com/2017/07/24/installing-debian-on-qemus-64-bit-arm-virt-board/).


### Get QEMU

This assumes you already have ```brew```. If you don't you will need to install that first.

```text
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

```text
brew install qemu
```

or, if QEMU is already installed - check for an upgrade:

```text
brew upgrade qemu
```

`brew` may ask you to change the ownership of certain directories. Do as it suggests and rerun the `qemu` installation.

### Ensure you have QEMU

From a terminal, enter `qemu-system-aarch64` and hit return. The program should be found.

## Installing debian

I have built a working system for us to use directly.

### Getting the distro

Go to [here](https://www.mediafire.com/file/3n44u32drprfijf/aarch64.zip).

Download aarch64.zip.

### Unzipping the distro

1. Create the folder you wish to use for the distro.
2. Change directory into that directory.
3. Unzip the zip file in this directory.
4. Confirm files are present.
5. Delete the unneeded ones - sorry.

Here is a sample:

```text
[Donnager] /tmp $ mkdir aarch64
[Donnager] /tmp $ cd aarch64
[Donnager] /tmp/aarch64 $ unzip ~/Downloads/aarch64.zip
Archive:  /Users/perrykivolowitz/Downloads/aarch64.zip
  inflating: hda.qcow2
  inflating: initrd.img-4.9.0-8-arm64  
  inflating: installer-initrd.gz
  inflating: installer-linux
  inflating: vmlinuz-4.9.0-8-arm64
[Donnager] /tmp/aarch64 $ ls
hda.qcow2			installer-initrd.gz		vmlinuz-4.9.0-8-arm64
initrd.img-4.9.0-8-arm64	installer-linux
[Donnager] /tmp/aarch64 $ rm -f inst*
[Donnager] /tmp/aarch64 $ ls
hda.qcow2			initrd.img-4.9.0-8-arm64	vmlinuz-4.9.0-8-arm64
[Donnager] /tmp/aarch64 $
```

### Adding a QEMU alias

Edit the file that your terminal runs at launch. This could be a different file depending upon your Mac. On the most recent machines, this will be `~/.zprofile`. It may be that you have `~/.profile` instead. If you have neither, make one or the other. Do `ps`. If you see `bash` make `~/.profile`. If you see `zsh` make `~/.zprofile`.

Add this line to the bottom of the file.

```text
alias ARM='qemu-system-aarch64 -M virt -m 2048 -cpu cortex-a53 -kernel vmlinuz-4.9.0-8-arm64 -initrd initrd.img-4.9.0-8-arm64 -append root=/dev/vda2 -drive if=none,file=hda.qcow2,format=qcow2,id=hd -device virtio-blk-pci,drive=hd  -netdev user,id=mynet,hostfwd=tcp::2222-:22 -device virtio-net-pci,netdev=mynet -nographic -smp 2'
```

Save the file and exit.

### Ensure the alias works

You can use this alias when you are in the directory where you installed the distro. It will not work anywhere else. Ensure you are in the right directory now.

Run:

```text
source ~/.zprofile
```

`~/.zprofile` may be a different file depending upon your Mac.

Now run `alias`.

You should get this plus some other text potentially:

```text
[Donnager] /tmp/aarch64 $ alias
ARM='qemu-system-aarch64 -M virt -m 2048 -cpu cortex-a53   -kernel vmlinuz-4.9.0-8-arm64 -initrd initrd.img-4.9.0-8-arm64   -append root=/dev/vda2 -drive if=none,file=hda.qcow2,format=qcow2,id=hd   -device virtio-blk-pci,drive=hd  -netdev user,id=mynet,hostfwd=tcp::2222-:22   -device virtio-net-pci,netdev=mynet -nographic -smp 2'
```

### Ensure the distro boots

Run:

```text
ARM
```

Exit the distro by entering `root` as the user and `a` as the password. When the shell prompt is shown, type `shutdown now`. Always do this to exit the distro.

## Installing QEMU on Windows

Don't.

## Installing QEMU on WSL

1. Install WSL - I prefer you use Ubuntu 18.04 LTS or later.

This means you may have to enable the Windows Subsystem for Linux in the old Add Remove Programs -> Windows Features settings.

### Enable WSL

1. Completely update your Windows machine.
2. Open Settings app.
3. Click on Apps.
4. On right hand side, click on Programs and Features. Yet another settings dialog appears following a completely different standard. Microsoft is wonderful.
5. On the left of the new dialog click `Turn Windows features on or off`.
6. Scroll down to `Windows Subsystem for Linux`.
7. Ensure this is checked. If it is not, checking it will enbable WSL.
8. Hit OK and close settings related windows.

There is a tiny change your computer BIOS will need a tickle to enable Virtualization Technology. You won't find out until much later. Microsoft is Wonderful.

### Install Ubuntu on Windows

WSL is (for now) a command-line only honest-to-goodness Linux system with some small limitations (dealing with file systems). Any distro will do but I strongly suggest Ubuntu 18.04 LTS.

1. Press the windows key &#8862;
2. Microsoft Store
3. Search `Ubuntu`
4. Choose the right one: `Ubuntu 18.04 LTS`
5. Download it. It is about 221 MB.
6. Install it.
7. "Launch"
8. Wait.
9. Make a trivial user name and password. Don't make this complicated - something easy. I used user name `a` with password `a`. Entering a password on the command line does not echo. This is normal.

### Install QEMU

1. `sudo -i`
2. Enter password.
3. `apt update` If this is your first time, there may be hundreds of packages to update. Don't. Rather do it at home. See below.
4. `apt install qemu-system`
5. Enter
6. Wait
7. `exit` This will leave the super user shell.

At home, update it.

1. `sudo -i`
2. `apt update`
3. `apt upgrade`
4. `exit`

### Adding a QEMU alias

Edit the file that your terminal runs at launch. This will be `~/.profile`.

Add this line to the bottom of the file.

```text
alias ARM='qemu-system-aarch64 -M virt -m 2048 -cpu cortex-a53 -kernel vmlinuz-4.9.0-8-arm64 -initrd initrd.img-4.9.0-8-arm64 -append root=/dev/vda2 -drive if=none,file=hda.qcow2,format=qcow2,id=hd   -device virtio-blk-pci,drive=hd  -netdev user,id=mynet,hostfwd=tcp::2222-:22 -device virtio-net-pci,netdev=mynet -nographic -smp 2'
```

Save the file and exit.

### Ensure the alias works (repeat)

You can use this alias when you are in the directory where you installed the distro. It will not work anywhere else. Ensure you are in the right directory now.

Run:

```text
source ~/.profile
```

Now run `alias`.

You should get this plus some other text potentially:

```text
[Donnager] /tmp/aarch64 $ alias
ARM='qemu-system-aarch64 -M virt -m 2048 -cpu cortex-a53   -kernel vmlinuz-4.9.0-8-arm64 -initrd initrd.img-4.9.0-8-arm64   -append root=/dev/vda2 -drive if=none,file=hda.qcow2,format=qcow2,id=hd   -device virtio-blk-pci,drive=hd  -netdev user,id=mynet,hostfwd=tcp::2222-:22   -device virtio-net-pci,netdev=mynet -nographic -smp 2'
```

### Get the distro

Go to [here](https://www.mediafire.com/file/3n44u32drprfijf/aarch64.zip)

1. Download aarch64.zip.
2. Choose to `Open with`.
3. Create the folder where your distro will live.
4. Drag all the files to that folder.
5. Delete the two files which begin with `installer`. Sorry.

### Change directory to where your distro lives

This is not as trivial as it sounds. Your home directory in `Ubuntu` is not your Windows home directory.

1. Change directory to here: `/mnt/c/Users`
2. Then `cd` to your user account name.
3. **THIS** is your Windows home directory.
4. `cd` to the distro.

### Ensure the distro boots (repeat)

Run:

```text
ARM
```

Exit the distro by entering `root` as the user and `a` as the password. When the shell prompt is shown, type `shutdown now`. Always do this to exit the distro.

## BOTH PLATFORMS - NEVER USE THE `CONSOLE` WINDOW

Once you have launched the virtual machine, `ssh` into it. Don't use that console directly.

`ssh user@localhost -p 2222`
