# VerifiedBoot
>Verified Boot of FIT image built using BuildRoot

## Getting Started
<p align="justify">
This is a fork of pratapms's Verified Boot (https://github.com/pratapms/VerifiedBoot). This repo has been modified so that it can be applied to a Udoo Neo Basic which is powered by an NXP i.MX 6SoloX.
</p>

| Components | Description |
| ---         |     ---      |
| U-boot | Primary Root of Trust |
| zImage | Non Secured World Image |
| ITB | Combination and signed copy of config, kernel,rootfs |
| RootFS | Non Secured Image |

## Description

<p align="justify">Verified boot can be achieved by digitally signing the u-boot with a public RSA followed by signing the Flattened Image Tree, herein referred as FIT which simply consist of multiple images glued together as single binary blob for example Linux kernel, RootFs, board-device-tree-blob.dtb and so on). This FIT is then signed with a private key which can only be authticated by the u-boot that is signed with the public key in former process. If for any reasons the verification failed during boot, the process is halted and the risk of loading un-secured kernel image will be averted. The following flow diagram depicts verified boot process with the help of signed FIT image. We will be following this process to acheive verified boot in this little project.</p>

![Verified Boot](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot.png)

## Prerequisites

It is recommended to start with the following pre-requisite.

| S.No | Pre-Requisite |
| ---         |     ---      |
| 1.   | A decent x86_64 machine money can buy     |
| 2.   | Ubuntu 18.04 LTS running on the above       |
| 3.   | Internet connection     |
| 4.   | Bleeding Edge of Build-Root right from the [build-root github](https://github.com/buildroot/buildroot.git)
| 5.   | Install arm tool chain using <br /> ``` sudo apt-get install gcc-arm-linux-gnueabihf ``` |
| 6.   | One Brain and Two Hands       |

## Build Instruction

### Step 1: Clone the latest Buidroot from github

``` git clone https://github.com/buildroot/buildroot.git ```

> The i.MX 6SoloX platform based Udoo Neo Basic board was been selected to demonstrate the build procedure

### Step 2: Copy build files
```The following files will need to be copied into <buildroot>/board/udoo/common/ directory```

| S.No | Item to copy          | Source         |
| ---  | ---                   | ---            |
| 1.   | post-build.sh         | common.tar.gz  |
| 2.   | smithdigital.its      | common.tar.gz  |
| 3.   | boot.src.txt          | common.tar.gz  |

       Note: post-build.sh has been modified by prepending a slightly modified version of pratapms's script, smith-digital-sign.sh.
       
### Step 3: Configure
Configure the Build root system by issuing the following command from the root directory

``` make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- mx6sx_udoo_neo_defconfig ```

### Step 4: Build
With the below command and provided scripts we should be able to glide through the compilation process

``` make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- ```

After buildroot completes its build, it runs post-build.sh script on the outputs and does the following:
* Creates a private and public keys using RSA-2048 and OpenSSL
* Signs the kernel and device tree binary and creates a FIT image based on the image tree source file
* Appends the signed device tree binary to the u-boot binary
       
### Step 5: Output
<p align="justify">
Output of the compilation process if found at output directory out of the above mentioned command will have all of the host signing utilities and target booting images
</p>

``` ls <buildroot>/output/ ```

| S.No | Folder | Description |
| --- | --- | --- |
| 1. | build | All of the software packages downloaded as part of the build process |
| 2. | host | The executable files obtained from the above build command |
| 3. | images | All of the dtb, rootfilesystem (rootfs.ext2) , uboot.bin , zImage , public & private keys, ITS and ITB
| 4. | target | Expanded format of the RootFile system (rootfs.ext2), which can be overlayed for extendabiity creating a folder with custom configuration and provide the path to buildroot through BR2_ROOTFS_OVERLAY |
 
### Step 6: Flashing
<p align="justify">
Flash the uboot image onto the  boot partition of the Flash, and the itb file to a predefined offset, the bootm command will redirect the execution of the uboot to the mentioned ITB flashed
</p>
           
<p align="justify">
The rootFS category is not mentioned with the image source file (*.its) Please refer to the board configuration file:

``` <u-boot>/include/configs/udoo_neo.h ```

specially to the CONFIG_EXTRA_ENV_SETTINGS macro to  provide customized option to create the environment before starting the kernel.
</p>

## Running on QEMU
<p align="justify">
As the above mentioned steps specifically targets i.MX 6 ARM-Cortex A9 variant and the target device was unavailable during the time of my validation, I had chosen to demonstrate the entire procedure over the emulator to get the feel of the above steps and to have a higher level of confidence that this can be brought up on silicon faster.
</p>

### Step 1: Get the latest u-boot and build it

``` git clone git://git.denx.de/u-boot.git ```

``` make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- vexpress_ca9x4_defconfig ```

``` make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- ```

### Step 2: Get the kernel

``` git clone https://github.com/torvalds/linux ```

    Note: U-Boot and linux kernel souce is checked-in this repository under VerifiedBootDemo.tar.gz file.
       
 ### Step 3: Run on QEMU
 
 ``` qemu-system-arm -machine vexpress-a9  -m 1G -display none -serial stdio -kernel <path/to/u-boot-wdtb> ```

     Note:
     1) The automate.sh script will take care of the appending the public key to the u-boot exactly as stated 
        within the block diagram and also create the ITB file from the specified ITS within the folder
     2) u-boot-wtb is the resultant image with public key append, which transforms it name to "Root Of Trust",
        even the ITB file is privately signed (Refer to the automate.sh) for a simple self explanatory steps of
        getting this possible.

## Deployment
![Verified Boot Demo tftp](https://github.com/tpbw4321/VerifiedBoot/blob/master/qemu-fit-1.png)
![Verified Boot Demo iminfo](https://github.com/tpbw4321/VerifiedBoot/blob/master/qemu-fit-2.png)
![Verified Boot Demo bootm](https://github.com/tpbw4321/VerifiedBoot/blob/master/qemu-fit-3.png)

## Authors
* **Barron Wong** - [tpbw4321 GitHub](https://github.com/tpbw4321)
* **Pratap Maddimsetty** - [PratapMs GitHub](https://github.com/pratpms)

## License

This project is distributed under the [GPLV3 License](https://opensource.org/licenses/GPL-3.0)

## Acknowledgments

* Open Source Community
* https://github.com/pratapms/VerifiedBoot
* https://elinux.org/images/f/f8/Verified_Boot.pdf
* https://github.com/u-boot/u-boot/tree/master/doc/uImage.FIT
* http://www.informit.com/articles/article.aspx?p=1647051&seqNum=5
* https://github.com/maximeh/buildroot/blob/master/docs/manual/customize-rootfs.txt


