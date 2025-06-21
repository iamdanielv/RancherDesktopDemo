# Rancher Desktop on Ubuntu - Demo

I was looking for a way to easily run a kubernetes cluster on Windows and came across [Rancher Desktop](https://rancherdesktop.io/).

This repo is a way for me to write down how I did it and hopefully help other people who may be interested in doing something similar.

## Pre-Req's

### This is tested on Ubuntu 25.04, but should also work on 24.04

[Ubuntu Desktop] (https://ubuntu.com/download/desktop)

Update your desktop install with:

```bash
sudo apt update && sudo apt upgrade -y
```

## Rancher Desktop Install

The Rancher team has a pretty comprehensive [install guide](https://docs.rancherdesktop.io/getting-started/installation/#linux) for installing Rancher Desktop on Linux. I will be following those instructions, you may want to refer to those instructions as you follow

### Check permissions

The first thing we need to do is check that our user has permission to use the kvm device. I modified the command provided in the isntructions to make it a little more readable:

```bash
[ -r /dev/kvm ] && [ -w /dev/kvm ] && echo -e "\n\e[32m✔ KVM OK\e[0m\n" || echo -e "\n\e[31m✗ problem with permissions\e[0m\n"
```

it should respond with:

```bash
✔ KVM OK
```

if not, you will need to change the permissions for your user. You can do this by running the following command in a terminal window as root (or sudo):

```bash
sudo usermod -a -G kvm "$USER"
```

To activate the new group permissions and make sure that all variables are set properly, you need to reboot your system with the command:

```bash
sudo reboot
```

### Install Rancher Desktop with .deb Package

In order to install Rancher Desktop, we need to add the GPG key and the repository to our sources list. I modified the instructions a little to make it more clear what each step is doing.

#### Install curl

The documentation asks us to use curl, but it may not be installed. To make sure curl is installed, run:

```bash
sudo apt install curl -y
```

#### Download and add the Rancher Desktop GPG key

```bash
if curl -fsSL https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/Release.key | gpg --dearmor | sudo tee /usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg > /dev/null; then
    echo -e "\e[32m✔ rancher GPG key added successfully\e[0m"
else
    echo -e "\e[31m✗ Failed to add rancher GPG key\e[0m" >&2
fi
```

#### Add the Rancher Desktop repository

```bash
if echo 'deb [signed-by=/usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg] https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/ ./' | sudo tee /etc/apt/sources.list.d/isv-rancher-stable.list > /dev/null; then
    echo -e "\e[32m✔ Repository added successfully\e[0m"
else
    echo -e "\e[31m✗ Failed to add repository\e[0m" >&2
fi
```

#### Update package lists

```bash
sudo apt update && echo -e "\e[32m✔ apt update successful\e[0m" || { echo -e "\e[31m✗ apt update failed\e[0m" >&2;}
```

#### Install Rancher Desktop

```bash
sudo apt install -y rancher-desktop && echo -e "\e[32m✔ Rancher Desktop installed successfully\e[0m" || { echo -e "\e[31m✗ Rancher Desktop installation failed\e[0m" >&2;}
```
