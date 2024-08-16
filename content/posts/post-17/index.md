---
title: "Building and installing Kali NetHunter for Xiaomi Mi A2 on VoidLinux OS"
description: "Kali NetHunter project has a lot of precompiled kernels and chroots/rootfs (alltogether ROM) for diferent devices but Xiaomi Mi A2 codename 'jasmine_sprout' is missing. It is a way to build a kernel and port Kali NetHunter on the device"
DID: ""
date: 2024-03-19T22:28:43+01:00
publishdate: 2024-03-19 22:28
lastmod: 2024-03-19 22:28
tags:
  - general
  - android
  - kernel
  - build
  - ROM
  - OS
  - Linux
  - VoidLinux
  - gcc
draft: true
---

Kali NetHunter (KNH) project here


https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project

To have (to install) NetHunter on Android phone the later has to be deblocked and rooted.

Here we installing KNH on Xiaomi Mi A2 on which /e/OS was [installed](https://doc.e.foundation/devices/jasmine_sprout/install) (dev build based on [Android R(11)](https://images.ecloud.global/dev/jasmine_sprout/)).

Then the /e/OS was rooted by Magisk

Kali Documentation on building KNH

https://www.kali.org/docs/nethunter/building-nethunter/

---

to build KNH first thing first to find and download source code of the kernel of the device

Kali docs recommends search throught XDA forums. GitHub and similar also would be OK.

On github there are several sources

[kernel_xiaomi_sdm660](https://github.com/herobuxx/kernel_xiaomi_sdm660) -> somehow mentioned on XDA 

[ kernel_xiaomi_jasmine](https://github.com/AlpacaGang/kernel_xiaomi_jasmine) /descriptio: kernel source for AOSP-based ROMs/

[LineageOS /android_kernel_xiaomi_sdm660](https://github.com/LineageOS/android_kernel_xiaomi_sdm660)

Building a kernel requires utilities (gcc, clang (maybe), make etc) and toolchaing specific for the Android kernel's building.

We will start with cloning [*Kali NetHunter Installer*](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/-/tree/master/nethunter-installer) which is installer that helps to generate `Image` as zip file to be used to install on Android (quazi ROM).

```bash
git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project.git
cd nethunter-installer
```

### Preparing the environment

Before building anything, you will need to initialize the [devices repository](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-devices) literarly starting aa script `bootstrap.sh`.

```bash
./bootstrap.sh
# when asked "Would you like to grab the full history of devices? " answer N(o)
```

### Building Kali NetHunter Kernel

#### Finding kernel source for the device and compile it

##### kernel source 

Kernel source could be found on XDA forums, github, gitlab and other git repos, elsewhere.

When a source is found, it has to be saved somewhere, it could be a folder of KNH's folder, specific folder for the devices etc. In our case we've made device's folder on `/opt` directory with a name of device and inside made a folder `kernels`. 

It is good practice to check installed kernel's version on the Android devices, which could be done with `adb`

```
adb shell cat /proc/version
```

And then download the kernel with the same version (e.g., 4) and patchlevel (e.g., 4), like 4.4.Y (the latest Y is a sublevel and possibli could be any).

To find out the kernel's version and patchlevel in the source code we will open `Makefile` at the root folder of kernel's source code and check there variables 'VERSION' and 'PATCHLEVEL'.

In our case installed Android (as mentioned above) goes with kernel `4.4.302-lineageos-g2e69f2ae550d` and the kernel `LineageOS /android_kernel_xiaomi_sdm660` mentioned above was cloned to the folder `kernels`.

```
git clone  https://github.com/LineageOS/android_kernel_xiaomi_sdm660.git
```

##### Compiling kernel

Usually compiling Android kernel includes the following steps

- downloading kernel
- installing building & compiling utilities on host machine (like gcc, make, clang etc) according to Google's build instructions 
- installing toolchain specific to the kernel chosen (usually there are toolchains of Google)
- setting variables, configuring environment
- building and compiling the kernel

On KNH docs website there are instructions on how to [build kernel for supported devices as](https://www.kali.org/docs/nethunter/building-nethunter/) well as how to port NetHunter onto other devices [manually](https://www.kali.org/docs/nethunter/porting-nethunter/) or by [means of KNH's building scripts](https://www.kali.org/docs/nethunter/porting-nethunter-kernel-builder/).

For novice kernel builder the process is not stightforward, also it is a case with building for KNH, of cource :)

Additional instructions of building kernels are provided on the gitlab resources of KNH, in particular [this 'Building a kernel for your device'](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-devices#building-a-kernel-for-your-device) and [this 'Kali NetHunter Kernel Builder'](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernel/-/tree/master?ref_type=heads#kali-nethunter-kernel-builder)

An example of building Android kernel with KNH scripts could be found in [this Github repo](https://github.com/5h4d0wb0y/android_kernel_huawei_vienna).


So, previously we've cloned kernel's source for Xiaomi Mi A2 /jasmine_sprout/ device. 

Now we try to build NetHunter Kernel by using KNH kernel builder (automatic) [touch](https://www.kali.org/docs/nethunter/porting-nethunter-kernel-builder)

```bash
cd android_kernel_xiaomi_sdm660
git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernel
cd kali-nethunter-kernel
./build.sh
```

With the command `./build.sh` building options in window in terminal will pop up.

NOTE:

Since `build.sh` script is designed to use in Debian or SUSE -like OS it is important to install dependancies in advace before running the script and also disable function "get_dependencies" in the function "setup_env".

Under the script the following Debian's debendancies are required:

```
axel bc binutils-aarch64-linux-gnu build-essential ccache curl device-tree-compiler pandoc libncurses5-dev lynx lz4 fakeroot xz-utils whiptail
```

Installing required dependancies on VoidLinux (with respective change of packages names if any)

```bash
sudo xbps-install -Su axel bc cross-aarch64-linux-gnu base-devel ccache curl dtc pandoc ncurses-devel lynx lz4 fakeroot xz newt
```

Firstly, we select "S. Setup Environment and download toolchains." in "OTHERS" tab, wait until it downloads everything and set up folders, environment.

Next, build your test kernel with 2. Configure & compile kernel from scratch.

At some point it will ask

[ WARNING  ] nethunter_defconfig not found, creating.
[   INFO   ] Please select the configuration you would like to use as basis

Select  `sdm660_defconfig`

During the build error

```
/usr/bin/ld: scripts/dtc/dtc-parser.tab.o:(.bss+0x10): multiple definition of `yylloc'; scripts/dtc/dtc-lexer.lex.o:(.bss+0x0): first defined here
collect2: error: ld returned 1 exit status
```

[here](https://github.com/BPI-SINOVOIP/BPI-M4-bsp/issues/4) is a little description and solutions, like

```
# return to the kernel's source root
cd ..
#edit file `scripts/dtc/dtc-lexer-lex.c_shipped`
nvim scripts/dtc/dtc-lexer-lex.c_shipped
# Find the line 'YYLTYPE yylloc' and change it to 'extern YYLTYPE yylloc'
```

---

{{< discuss >}}
