# Arcadas Server Environment

## OS installation

Ubuntu 18.04.2 LTS Server ([download](https://ubuntu.com/download/server)) \
Create USB installer on unix like sytems in CLI.

```sh
# List disks (search the USB drive name - sdX)
sudo run fdisk -l
# Change ISO path and X (without number)
sudo dd if=/path/to/downloaded/iso of=/dev/sdX
```

Boot from USB on the server. Istall Ubuntu Server with *OpenSSH, Docker* and *AWS*.

After first boot:

```sh
sudo apt-get update
```

## Must have apps

```sh
sudo apt-get -y install vim mc ufw samba git xclip nmap
```

## Install and config SSH

SSH key generation: [keygen](https://www.ssh.com/ssh/keygen/) \
Clipboard copy: [pbcopy/xclip](https://programmer.help/blogs/terminal-copy-and-paste-xclip-clip-xsel-and-pbcopy-linux-mac-and-ubuntu.html)

```sh
# Client - Copy public key
cat ~/.ssh/id_rsa.pub | pbcopy
```

```sh
# Server - Paste public key
vim ~/.ssh/authorized_keys
```

```sh
# Server - Check permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

```sh
# Client - Check connection by key
ssh <server_username>@<server_ip_address>
```

```sh
# Server - Disabling SSH Password Authentication
sudo vim /etc/ssh/sshd_config
# Search for the following
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM no
```

Generate SSH key on the server

```sh
# Server - Generate key
ssh-keygen -t rsa -b 4096 -C "<email@address.com>"
```

## Setup Github

```sh
# Client (Not on the server!) - Copy public key to clipboard (Change the IP!)
ssh 192.168.0.21 "cat ~/.ssh/id_rsa.pub" | pbcopy
```

Paste it into Github - Settings. Now you can pull this repo:

```sh
mkdir ~/github
cd ~/github
git clone git@github.com:arcadas/arcadas-server.git
# Are you sure you want to continue connecting (yes/no)? yes
```

## Bash aliases

Create backup and copy all files from bash to home folder and run:

```sh
cd
cp .bashrc .bashrc_original
cp github/arcadas-server/bash/* .
source .bashrc
```

Check all aliases before use them, please! \
You can disable all types of aliases in .bashrc.

## Setup Hosts

```sh
# Open Host file
sudo vim /etc/hosts
# Add the following
127.0.0.1  arcadas.com transmission.arcadas.com media.arcadas.com gitlab.arcadas.com cockpit.arcadas.com portainer.arcadas.com
```
