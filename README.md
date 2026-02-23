# Qualcomm Linux Repo Manifests

This git repository is used to download manifests for Qualcomm Linux Yocto BSP releases. This documentation assumes that you have gone through [Qualcomm Linux | Qualcomm](https://www.qualcomm.com/developer/software/qualcomm-linux)

If you have a yocto environment already setup, you can skip the section for Host Setup. 

## Host Setup

The host machine needs a few setup operations to ensure the required software tools are ready

### Required Packages for the Build Host 

**For Ubuntu 22.04**
```bash
sudo apt update
sudo apt install build-essential chrpath cpio debianutils diffstat file gawk \
    gcc git iputils-ping libacl1 locales python3 python3-git python3-jinja2 \
    python3-pexpect python3-pip python3-subunit socat texinfo unzip \
    wget xz-utils zstd

# the repo manifest workflow depends on kas to parse the configuration files 
sudo apt install python3-yaml pipx

# This command should add the kas binary location to your PATH.
# Restart your shell session after running this command for the changes to take effect.
pipx ensurepath
 
# The kas version is expected to be 4.8 or higher
pipx install kas
```

### Install the `repo` utility

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

### Set up locales if you haven't setup already, by using the following commands

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

### Update Git configuration

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

## Build Guide

### Download the Yocto meta layers 

```bash
mkdir [release]
cd [release]
repo init -u https://github.com/qualcomm-linux/qcom-manifest -b [branch name] -m [release manifest]
repo sync
```

**Example:**

To download the `qli.2.0-rc1` release

```bash
repo init -u https://github.com/qualcomm-linux/qcom-manifest -b main -m qli.2.0-rc1.xml
repo sync
```

### Setup the build folder for a BSP release

```bash
source setup-environment --machine path/to/kas-machine-config.yml --distro path/to/kas-distro-config.yml --kernel meta-qcom/ci/kas-kernel-config.yml
```

**Example:**

```bash
source setup-environment --machine meta-qcom/ci/qcs9100-ride-sx.yml --distro meta-qcom/ci/qcom-distro-prop-image.yml --kernel meta-qcom/ci/linux-qcom-6.18.yml
```

**Note:** Also, see the [script](https://github.com/qualcomm-linux/meta-qcom-releases/blob/main/setup-environment) used to the initialize bitbake environment.

### Build an image

```bash
bitbake [image recipe]
```

**Image recipes:**

Image Name           	    	    | Description
---------------------	    	    |---------------------------------------------------
qcom-minimal-image          	    | A minimal rootfs image that boots to shell.
qcom-console-image          	    | Boot to shell with Package group to bring in all basic packages.
qcom-multimedia-image       	    | Image recipe with upstream multimedia software components.
qcom-multimedia-proprietary-image  	| Image recipe with proprietary multimedia software components.

**Example command:**

```bash
bitbake qcom-multimedia-proprietary-image
```

## Flash the image

To flash the generated build, see the [Flash software](https://docs.qualcomm.com/bundle/publicresource/topics/80-70017-254/flash_images.html?vproduct=1601111740013072&latest=true)

## References

If you are new to the Yocto project, you may try your first build as documented in the Yocto project at [Standard Yocto environment](https://docs.yoctoproject.org/dev/brief-yoctoprojectqs/index.html)

The complete index of the Yocto project docs can be found [here](https://docs.yoctoproject.org/dev/singleindex.html#welcome-to-the-yocto-project-documentation)
