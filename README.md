# chromeos-build
Scripts to build Chrome OS easily

## How to run Chromium OS on KVM

Retrieve one of KVM images.  I recommend [chromiumos_qemu_image-R60-9592.63-amd64-generic.32GB.qcow2.xz](https://github.com/jam7/chromeos-build/releases/download/R60/chromiumos_qemu_image-R60-9592.63-amd64-generic.32GB.qcow2.xz), 410MB.

Then, create VM using the image.  [Here](https://www.chromium.org/chromium-os/how-tos-and-troubleshooting/running-chromeos-image-under-virtual-machines) has command line for KVM.  If you use GUI manager, plase make sure to use **cirrius** VGA and **SATA** or **IDE** storage device.

You cannot log in from GUI until you register your own google API keys, but it is possible to ssh to Chromium OS with chronos:chronos account.

## How to compile Chromium OS by yourself

### Prerequisites
Prerequisites are described [here](https://www.chromium.org/chromium-os/developer-guide).

I'll list them for the ease.  **Bold** means new or different prerequisities from above.  I noticed them through my experience.

- ubuntu **16.04 works**
- x86_64 64 bit system
- an account with sudo access
- **16** GB RAM for arm and x86.  **20** GB RAM for amd64.  (For example, amd64 uses gold-linker, but it still uses 12G.  The whole system uses at least 18 GB.)
- git and curl
- configure git
- tweak sudoers for **400** minutes (My machine takes more than 180 min for compilation)

### Setup
Type following command to prepare depo_tools and sources.

```
$ make setup
```

### Compile
Type following command to create images for all architectures.  This takes a day or more for me.

```
$ make images
```

You can create images for architecures you need only by following command.

```
$ make arm | x86 | x64
```

In order to convert thses images into KVM images, type one of following commands.

```
$ make kvm
$ make armk | x86k | x64k
```

### How to convert raw image to qcow2 image

Convert qemu raw image to qcow2 image if your qemu-manager requires it.

```
$ qemu-img convert -f raw -O qcow2 chromiumos_qemu_image-R55-8872.76-amd64-generic.img chromiumos_qemu_image-R55-8872.76-amd64-generic.qcow2
```

## How to change chronos password

Push `CTRL`+`ALT`+`F2`.  Type `chronos` as a user.  You may need to enter `chronos` as password also.

```
$ sudo mount -o remount,rw /
$ sudo chromeos-setdevpasswd
Password:
Verifying - Password:
$ sudo passwd chronos
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

It is required to update UNIX password to log-in through ssh.

## How to set google API keys

You need to retrieve your own API keys from https://www.chromium.org/developers/how-tos/api-keys.  Then, you can set those API keys into Chromium OS like below.

```
$ sudo mount -o remount,rw /
$ sudo vi /etc/chrome_dev.conf
# append at the bottom below
GOOGLE_API_KEY=your_api_key
GOOGLE_DEFAULT_CLIENT_ID=your_client_id
GOOGLE_DEFAULT_CLIENT_SECRET=your_client_secret
```

You need to reboot Chromium OS once.

## How to disable sleep

```
$ sudo bash -c 'echo 1 >/var/lib/power_manager/disable_idle_suspend'
$ sudo chown power:power /var/lib/power_manager/disable_idle_suspend
$ sudo restart powerd
```

## Releases
You can download pre-compiled image through [release page](https://github.com/jam7/chromeos-build/releases)

## References
[a list of released chromebook](https://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices)  
[developer guide](https://www.chromium.org/chromium-os/developer-guide)  
[Running a Chromium OS image under KVM](https://www.chromium.org/chromium-os/how-tos-and-troubleshooting/running-chromeos-image-under-virtual-machines)  
[Power manager overrides](https://github.com/dnschneid/crouton/wiki/Power-manager-overrides)  
