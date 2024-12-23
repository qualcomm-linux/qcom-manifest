# QCOM Repo Manifest README

This git repository is used to download manifests for QCOM Linux Yocto BSP releases.

The branch will be based on the release type Linux with release manifests in each branch tied to the base releases.

For QCOM Linux Yocto BSP releases the manifest branches will be named as qcom-linux-[Yocto-Project-release],
so qcom-linux-kirkstone with all manifests tied to releases on Kirkstone in this branch.

Below procedure assumes that you have gone through [Qualcomm Linux | Qualcomm](https://www.qualcomm.com/developer/software/qualcomm-linux)

If you are already having standard yocto environment, skip below prepare Host setup steps


# Host Setup

The host machine needs a few setup operations to ensure the required software tools are ready

## Install below packages to prepare your host environment for Yocto build

**For ubuntu 22.04**
```bash
sudo apt update
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential \
    chrpath socat cpio python3 python3-pip python3-pexpect xz-utils \
    debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
    libsdl1.2-dev pylint xterm python3-subunit mesa-common-dev zstd \
    liblz4-tool locales tar python-is-python3 file libxml-opml-simplegen-perl \
    vim whiptail
```

## Install the `repo` utility

To use this manifest repo, the repo tool must be installed first.
To install repo, run these commands:

```bash
mkdir -p ~/bin
cd ~/bin
#Note if you already have a previous directory of repo_tool, you can delete it
rm -rf ~/bin/repo_tool
git clone https://android.googlesource.com/tools/repo.git -b v2.41 repo_tool
cd repo_tool
git checkout -b v2.41
export PATH=~/bin/repo_tool:$PATH
```

If your region is blocking access to android.googlesource, try the following configuration to fetch
repo from Codelinaro mirror
```bash
git config --global url.https://git.codelinaro.org/clo/la/tools/repo.insteadOf https://android.googlesource.com/tools/repo
```

If the above method did not work you can try below commands for repo installation

Install curl if you haven't installed already
```bash
sudo apt install curl
```

**Note:** latest repo version works with python3
```bash
mkdir -p ~/bin
curl https://raw.githubusercontent.com/GerritCodeReview/git-repo/v2.41/repo -o ~/bin/repo && chmod +x ~/bin/repo
export PATH=~/bin:$PATH
```

## Set up locales if you haven't setup already, by using the following commands

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

## Update git configurations

**Check if your identity is configured in .gitconfig**
```bash
git config --get user.email
git config --get user.name
```

**Run the following commands if you do not have your account identity set in .gitconfig**
```bash
git config --global user.email you@example.com
git config --global user.name "Your Name"
```

**Add below UI color option for output of console**
```bash
git config --global color.ui auto
```

## Download the Yocto Project BSP

```bash
mkdir [release]
cd [release]
repo init -u https://github.com/qualcomm-linux/qcom-manifest -b [branch name] -m [release manifest]
repo sync
```

Each branch will have detailed READMEs describing exact syntax.

**Example:**

To download the `qcom-6.6.28-QLI.1.1-Ver.1.1` release

```bash
repo init -u https://github.com/qualcomm-linux/qcom-manifest -b qcom-linux-kirkstone -m qcom-6.6.28-QLI.1.1-Ver.1.1.xml
repo sync
```

## Setup the build folder for a BSP release

MACHINE=[machine] DISTRO=qcom-[backend] source setup-environment

[machine]   Target hardware

[backend]   Graphics backend type
- qcom-wayland     meta-qcom-distro

Example for setup Wayland, machine qcs6490-rb3gen2-vision-kit:

```bash
MACHINE=qcs6490-rb3gen2-vision-kit DISTRO=qcom-wayland source setup-environment
```
**Note:** [Source to initialize bitbake environment](https://github.com/quic-yocto/meta-qcom-distro/blob/kirkstone/set_bb_env.sh)

## Build an image

bitbake [image recipe]

**Some image recipes:**

Image Name           	    	| Description
---------------------	    	|---------------------------------------------------
qcom-minimal-image          	| A minimal rootfs image that boots to shell.
qcom-console-image          	| Boot to shell with Package group to bring in all basic packages.
qcom-multimedia-image       	| Image recipe includes recipes for multimedia software components, such as, audio, bluetooth, camera, computer vision, display and video.
qcom-multimedia-test-image  	| This image recipe includes tests

**Example command:**

```bash
bitbake qcom-multimedia-image
```

## To Include add-on layers, refer below README.md files

Mandatory layers:
1. [Qualcomm Hardware Enablement](https://github.com/quic-yocto/meta-qcom-hwe/blob/kirkstone/README.md)

Optional layers:
1. [Qualcomm Intelligent Multimedia Product SDK](https://github.com/quic-yocto/meta-qcom-qim-product-sdk/blob/kirkstone/README.md)
2. [Realtime Linux](https://github.com/quic-yocto/meta-qcom-realtime/blob/kirkstone/README.md)
3. [Qualcomm Intelligent Robotics Product SDK](https://github.com/quic-yocto/meta-qcom-robotics-sdk/blob/kirkstone/README.md)

## Flash the image

To flash the generated build, see the [Flash software](https://docs.qualcomm.com/bundle/publicresource/topics/80-70015-254/flash_images.html)


## References

If you are new to the Yocto project, you may try your first build as documented in the Yocto project at [Standard Yocto environment](https://docs.yoctoproject.org/4.0.22/brief-yoctoprojectqs/index.html)

The complete index of Yocto project docs is available at [Yocto project docs](https://docs.yoctoproject.org/4.0.22/singleindex.html#welcome-to-the-yocto-project-documentation)
