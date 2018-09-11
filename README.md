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
| 4.   | One Brain and Two Hands       |

```
- A decent x86_64 machine money can buy
- Ubuntu 16.04 LTS running on the above
- Internet connection
- One Brain and Two Hands
```

### nstalling

A step by step series of examples that tell you how to get a development env running

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

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

