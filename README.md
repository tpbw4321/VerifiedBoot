# VerifiedBoot
>Verified Boot of FIT image built using BuildRoot

## Getting Started

The Build-root platform has evolved over the years and finally with the support to build the kernel, u-boot along with the  capabilities to build FIT image made life easier for embedded systems. The developer just have to get the configuration straight and trigger the build for the Build-root to compile and deliver the aforementioned boot essentials 

| Components | Description |
| ---         |     ---      |
| U-boot | Primary Root of Trust |
| zImage | Non Secured World Image |
| ITB | Combination and signed copy of config, kernel,rootfs |
| RootFS | Non Secured Image |

## Secured Boot

For the i.mx platform the primary root of trust will be the hardware assisted High Assurance Boot (HAB), which serves the purpose of authenticating the boot process right from the moment processor comes out of reset. Following sequence diagram has been extracted from the NXP website to demonstrate secured boot.

![Verified Boot](https://github.com/pratapms/VerifiedBoot/blob/master/Secured-Boot.png)

## Verified Boot

The sequence of Verified boot follows the unsecured booting, without the use of trust zone. Even for verification and validation of the public and private hash keys, the boot process runs from unsecured execution environment. In the following diagram the section(s) marked green bubble represents un-secured items and the ones that are marked in red depicts secured components, which has been fused with privately signed hash. 

![Verified Boot](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot.png)

### Prerequisites

It is recommended to start with the following pre-requisite.

| S.No | Pre-Requisite |
| ---         |     ---      |
| 1.   | A decent x86_64 machine money can buy     |
| 2.   | Ubuntu 16.04 LTS running on the above       |
| 3.   | Internet connection     |
| 4.   | Bleeding Edge of Build-Root right from the [build-root github](https://github.com/buildroot/buildroot.git)
| 5.   | Install arm tool chain using <br /> ``` sudo apt-get install gcc-arm-linux-gnueabihf ``` |
| 6.   | One Brain and Two Hands       |

### Installing

A step by step series of examples that tell you how to get a development env running

Step1: Clone the latest Buidroot, i.e. master copy of the software
       https://github.com/buildroot/buildroot.git
Step2:
       Copy the following items provided from the repository to the respective buildroot folders
       
       | S.No | Item to copy          | Target        |
       | ---- |-----------------------|---------------|
       | 1.   | Patches               | Board Folder  |
       | 2.   | smith-digital-sign.sh | Board Folder  |
       | 3.   | smithdigital.its      | Board Folder  |
       | 4.   | nitrogen6sx_defconfig | Board Folder  |
       
 Step3:
       Need to configure the Build root system, following is the simple command
       make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- nitrogen6sx_defconfig
       
       Note: 
       The Git hub provides the modified nitrogen6sx_defconfig file to take care of all the relative changes with 
       respective to generate images for verified Boot
            
 Step4:
       With the below command and provided patches we should be able to glide through the compilation process
       make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
       
       Note:
       The common.tar.gz files should go into the "board/boundarydevices" of the original buildroot, this particular folder   
       has all the necessary patches, board specific files and post script
       
 Step5:
       The compiler out of the above mentioned command will have all of the host signing utilities and target booting images
       PATH: ls ${PWD}/output
       
        1) build: All of the software packages downloaded as part of the build process
        2) host : The executable files obtained from the above build command
        3) Images : All of the dtb, rootfilesystem (rootfs.ext2) , uboot.bin , zImage , public & private keys, ITS and ITB
        4) target : Expanded format of the RootFile system (rootfs.ext2), which can be overlayed for extendabiity 
 
 Step6:
        Flash the uboot image onto the  boot partition of the Flash, and the itb file to a predefined offset, the bootm  
           command will redirect the execution of the uboot to the mentioed ITB flashed
           
        Note:
         The rootFS category is not mentioned with the image source file (*.its)
         Please refer to the board configuration file: 
         path<u-boot>/include/configs/nitrogen6sx.h, specially to the CONFIG_EXTRA_ENV_SETTINGS macro to  provide     
         customized option to create the environment before starting the kernel

## Running the tests over QEMU

As the above mentioned steps specifically targets i.MX 6 ARM-Cortex A9 variant based on IM6 platform, as the target is unavailable, we will try to demonstrate the entire procedure over the emulator to get the feel of the above steps:

Step1) Clone the uboot master 
      git clone git://git.denx.de/u-boot.git
      
Step2)
       make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- vexpress_ca9x4_defconfig
       make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi-
       
       Note:
       Please download the arm-non-linux-gnueabi compiler, we don't need any hard float capable compiler as we just want to  
       emulate the procedure
       
Step3)
       git clone https://github.com/torvalds/linux
       git checkout -b stable v4.8
       
       Note: 
       Download the latest linux kernel, checkout a stable version
       
Step4)
       To save the above mentioned procedures,just download the folder VerifiedBootDemo.tar.gz to get all of the essentials:
       
       qemu-system-arm -M vexpress-a9 -m 512M -serial stdio -net nic -net tap,ifname=tap0 -kernel path to u-boot-wtdb

       Note:
       1) The automate.sh script will take care of the appending the public key to the u-boot exactly as stated within the   
          block diagram and also create the ITB file from the specified ITS within the folder
       2) u-boot-wtb is the resultant image with public key append, which transforms it name to "Root Of Trust", even the ITB   
          file is privately signed (Refer to the automate.sh) for a simple self explanatory steps of getting this possible.
       
Step5) Following are the test results as part of the above procedure

        ![Verified Boot Demo](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot-Demo1.png)
        ![Verified Boot Demo Booting](https://github.com/pratapms/VerifiedBoot/blob/master/Verified-Boot-Demo.png)


### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Authors

* **Pratap Maddimsetty** - [PratapMs GitHub](https://github.com/pratpms)

## License

This project is distributed under the [GPLV3 License](https://opensource.org/licenses/GPL-3.0)

## Acknowledgments

* Open Source Community

