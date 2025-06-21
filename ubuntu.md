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

If everything went smoothly, you should see something similar to:

```bash
✔ Rancher Desktop installed successfully
```

you can now start Rancher Desktop by running the following command in a terminal:

```bash
rancher-desktop
```

or the Rancher Desktop icon in the Apps view.

## Add pass

Rancher Desktop uses pass for credential storage, we need to generate a key and add it to our system. To do this run the following command:

```bash
gpg --generate-key
```

This will prompt you to enter some information about your key, such as your name, email address, and passphrase. Follow the prompts until you have completed the process.

Once the tool has completed generating your key, it should show something similar to:

```bash
public and secret key created and signed.

pub   ed25519 YYYY-MM-DD [SC] [expires: YYYY-MM-DD]
      70C_SOME_RANDOM_NUMBERS_AND_LETTERS_ED
uid                      daniel <daniel@test.com>
sub   cv25519 YYYY-MM-DD [E] [expires: YYYY-MM-DD]
```

You will need the public key for Rancher Desktop to work. You can find this in the output of the `gpg --generate-key` command, under the "pub" line. The public key in the sample above is: "70C_SOME_RANDOM_NUMBERS_AND_LETTERS_ED", but yours will be different.

## Initialize pass

To get the pub key into pass, we can run the following command:

```bash
pass init 70C_SOME_RANDOM_NUMBERS_AND_LETTERS_ED
```

the command should respond with something similar to:

```bash
Password store initialized for 70C_SOME_RANDOM_NUMBERS_AND_LETTERS_ED
```

For more details on pass, see its [website](https://www.passwordstore.org/).

## Setup Traefik

Rancher Desktop uses Traefik as a reverse proxy. We need to change our system config to enable it to handle ports properly. We can do this by editing the `/etc/sysctl.conf` file. Type the followign into a terminal:

```bash
sudo nano /etc/sysctl.conf
```

and add the following to the bottom of the file:

```bash
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=80
```

this will make the setting persist across reboots. We can check that it was set correctly by running:

```bash
sysctl net.ipv4.ip_unprivileged_port_start
```

it should respond with something similar to:

```bash
daniel@rddemo:~$ sysctl net.ipv4.ip_unprivileged_port_start
net.ipv4.ip_unprivileged_port_start = 80
```
