# VerifiedBoot
>Verified Boot of FIT image built using BuildRoot

## Getting Started
<p align="justify">
The Build-root platform has evolved over the years and finally with the support to build the kernel, u-boot along with the  capabilities to build FIT image, made life easier for embedded systems. The developer just have to get the configuration straight and trigger the build for the Build-root to compile and deliver the aforementioned boot essentials. Following table lists the critical components to boot embedded systems and their role in verified boot
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
| 2.   | Ubuntu 16.04 LTS running on the above       |
| 3.   | Internet connection     |
| 4.   | Bleeding Edge of Build-Root right from the [build-root github](https://github.com/buildroot/buildroot.git)
| 5.   | Install arm tool chain using <br /> ``` sudo apt-get install gcc-arm-linux-gnueabihf ``` |
| 6.   | One Brain and Two Hands       |

## Build Instruction

### Step 1: Clone the latest Buidroot from github

``` git clone https://github.com/buildroot/buildroot.git ```

> The IMX6 platform based nitrogen6sx board was been selected to demonstrate the build procedure

### Step 2: Copy build files
Copy the following list of files from this repository to the respective buildroot folders mantioned in Target column

| S.No | Item to copy          | Target         |
| ---  | ---                   | ---            |
| 1.   | Patches               | Common.tar.gz  |
| 2.   | smith-digital-sign.sh | Common.tar.gz  |
| 3.   | smithdigital.its      | Common.tar.gz  |
| 4.   | nitrogen6sx_defconfig | Common.tar.gz  |
       
### Step 3: Configure
Configure the Build root system by issuing the following command from the root directory

``` make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- nitrogen6sx_defconfig ```

<p align="justify">All of the above files are provided  part of the common.tar.gz file. The Git hub provides the modified nitrogen6sx_defconfig file to take care of all the relative changes to generate images for verified Boot. TrustZone specific Hardware & Software configurations are deferred for this secured boot demonstration due to lack of hardware capabilities.
</p>

### Step 4: Build
With the below command and provided patches we should be able to glide through the compilation process

``` make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- ```

<p align="justify">
The common.tar.gz files should go into the "board/boundarydevices" of the original buildroot, this particular folder   
has all the necessary patches, board specific files and post script.
</p>
       
### Step 5: Output
<p align="justify">
Output of the compilation process if found at output directory out of the above mentioned command will have all of the host signing utilities and target booting images
</p>

``` ls ${PWD}/output ```

| S.No | Folder | Description |
| --- | --- | --- |
| 1. | build | All of the software packages downloaded as part of the build process |
| 2. | host | The executable files obtained from the above build command |
| 3. | images | All of the dtb, rootfilesystem (rootfs.ext2) , uboot.bin , zImage , public & private keys, ITS and ITB
| 4. | target | Expanded format of the RootFile system (rootfs.ext2), which can be overlayed for extendabiity creating a folder with custom configuration and provide the path to buildroot through BR2_ROOTFS_OVERLAY |
 
### Step 6: Flashing
<p align="justify">
Flash the uboot image onto the  boot partition of the Flash, and the itb file to a predefined offset, the bootm command will redirect the execution of the uboot to the mentioed ITB flashed
</p>
           
<p align="justify">
The rootFS category is not mentioned with the image source file (*.its) Please refer to the board configuration file:

``` <u-boot>/include/configs/nitrogen6sx.h ```

specially to the CONFIG_EXTRA_ENV_SETTINGS macro to  provide customized option to create the environment before starting the kernel.
</p>

## Running on QEMU
<p align="justify">
As the above mentioned steps specifically targets i.MX 6 ARM-Cortex A9 variant and the target device was unavailable during the time of my validation, I had chosen to demonstrate the entire procedure over the emulator to get the feel of the above steps and to have a higher level of confidence that this can be brought up on silicon faster.
</p>

### Step 1: Get the latest u-boot and build it

``` git clone git://git.denx.de/u-boot.git ```

``` make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- vexpress_ca9x4_defconfig ```

``` make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- ```
     
    Note: Please download the arm-non-linux-gnueabi compiler, we don't need any hard float capable compiler 
          as we just want to emulate the procedure.

### Step 2: Get the kernel

``` git clone https://github.com/torvalds/linux ```

    Note: U-Boot and linux kernel souce is checked-in this repository under VerifiedBootDemo.tar.gz file.
       
 ### Step 3: Run on QEMU
 
 ``` qemu-system-arm -M vexpress-a9 -m 512M -serial stdio -net nic -net tap,ifname=tap0 -kernel <path/to/u-boot-wtdb> ```

     Note:
     1) The automate.sh script will take care of the appending the public key to the u-boot exactly as stated 
        within the block diagram and also create the ITB file from the specified ITS within the folder
     2) u-boot-wtb is the resultant image with public key append, which transforms it name to "Root Of Trust",
        even the ITB file is privately signed (Refer to the automate.sh) for a simple self explanatory steps of
        getting this possible.

## Deployment

![Verified Boot Demo](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot-Demo1.png)
![Verified Boot Demo Booting](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot-Demo.png)

## Authors

* **Pratap Maddimsetty** - [PratapMs GitHub](https://github.com/pratpms)

## License

This project is distributed under the [GPLV3 License](https://opensource.org/licenses/GPL-3.0)

## Acknowledgments

* Open Source Community
* https://boundarydevices.com/high-assurance-boot-hab-dummies/
* https://elinux.org/images/f/f8/Verified_Boot.pdf
* https://lxr.missinglinkelectronics.com/uboot/doc/uImage.FIT/
* https://events.static.linuxfound.org/sites/events/files/slides/U-Boot%20FIT%20for%20Xen.pdf
* http://www.informit.com/articles/article.aspx?p=1647051&seqNum=5
* https://github.com/maximeh/buildroot/blob/master/docs/manual/customize-rootfs.txt


