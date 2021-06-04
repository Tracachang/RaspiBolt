---
layout: default
title: Raspberry Pi
nav_order: 20
---
<!-- markdownlint-disable MD014 MD022 MD025 MD033 MD040 -->

# Raspberry Pi
{: .no_toc }

We configure the Raspberry Pi and install the Linux operating system.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Preparing the operating system

The node runs headless, that means without keyboard or display, so the operating system Raspbian Buster Lite is used.

1. Download the [Manjaro Pi4 minimal](https://manjaro.org/downloads/arm/raspberry-pi-4/arm8-raspberry-pi-4-minimal/){:target="_blank"} disk image
2. Write the disk image to your SD card with [this guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md){:target="_blank"}

### Enable Secure Shell

Without keyboard or screen, no direct interaction with the Pi is possible during the initial setup.
After writing the image to the microSD card, create an empty file called “ssh” (without extension) in the main directory of the card.
This causes the Secure Shell (ssh) to be enabled from the start and we will be able to login remotely.

* Create a file `ssh` in the boot partition of the microSD card


### Start your Pi

* Safely eject the sd card from your computer
* Insert the sd card into the Pi
* Connect the Pi to your network with an ethernet cable
* Start the Pi by connecting it to the power adapter using the USB-C cable

---

## Connecting to the Raspberry Pi

### Find it

The Pi is starting and gets a new address from your home network.
Finding it can be a bit tricky without a screen.
If you're lucky, you don't need to know this address and can just connect using mDNS.

* If you have an android phone connected to the network (Wifi) you can install the app [fing](https://play.google.com/store/apps/details?id=com.overlook.android.fing&hl=en_US&gl=US) which will scan for all connected devices in your network. Once you have the IP you can move to the next step: Access with Secure Shell

* On your regular computer, or mobile phone you can access to the Router Configuration web page, go to the following URL in your webbrowser http://192.168.1.1 log in, and you will see all connected devices. Once you have the IP of your raspberry pi you will can connect with ssh.


### Access with Secure Shell

Now it’s time to connect to the Pi via SSH and get to work.
For that, a Secure Shell (SSH) client is needed.

If you need to provide connection details, use the following settings:

* host name: the ip address like `192.168.1.20`
* port: `22`
* username: `pi`
* password:  `raspberry`.

Install and start the SSH client for your operating system:

* Windows: PuTTY ([Website](https://www.putty.org){:target="_blank"})
* MacOS and Linux: from the Terminal, use the native command:
  * `ssh pi@192.168.1.20`

<script id="asciicast-UxufwsDLfdhIfitCfBbHXx4mA" src="https://asciinema.org/a/UxufwsDLfdhIfitCfBbHXx4mA.js" async></script>

🔍 *more: [using SSH with Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md){:target="_blank"}*

---

## Initial Setup

When you will connect to ssh you'll see a menu to install, it is very straightforward you'll be asked a username, we will use "admin" for this tutorial, set the password `password [A]` and when password for root is asked we will write again the same `password [A]`. We will choose local settings like language and location, and configure wifi if needed. Once the setup is done we will see the command line. 

## The command line

We are going to work on the command line of the Pi, which may be new to you.
Find some basic information below, it will help you navigate and interact with your Pi.

You enter commands and the Pi answers by printing the results below your command.
To make it clear where a command begins, every command in this guide starts with the `$` sign. The system response is marked with the `>` character.

In the following example, just enter `ls -la` and press the enter/return key:

```sh
$ ls -la
> example system response
# This is a comment, don't enter this on the command line
```

* **Auto-complete commands**:
  When you enter commands, you can use the `Tab` key for auto-completion, eg. for commands, directories or filenames.

* **Command history**:
  by pressing ⬆️ (arrow up) and ⬇️ (arrow down) on your keyboard, you can recall your previously entered commands.

* **Common Linux commands**:
  For a very selective reference list of Linux commands, please refer to the [FAQ](raspibolt_faq.md) page.

* **Use admin privileges**:
  Our regular user has no admin privileges.
  If a command needs to edit the system configuration, we need to use the `sudo` ("superuser do") command as prefix.
  Instead of editing a system file with `nano /etc/fstab`, we use `sudo nano /etc/fstab`.

  For security reasons, the user "bitcoin" cannot use the `sudo` command.

* **Using the Nano text editor**:
  We use the Nano editor to create new text files or edit existing ones.
  It's not complicated, but to save and exit is not intuitive.

  * Save: hit `Ctrl-O` (for Output), confirm the filename, and hit the `Enter` key
  * Exit: hit `Ctrl-X`

* **Copy / Paste**:
  If you are using Windows and the PuTTY SSH client, you can copy text from the shell by selecting it with your mouse (no need to click anything), and paste stuff at the cursor position with a right-click anywhere in the ssh window.

  In other Terminal programs, copy/paste usually works with `Ctrl`-`Shift`-`C` and `Ctrl`-`Shift`-`V`.

<script id="asciicast-mfa3ZyTE3K1RdbAXowaFjEJZK" src="https://asciinema.org/a/mfa3ZyTE3K1RdbAXowaFjEJZK.js" async></script>

---

## Working on the Raspberry Pi

You are now on the command line of your own Bitcoin node.
Let's start with the configuration.

### Software update

It is important to keep the system up-to-date with security patches and application updates.
The “Advanced Packaging Tool” (apt) makes this easy.

💡 Do this regularly every few months to get security related updates.

```sh
$ sudo pacman -Syu
```

Make sure that all necessary software packages are installed:

```sh
$ sudo pacman -S htop git curl bash-completion jq qrencode hdparm
```

<script id="asciicast-hg9s5u5vzv04OpUPwTFfqqrLy" src="https://asciinema.org/a/hg9s5u5vzv04OpUPwTFfqqrLy.js" async></script>

### Add users

The bitcoin and lightning processes will run in the background (as a "daemon") and use the separate user “bitcoin” for security reasons.
This user does not have admin rights and cannot change the system configuration.

* Enter the following command, set your `password [A]` and confirm all questions with the enter/return key.

  ```sh
  $ sudo useradd bitcoin -m
  ```

* For convenience, the user "admin" is also a member of the group "bitcoin", giving it read-only privileges to configuration files.

  ```sh
  $ sudo gpasswd -a admin bitcoin
  ```

* Restart your RaspiBolt.

  ```sh
  $ sudo reboot
  ```

<script id="asciicast-8uhMDvDcDNf3cUT6A3FcqN4lo" src="https://asciinema.org/a/8uhMDvDcDNf3cUT6A3FcqN4lo.js" async></script>

---

## Attach external drive

To store the blockchain, we need a lot of space.
As a server installation, the Linux native file system Ext4 is the best choice for the external hard disk, so we will format the hard disk, erasing all previous data.
The external hard disk is then attached to the file system and can be accessed as a regular folder (this is called “mounting”).

🚨 **Existing data on this drive will be deleted!**

### Log in as "admin"

* Do not yet connect the external drive to your Pi, we need to check some things first.
* Start your Raspberry Pi by unplugging it and connecting the power cable again.
* Log in using SSH, but now with the user `admin`, your `password [A]` and the new hostname (e.g. `raspibolt.local`) or the IP address.

  ```sh
  $ ssh admin@192.168.1.20
  ```

* To change system configuration and files that don't belong to the "admin", you have to prefix commands with `sudo`.
  You will be prompted to enter your admin password from time to time for increased security.

### Make sure USB3 is performant

The Raspberry Pi 4 supports USB3 drives, but is very picky.
A lot of USB3 adapters for external drives are not compatible and need a manual workaround to be usable.
We will now check if your drive works well as-is, or if additional configuration is needed.

🔍 *more: [Raspberry Pi forum: bad performance with USB3 SSDs](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=245931){:target="_blank"}*

* First, lets get some information about your drive from the kernel messages.
  Clear the kernel buffer, and follow the new messages (let the last command run):

  ```sh
  $ sudo dmesg -C
  $ sudo dmesg -w
  ```

* Connect your external drive to the blue USB3 ports of the running Raspberry Pi, preferably with a good cable that came with the drive.

  Once the system recognizes it, details are automatically displayed by the `dmesg` command.

  ```
  [  726.547907] usb 2-1: new SuperSpeed Gen 1 USB device number 3 using xhci_hcd
  [  726.579304] usb 2-1: New USB device found, idVendor=152d, idProduct=0578, bcdDevice= 3.01
  [  726.579321] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
  [  726.579333] usb 2-1: Product: USB 3.0 Device
  [  726.579346] usb 2-1: Manufacturer: USB 3.0 Device
  [  726.579357] usb 2-1: SerialNumber: 000000005B3E
  [  726.582254] usb 2-1: UAS is blacklisted for this device, using usb-storage instead
  [  726.582350] usb 2-1: UAS is blacklisted for this device, using usb-storage instead
  [  726.582364] usb-storage 2-1:1.0: USB Mass Storage device detected
  [  726.582674] usb-storage 2-1:1.0: Quirks match for vid 152d pid 0578: 1800000
  [  726.582783] scsi host0: usb-storage 2-1:1.0
  [  727.598422] scsi 0:0:0:0: Direct-Access     INTENSO  SATA III SSD     0301 PQ: 0 ANSI: 6
  [  727.599182] sd 0:0:0:0: Attached scsi generic sg0 type 0
  [  727.605796] sd 0:0:0:0: [sda] 937703088 512-byte logical blocks: (480 GB/447 GiB)
  [  727.606519] sd 0:0:0:0: [sda] Write Protect is off
  [  727.606536] sd 0:0:0:0: [sda] Mode Sense: 47 00 00 08
  [  727.607982] sd 0:0:0:0: [sda] Disabling FUA
  [  727.607998] sd 0:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
  [  727.611337]  sda: sda1
  [  727.614890] sd 0:0:0:0: [sda] Attached SCSI disk
  ```

* Make a note of the values shown for `idVendor` and `idProduct` (in this case "152d" and "0578").
  Then, exit `dmesg` with `Ctrl`-`C`.

* List all block devices with additional information.
  The list shows the devices (e.g. `sda`) and the partitions they contain (e.g. `sda1`).

  Make a note of the partition name you want to use (in this case "sda1").

  ```sh
  $ lsblk -o NAME,MOUNTPOINT,UUID,FSTYPE,SIZE,LABEL,MODEL
  > NAME        MOUNTPOINT UUID                                 FSTYPE   SIZE LABEL  MODEL
  > sda                                                                447.1G        SATA_III_SSD
  > └─sda1                 9ec0b784-d448-4757-a3b2-8abd57c544f3 ext4   447.1G
  > mmcblk0                                                             14.9G
  > ├─mmcblk0p1 /boot      5203-DB74                            vfat     256M boot
  > └─mmcblk0p2 /          2ab3f8e1-7dc6-43f5-b0db-dd5759d51d4e ext4    14.6G rootfs
  ```

* If your external drive (e.g. `sda`) does not contain any partitions (e.g. no `sda1`), create a partition first using as described here:
  <https://superuser.com/questions/643765/creating-ext4-partition-from-console>

* Now, let's test the read performance of your drive.
  Make sure to use the right partition name (used with the `/dev/` prefix).

  ```sh
  $ sudo hdparm -t --direct /dev/sda1

  /dev/sda1:
  Timing O_DIRECT disk reads:   2 MB in 31.18 seconds =  65.69 kB/sec
  ```

* In this case, the performance is really bad: 65 kB/sec is so 1990's.
  If the measured speed is more than 50 MB/s, you can skip the rest of this section and go directly to formatting the external drive.

  Otherwise we need to configure the USB driver to ignore the UAS interface of your drive.
  This configuration must be passed to the Linux kernel on boot:

  * Open the `cmdline.txt` file of the bootloader.

    ```sh
    $ sudo nano /boot/cmdline.txt
    ```

  * At the start of the line of parameters, add the text `usb-storage.quirks=aaaa:bbbb:u` where `aaaa` is the "idVendor" and `bbbb` is the "idProduct" value.
    Make sure that there is a single space character (` `) between our addition and the next parameter.
    Save and exit.

    ```
    usb-storage.quirks=152d:0578:u ..............
    ```

  * Reboot the Raspberry Pi with the external drive still attached.

    ```sh
    $ sudo reboot
    ```

  * After you logged in as "admin" again, let's test the read performance once more.

    ```sh
    $ sudo hdparm -t --direct /dev/sda1

    /dev/sda1:
    Timing O_DIRECT disk reads: 510 MB in  3.01 seconds = 169.59 MB/sec
    ```

  * You should see a significant increase in performance.
  If the test still shows a very slow read speed, your drive or USB adapter might not be compatible with the Raspberry Pi.
  In that case I recommend visiting the Raspberry Pi [Troubleshooting forum](https://www.raspberrypi.org/forums/viewforum.php?f=28){:target="_blank"} or simply try out hardware alternatives.

<script id="asciicast-NiOhoAsu2g9kltfHXzfU6GLnq" src="https://asciinema.org/a/NiOhoAsu2g9kltfHXzfU6GLnq.js" async></script>

### Format external drive and mount

* Format the partition on the external drive with Ext4 (use `[NAME]` from above, e.g `sda1`)

  ```sh
  $ sudo mkfs.ext4 /dev/[NAME]
  ```

* List the devices once more and copy the `UUID` into a text editor on your local computer.

  ```sh
  $ lsblk -o NAME,MOUNTPOINT,UUID,FSTYPE,SIZE,LABEL,MODEL
  ```

* Edit the `fstab` file and add the following as a new line at the end, replacing `123456` with your own `UUID`.

  ```sh
  $ sudo nano /etc/fstab
  ```

  ```
  UUID=123456 /mnt/ext ext4 rw,nosuid,dev,noexec,noatime,nodiratime,auto,nouser,async,nofail 0 2
  ```

  🔍 *more: [complete fstab guide](http://www.linuxstall.com/fstab){:target="_blank"}*

* Create the directory to add the hard disk and set the correct owner

  ```sh
  $ sudo mkdir /mnt/ext
  ```

* Mount all drives and check the file system. Is “/mnt/ext” listed?

  ```sh
  $ sudo mount -a
  $ df -h /mnt/ext
  > Filesystem      Size  Used Avail Use% Mounted on
  > /dev/sda1       440G   73M  417G   1% /mnt/ext
  ```

### Create bitcoin directory

* Set the owner

  ```sh
  $ sudo chown -R bitcoin:bitcoin /mnt/ext/
  ```

* Switch to user "bitcoin", navigate to the external drive and create the bitcoin directory.

  ```sh
  $ sudo su bitcoin
  $ cd /mnt/ext
  $ mkdir bitcoin
  $ ls -la
  > total 28
  > drwxr-xr-x 4 bitcoin bitcoin  4096 Dec 12 17:43 .
  > drwxr-xr-x 4 root    root     4096 Dec 12 17:38 ..
  > drwxr-xr-x 2 bitcoin bitcoin  4096 Dec 12 17:43 bitcoin
  > drwx------ 2 bitcoin bitcoin 16384 Dec 12 17:30 lost+found
  ```

* Create a testfile in the new directory and delete it.

  ```sh
  $ touch bitcoin/test.file
  $ rm bitcoin/test.file
  ```

  If this command gives you an error, chances are that your external hard disk is mounted as “read only”.
  This must be fixed before proceeding.
  If you cannot fix it, consider reformatting the external drive.

* Exit the "bitcoin" user session

  ```sh
  $ exit
  ```

<script id="asciicast-c75NG00m72iaguULOzzVc9Z3v" src="https://asciinema.org/a/c75NG00m72iaguULOzzVc9Z3v.js" async></script>

🔍 *more: [external storage configuration](https://www.raspberrypi.org/documentation/configuration/external-storage.md){:target="_blank"}*

---

## Move swap file

The usage of a swap file can degrade your SD card very quickly.
Therefore, we will move it to the external drive.

Use dd to create a swap file the size of your choosing. For example, creating a 4096 MiB swap file:
```sh
$ dd if=/dev/zero of=/mnt/ext/swapfile bs=1M count=4096 status=progress
```
Set the right permissions (a world-readable swap file is a huge local vulnerability):
```sh
$ chmod 600 /mnt/ext/swapfile
```
After creating the correctly sized file, format it to swap:
```sh
$ mkswap /mnt/extswapfile
```
Activate the swap file:
```sh
$ swapon /mnt/ext/swapfile
```
Finally, edit the fstab configuration to add an entry for the swap file:
```sh
$ sudo nano /etc/fstab
```
And add the entry:

```sh
/mnt/ext/swapfile none swap defaults 0 0
```

Then disable zswap-arm.service with sudo systemctl disable zswap-arm.service  and restart

```sh
  $ sudo reboot now
  ```
After reboot let's check with the command swapon:

```sh
  $ swapon
  ```
And you should see this: 

```sh
NAME              TYPE SIZE   USED PRIO
/mnt/ext/swapfile file   4G 422.5M   -2

<script id="asciicast-p7I8GeTfxOk15dFWHu8FVV83q" src="https://asciinema.org/a/p7I8GeTfxOk15dFWHu8FVV83q.js" async></script>
```
---

Next: [Security >>](raspibolt_21_security.md)
