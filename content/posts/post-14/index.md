---
title: "Getting background removal in OBS on VoidLinux (and any other GNU/Linux-based distro)"
description: "Plugin obs-backgroundremoval"
DID: "obs-backremove-voidlinux"
date: 2023-05-30T20:13:00+02:00
publishdate: 2023-05-30 20:13
lastmod: 2023-05-30 20:13
tags:
  - general
  - obs
  - build
  - instal
  - compile
  - plugins
  - voidlinux
draft: false
---

## TLDR;

Building, compiling and installing plugin `obs-backgroundremoval` of *OBS* studio to remove 'virtual' background without using 'real' green background. The procedure was made on VoidLinux with `cmake`, `make`, `pyenv`, `git`, but should work on any linux distro.

```bash
cd ~/Downloads
git clone https://github.com/royshil/obs-backgroundremoval
cd obs-backgroundremoval

pyenv install 3.9.1 
sudo xbps-install -S obs obs-devel cmake make

mkdir build

cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr -DLINUX_PORTABLE=OFF -S . -B build 
cmake --build build 

sudo cmake --install build --prefix /usr
```

## OBS

Free and open source software for video recording and live streaming.[bam](https://obsproject.com)

## OBS plugin to remove background: [obs-backgroundremoval](https://github.com/royshil/obs-backgroundremoval)

An OBS plugin for removing background in portrait images (video), making it easy to replace the background when screen recording. 

There are instructions and precompiled packages for some platrforms (Mac OS, Windows, Linux), but for some Linux-based platrforms there are neither instructions, nor precompeled packages. 

We can use general building, compiling and installing instructions to make this plugin available on the platrforms not listed. For this we'll use [`cmake`](https://cmake.org/cmake/help/latest/guide/tutorial/Installing%20and%20Testing.html) since the project is built on C++. Let's go.

```bash
# clone plugin
cd ~/Downloads
git clone https://github.com/royshil/obs-backgroundremoval
cd obs-backgroundremoval

# required libs/packages
# - python >= 3.9.1
# - OBS studio >=28
# - OBS dev library ('libobs' or 'obs-dev' or 'obs-devel' depending on distributive packages' naming)
# - cmake; 'cmake' generator has to be 'Unix Makefiles'
# - make

# for setting and using required python version we can use [`pyenv`](https://github.com/pyenv/pyenv)
pyenv install 3.9.1 #if you have a problem of finding correct (new) version of python, try to update 'pyenv' by pulling changes from the repo or using [pyenv's plugin](https://github.com/pyenv/pyenv-update)

# because we're building and compiling package on [VoidLinux](https://voidlinux.org/), the 'xbps' packages manager is used. for any other platforms the respective package manager should be used
sudo xbps-install obs obs-devel cmake make

#make a build folder to build 
mkdir build

#configure 'cmake' (thanks to these resources and answers it has been managed how to do: [1](https://stackoverflow.com/questions/67425557/how-do-i-build-a-cmake-project) [2](https://www.cyberciti.biz/faq/howto-compile-and-run-c-cplusplus-code-in-linux/) [3](https://www.poftut.com/cmake-tutorial-to-build-and-compile-in-linux/) [4](https://cmake.org/cmake/help/latest/guide/tutorial/Installing%20and%20Testing.html#getting-started)
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr -DLINUX_PORTABLE=OFF -S . -B build #configs options are also extraxted from a building script 'build-linux.zsh' located in the project's folder '.github/scripts/'
#build executables and libs
cmake --build build #the latter is a folder

#and finally install
sudo cmake --install build --prefix /usr
```

After installation 'background removal' will be available as a filter of capture source.

## Some info on the subject of background removal 

### Why are movie scenes shot with green background? ([hit it off](https://movies.stackexchange.com/questions/52597/why-are-movie-scenes-shot-with-green-background))

Green is currently used as a backdrop more than any other color because image sensors in digital video cameras are most sensitive to green, due to the bayer pattern allocating more pixels to the green channel, mimicking the human eye's increased sensitivity to green light. Therefore, the green camera channel contains the least "noise" and can produce the cleanest key/matte/mask. Additionally, less light is needed to illuminate green, again because of the higher sensitivity to green in image sensors. Bright green has also become favored since a blue background may match a subject's eye color or common items of clothing such as jeans.([link](https://movies.stackexchange.com/a/52600))

### Virtual background: amending and/or removal

[virtual background](https://www.pcmag.com/encyclopedia/term/virtual-background) - A simulated environment that appears behind a participant in a video conference. A virtual background can make it appear that people are at the beach, a famous landmark or even outer space.

#### [Popular background removal techniques](https://www.analyticsvidhya.com/blog/2021/07/learn-how-to-do-real-time-background-replacement-using-opencv-and-cvzone/)

Image clipping path – This technique is used if the subject of the image has sharp edges. All those elements that fall outside the path will be eliminated.

Image cut-out – Here we cut the required region or subject in a frame and remove the background.

Image masking – If the images have frills or fine edges we can use image masking techniques.

Erasing the background – Erasing the background of an image using any different tools

Many famous applications use a background removal technique and replace it with a custom one. 

#### [How Automatic Image Background Removal Works](https://www.withoutbg.com/resources/how-automatic-image-background-removal-works)

The technical term for image background removal is image matting or alpha matting, whereby the original background is removed from a still image or video using one of several techniques.

An image can be modeled as a combination of foreground F and background B using an alpha matte $\alpha$:

$$ \begin{equation}I_{i}=\alpha_{i} F_{i}+\left(1-\alpha_{i}\right) B_{i} \quad \alpha_{i} \in[0,1]\end{equation} $$

The alpha matte encodes the transparency of each pixel in an image, with white representing opaque pixels (foreground), black representing transparent pixels (background), and grayscale values representing intermediate transparency levels. When creating a composite image, the alpha matte guides the process of blending the foreground object with the new background, using the intermediate transparency levels to achieve a seamless and realistic composite image.

The alpha matte is a matrix of transparency values ranging from 0 to 1. A value of 0 represents complete transparency (background), and 1 represents complete opacity (foreground). The intermediate values between 0 and 1 correspond to levels of partial opacity, representing the pixels in the transition region.

##### Traditional Techniques VS Deep Learning

Background removal techniques have evolved over time, with traditional methods relying on handcrafted features and mathematical models to separate foreground and background pixels. While these methods can be effective, they may require significant human intervention, and their accuracy can be limited by their dependence on these predetermined features.

However, deep learning methods can also require a large amount of training data and computing power, and their results may be difficult to interpret or explain. Traditional methods may still be useful in certain situations or when human intervention is necessary.

{{< discuss >}}
