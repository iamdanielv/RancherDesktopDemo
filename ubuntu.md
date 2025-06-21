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
